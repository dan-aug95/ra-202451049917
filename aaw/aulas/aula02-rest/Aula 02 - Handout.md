# HANDOUT — AULA 02

## Dissecando o HTTP

*6 requisições sob o microscópio — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

Vocês interceptaram 6 conversas entre um app e a API de uma biblioteca. Para CADA card:

- Descrevam o que o cliente pediu (verbo + recurso na URI)
- Expliquem o que o status code da resposta informa
- Respondam: repetindo a MESMA requisição 3 vezes seguidas, o estado do servidor muda?

Ao final, preencham juntos a TABELA-SÍNTESE dos verbos na última página.

*⏱️ Tempo: 30 minutos  |  👥 Formato: em duplas  |  Dica: o card 6 esconde uma pegadinha de quem é a culpa.*

> **Nomes:** ____________________   **Turma:** ____________________   **Data:** ___ / ___ / ______

## REQUISIÇÃO 01 — A prateleira inteira

```text
→ REQUISIÇÃO
GET /api/livros HTTP/1.1
Host: biblioteca.newton.br
Accept: application/json
```

```text
← RESPOSTA
HTTP/1.1 200 OK
Content-Type: application/json

[ { "id": 1, "titulo": "Clean Code", "autor": "Robert C. Martin" },
  { "id": 7, "titulo": "O Programador Pragmático", "autor": "Hunt & Thomas" } ]
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)? Ele pediu uma consulta GET da lista de livros.

2. O que o status code informa? Deu certo? Culpa de quem se não deu? informa o codigo 200 OK informa que a requisição deu certo. Se fosse um erro quatrocentos seia do cliente e quinhentos do servidor.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta? não muda nada no servidor porque o GET é apenas uma leitura; A resposta tende a ser a mesma se os dados nao forem alterados.

## REQUISIÇÃO 02 — O livro fantasma

```text
→ REQUISIÇÃO
GET /api/livros/99 HTTP/1.1
Host: biblioteca.newton.br
Accept: application/json
```

```text
← RESPOSTA
HTTP/1.1 404 Not Found
Content-Type: application/problem+json

{ "title": "Not Found", "status": 404 }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)? O cliente pediu para consultar o livro de ID 99.

2. O que o status code informa? Deu certo? Culpa de quem se não deu? O 404 not found informa que o recurso nao foi encontrado. Arequisição não foi atendida; o problema esta no cliente pois o livro não existe.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta? O estado do servidor não muda. As tres requisiçoes devem retornar 404, enquanto o livro continuar inexistente.

## REQUISIÇÃO 03 — Livro novo na estante

```text
→ REQUISIÇÃO
POST /api/livros HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "titulo": "Domain-Driven Design", "autor": "Eric Evans" }
```

```text
← RESPOSTA
HTTP/1.1 201 Created
Location: /api/livros/8
Content-Type: application/json

{ "id": 8, "titulo": "Domain-Driven Design", "autor": "Eric Evans" }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)? POST para cadastrar um novo livro.
        
2. O que o status code informa? Deu certo? Culpa de quem se não deu? 201 Created significa cadastro realizado com sucesso.

3. Enviando este POST 3 vezes seguidas, o que acontece na estante? Para que serve o header Location? cada POST cria um novo livro. O location indica onde o novo recurso foi criado /api/livros/8.

## REQUISIÇÃO 04 — Corrigindo a ficha completa

```text
→ REQUISIÇÃO
PUT /api/livros/7 HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "id": 7, "titulo": "O Programador Pragmático", "autor": "D. Hunt; D. Thomas" }
```

```text
← RESPOSTA
HTTP/1.1 200 OK
Content-Type: application/json

{ "id": 7, "titulo": "O Programador Pragmático", "autor": "D. Hunt; D. Thomas" }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)? PUT para atualizar completamente o livro 7.

2. O que o status code informa? Deu certo? Culpa de quem se não deu? 200 OK atualização realizada com sucesso.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta? O estado fica igual após as três requisiçoes. A resposta tambem permanece igual.

## REQUISIÇÃO 05 — Fora do catálogo

```text
→ REQUISIÇÃO
DELETE /api/livros/7 HTTP/1.1
Host: biblioteca.newton.br
```

```text
← RESPOSTA
HTTP/1.1 204 No Content
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)? DELETE para excluir o livro 7.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?204 no content: exclusão realizada com sucesso, sem conteudo na resposta.

3. Repetindo o DELETE, o estado do servidor muda? Que resposta você ESPERA na segunda vez? Na segunda vez, o livro já não existe. esperado é 404 not found.

## REQUISIÇÃO 06 — O cadastro capenga

```text
→ REQUISIÇÃO
POST /api/livros HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "autor": "Anônimo" }
```

```text
← RESPOSTA
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{ "title": "Bad Request", "status": 400,
  "errors": { "Titulo": [ "O campo Titulo é obrigatório" ] } }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)? POST para cadastrar um livro

2. O que o status code informa? Deu certo? Culpa de quem se não deu?400 bad request: dados invalidos e ou incompletos

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta? O servidor não é alterado. As três tentativas devem retornar 400.

## TABELA-SÍNTESE — Os verbos do HTTP

*Preencham com base nos 6 cards. “Seguro” = não altera nada no servidor. “Idempotente” = repetir N vezes deixa o servidor no mesmo estado que 1 vez.*

| **Verbo** | **Para que serve** | **Seguro?** | **Idempotente?** | **Status típicos** |
| --- | --- | --- | --- | --- |
| **`GET`** |  |  |  |  |
| **`POST`** |  |  |  |  |
| **`PUT`** |  |  |  |  |
| **`PATCH`** |  |  |  |  |
| **`DELETE`** |  |  |  |  |

## DESAFIO

1. O verbo PATCH não apareceu em nenhum card. Qual a diferença entre PATCH e PUT? Um app de banco quer alterar SÓ o apelido do usuário, entre dezenas de campos do perfil — qual dos dois você usaria e por quê?
PUT substitui o recurso inteiro. PATCH altera apenas parte dele.

Para alterar só o apelido, usaria PATCH, pois não é necessário enviar todos os campos do perfil.