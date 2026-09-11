Aula 04 – REST: Respostas
Parte 1 – Café Newton

1. GET /cafes?page=1&size=5 → 200, envelope page/size/total/items com 5 itens (de 240).

2. GET /cafes?size=100000 → 400; o servidor limita o tamanho a no máximo 100.

3. GET /cafes/1 → 200, com headers ETag e Cache-Control.

4. GET /cafes/1 com If-None-Match: "cafe-1-v1" → 304, sem corpo (cliente já tem a versão).

5. POST /cafes válido → 201 com header Location apontando para o novo café.

6. POST /cafes sem nome → 400 com a lista de erros de validação.

7. DELETE /cafes/{id} duas vezes → 204 e depois 404. É idempotente porque o estado do servidor é o mesmo após 1 ou N chamadas (o recurso não existe); só a resposta muda.
Parte 2 – PetHouse: os 12 erros

01. Chamei POST /api/v1/getPets. A resposta mostrou: Lista os pets. Regra violada: Verbo na URI e método errado (POST para leitura). Redesenho: GET /api/v1/pets.

02. Chamei GET /api/v1/deletarPet?id=7. A resposta mostrou: O pet foi apagado. Regra violada: GET deve ser seguro (sem efeito colateral); verbo na URI. Redesenho: DELETE /api/v1/pets/7 → 204.

03. Chamei GET /api/v1/pet/{id}. A resposta mostrou: Rota no singular. Regra violada: Inconsistência: recursos no plural. Redesenho: GET /api/v1/pets/{id}.

04. Chamei GET /banhosTosa e /tutores_vip. A resposta mostrou: Grafias misturadas (camelCase, snake_case); 'vip' virou recurso. Regra violada: Padrão de nomes inconsistente (usar kebab-case); filtro tratado como recurso. Redesenho: GET /api/v1/banhos-tosa e GET /api/v1/tutores?vip=true.

05. Chamei POST /api/v1/pets. A resposta mostrou: Retorna 200 sem Location. Regra 
violada: Criação deve retornar 201 Created + Location. Redesenho: 201 + Location: /api/v1/pets/{id}.

06. Chamei GET /api/v1/pets/999999. A resposta mostrou: 200 com corpo dizendo 'não encontrado'. Regra violada: Status code errado; recurso inexistente é 404. Redesenho: 404 Not Found (corpo de erro padronizado).

07. Chamei GET /api/pets. A resposta mostrou: Sem versão e campos com nomes diferentes. Regra violada: Quebra de contrato sem versionamento; clientes antigos quebram. Redesenho: Manter /api/v1 e criar /api/v2 para mudanças.

08. Chamei GET /petshops/1/clientes/5/pets/9/consultas/12/exames/6. A resposta mostrou: Ids anteriores trocados por 99 dão a mesma resposta. Regra violada: Aninhamento excessivo (máx. 1 nível). Redesenho: GET /api/v1/exames/6 ou /api/v1/consultas/12/exames.

09. Chamei GET /api/v1/consultas. A resposta mostrou: Resposta enorme e lenta. Regra violada: Falta de paginação. Redesenho: GET /api/v1/consultas?page=1&size=20 (teto no servidor).

10. Chamei PUT /api/v1/pets/12/vacinas. A resposta mostrou: Cada chamada adiciona vacinas duplicadas. Regra violada: PUT deve ser idempotente. Redesenho: PUT substitui a lista; para adicionar usar POST /pets/12/vacinas.

11. Chamei POST /sessao + GET /meus-pets. A resposta mostrou: Após login do Bruno, Ana vê os pets do Bruno. Regra violada: Violação de stateless (sessão guardada no servidor). Redesenho: Token (ex.: JWT) em cada requisição via header Authorization.

12. Chamei GET /api/v1/tabela-de-precos. A resposta mostrou: Sem ETag/Cache-Control. Regra violada: Falta de cache em dado que muda raramente. Redesenho: Cache-Control: max-age longo + ETag (304).