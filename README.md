# Cadastro de pedidos — passo a passo simples

Neste exercício, vamos adicionar uma nova página ao projeto **Portal de Pedidos**.

Essa página permitirá:

1. preencher os dados de um pedido;
2. enviar os dados para a API;
3. guardar o novo pedido no arquivo `dados/pedidos.json`;
4. consultar o pedido pelo portal.

Vamos trabalhar com três arquivos:

```text
frontend/cadastrar.html
frontend/cadastrar.js
server.mjs
```

---

# 1. Criar o HTML

Dentro da pasta:

```text
Portal_de_Pedidos_Alunos/frontend
```

crie o arquivo:

```text
cadastrar.html
```

Cole o código completo:

```html
<!doctype html>
<html lang="pt">
  <head>
    <meta charset="UTF-8" />

    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0"
    />

    <title>Cadastrar pedido</title>

    <link rel="stylesheet" href="styles.css" />
  </head>

  <body>
    <header class="topbar">
      <div class="brand">
        ALUN <span>BUSINESS</span>
      </div>

      <a href="/">Voltar ao portal</a>
    </header>

    <main class="shell">
      <section class="hero">
        <p class="eyebrow">PORTAL DE PEDIDOS</p>

        <h1>Cadastrar um novo pedido</h1>

        <p>
          Preencha os dados necessários para criar um pedido.
        </p>
      </section>

      <section class="card">
        <p class="step">CADASTRO</p>

        <h2>Dados do pedido</h2>

        <form id="pedidoForm">
          <div>
            <label for="numero">
              Número do pedido
            </label>

            <input
              id="numero"
              name="numero"
              type="text"
              inputmode="numeric"
              placeholder="Ex.: 2001"
              required
            />
          </div>

          <div>
            <label for="titular">
              Nome do titular
            </label>

            <input
              id="titular"
              name="titular"
              type="text"
              placeholder="Nome completo"
              required
            />
          </div>

          <div>
            <label for="tipo">
              Tipo do pedido
            </label>

            <select
              id="tipo"
              name="tipo"
              required
            >
              <option value="">Selecione</option>
              <option value="Pedido de benefício">Pedido de benefício</option>
              <option value="Atualização de dados">Atualização de dados</option>
              <option value="Pedido de declaração">Pedido de declaração</option>
            </select>
          </div>

          <div>
            <label for="estado">
              Estado inicial
            </label>

            <select
              id="estado"
              name="estado"
              required
            >
              <option value="">Selecione</option>
              <option value="Registado">Registado</option>
              <option value="Em análise">Em análise</option>
              <option value="Aguardando documento">Aguardando documento</option>
            </select>
          </div>

          <div>
            <label for="dataRegisto">
              Data de registo
            </label>

            <input
              id="dataRegisto"
              name="dataRegisto"
              type="date"
              required
            />
          </div>

          <div>
            <label for="delegacao">
              Delegação
            </label>

            <select
              id="delegacao"
              name="delegacao"
              required
            >
              <option value="">Selecione</option>
              <option value="Cidade de Maputo">Cidade de Maputo</option>
              <option value="Matola">Matola</option>
              <option value="Beira">Beira</option>
              <option value="Nampula">Nampula</option>
            </select>
          </div>

          <div>
            <label for="responsavel">
              Responsável pelo pedido
            </label>

            <select
              id="responsavel"
              name="responsavel"
              required
            >
              <option value="">Selecione</option>
              <option value="celina">Celina</option>
              <option value="paulo">Paulo</option>
            </select>
          </div>

          <button
            class="primary"
            type="submit"
          >
            Cadastrar pedido
          </button>
        </form>

        <div
          id="resultMessage"
          class="message hidden"
          role="status"
        ></div>
      </section>
    </main>

    <footer>
      Projeto fictício para fins exclusivamente didáticos.
    </footer>

    <script src="cadastrar.js"></script>
  </body>
</html>
```

## Explicação simples do HTML

O HTML cria tudo o que aparece na página.

### `head`

```html
<head>
```

Configura a página, o título da aba e o arquivo de estilos.

```html
<link rel="stylesheet" href="styles.css" />
```

Essa linha utiliza a aparência que já existe no portal.

### `header`

```html
<header class="topbar">
```

Cria a barra superior com o nome da empresa e o link para voltar ao portal.

### `form`

```html
<form id="pedidoForm">
```

Agrupa todos os campos do cadastro.

O identificador `pedidoForm` será utilizado pelo JavaScript.

### `label`

```html
<label for="numero">Número do pedido</label>
```

Mostra o nome do campo.

### `input`

```html
<input id="numero" type="text" required />
```

Permite digitar uma informação.

`required` significa que o campo é obrigatório.

### `select`

```html
<select id="estado">
```

Apresenta uma lista de opções prontas.

### Botão

```html
<button type="submit">
```

Envia o formulário.

### Mensagem

```html
<div id="resultMessage"></div>
```

Será utilizada para apresentar sucesso ou erro.

### JavaScript

```html
<script src="cadastrar.js"></script>
```

Liga o arquivo HTML ao JavaScript do cadastro.

---

# 2. Criar o JavaScript

Dentro da pasta:

```text
Portal_de_Pedidos_Alunos/frontend
```

crie o arquivo:

```text
cadastrar.js
```

Cole o código completo:

```javascript
const pedidoForm = document.querySelector("#pedidoForm");
const resultMessage = document.querySelector("#resultMessage");

function mostrarMensagem(elemento, texto, sucesso = false) {
  elemento.textContent = texto;
  elemento.classList.remove("hidden", "success");

  if (sucesso) {
    elemento.classList.add("success");
  }
}

pedidoForm.addEventListener("submit", async (event) => {
  event.preventDefault();

  const pedido = {
    numero: document.querySelector("#numero").value.trim(),
    titular: document.querySelector("#titular").value.trim(),
    tipo: document.querySelector("#tipo").value,
    estado: document.querySelector("#estado").value,
    dataRegisto: document.querySelector("#dataRegisto").value,
    delegacao: document.querySelector("#delegacao").value,
    responsavel: document.querySelector("#responsavel").value
  };

  mostrarMensagem(
    resultMessage,
    "A cadastrar o pedido..."
  );

  try {
    const response = await fetch("/api/pedidos", {
      method: "POST",

      headers: {
        "Content-Type": "application/json"
      },

      body: JSON.stringify(pedido)
    });

    const body = await response.json();

    if (!response.ok) {
      mostrarMensagem(
        resultMessage,
        body.erro || "Não foi possível cadastrar o pedido."
      );

      return;
    }

    mostrarMensagem(
      resultMessage,
      `Pedido ${body.pedido.numero} cadastrado com sucesso.`,
      true
    );

    pedidoForm.reset();
  } catch (error) {
    mostrarMensagem(
      resultMessage,
      "Não foi possível comunicar com o servidor."
    );

    console.error(error);
  }
});
```

## Explicação geral do JavaScript

O JavaScript faz quatro coisas:

1. espera a pessoa clicar em **Cadastrar pedido**;
2. lê os valores preenchidos no formulário;
3. envia os valores para `POST /api/pedidos`;
4. apresenta uma mensagem de sucesso ou erro.

Esta linha impede que a página seja recarregada:

```javascript
event.preventDefault();
```

Este objeto reúne os dados preenchidos:

```javascript
const pedido = {
```

Esta parte envia os dados para a API:

```javascript
fetch("/api/pedidos", {
  method: "POST"
})
```

`JSON.stringify(pedido)` transforma o objeto em JSON antes do envio.

---

# 3. Adicionar o cadastro ao servidor

Abra o arquivo:

```text
Portal_de_Pedidos_Alunos/server.mjs
```

Dentro da função:

```javascript
async function apiHandler(req, res, url) {
```

procure esta linha:

```javascript
const match = url.pathname.match(
  /^\/api\/pedidos\/([^/]+)(?:\/(historico|anexos))?$/
);
```

O novo código deve ser colocado **imediatamente antes** dessa linha.

Cole:

```javascript
if (
  req.method === "POST" &&
  url.pathname === "/api/pedidos"
) {
  const body = await readBody(req);

  const numero = String(body.numero || "").trim();
  const titular = String(body.titular || "").trim();
  const tipo = String(body.tipo || "").trim();
  const estado = String(body.estado || "").trim();
  const dataRegisto = String(body.dataRegisto || "").trim();
  const delegacao = String(body.delegacao || "").trim();
  const responsavel = String(body.responsavel || "").trim();

  if (
    !numero ||
    !titular ||
    !tipo ||
    !estado ||
    !dataRegisto ||
    !delegacao ||
    !responsavel
  ) {
    return sendJson(res, 400, {
      erro: "Preencha todos os campos obrigatórios."
    });
  }

  if (!/^\d+$/.test(numero)) {
    return sendJson(res, 400, {
      erro: "O número do pedido deve conter somente números."
    });
  }

  const pedidos = JSON.parse(
    await fs.readFile(pedidosPath, "utf8")
  );

  const pedidoExistente = pedidos.find(
    (pedido) => pedido.numero === numero
  );

  if (pedidoExistente) {
    return sendJson(res, 409, {
      erro: "Já existe um pedido com esse número."
    });
  }

  const novoPedido = {
    numero,
    titular,
    tipo,
    estado,
    dataRegisto,
    delegacao,
    responsavel,

    historico: [
      {
        data: dataRegisto,
        evento: "Pedido registado"
      }
    ]
  };

  pedidos.push(novoPedido);

  await fs.writeFile(
    pedidosPath,
    JSON.stringify(pedidos, null, 2),
    "utf8"
  );

  console.log(
    `Pedido ${numero} gravado em: ${pedidosPath}`
  );

  return sendJson(res, 201, {
    mensagem: "Pedido cadastrado com sucesso.",
    pedido: publicPedido(novoPedido)
  });
}
```

## Explicação simples do servidor

O novo código cria a operação:

```text
POST /api/pedidos
```

Essa operação:

1. recebe os dados enviados pelo JavaScript;
2. verifica se os campos foram preenchidos;
3. verifica se o número possui apenas números;
4. abre o arquivo `dados/pedidos.json`;
5. verifica se o número já existe;
6. adiciona o novo pedido;
7. grava novamente o arquivo;
8. devolve o código `201`.

O cadastro será guardado em:

```text
Portal_de_Pedidos_Alunos/dados/pedidos.json
```

---

# 4. Reiniciar o servidor

Depois de alterar o `server.mjs`, pare o servidor:

```text
Ctrl + C
```

Inicie novamente:

```bash
npm start
```

---

# 5. Testar

Abra:

```text
http://localhost:3100/cadastrar.html
```

Preencha os campos utilizando um número novo, por exemplo:

```text
5001
```

Clique em:

```text
Cadastrar pedido
```

O resultado esperado é:

```text
Pedido 5001 cadastrado com sucesso.
```

O terminal também mostrará o caminho do arquivo atualizado.

Depois, abra o portal:

```text
http://localhost:3100
```

Entre com o perfil escolhido como responsável e consulte o novo número.

