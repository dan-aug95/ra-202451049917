# HANDOUT — AULA 05

## Escolha o Banco

*Persistência em arquiteturas distribuídas — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

Vocês são o time de arquitetura de dados contratado pelas 4 empresas abaixo. Para CADA cenário:

- Escolham o modelo de banco: relacional, documento, chave-valor ou grafo
- Justifiquem com pelo menos 2 fatores do contexto (estrutura dos dados, padrão de acesso, escala, consistência...)
- Apontem o principal risco da escolha de vocês

*⏱️ Tempo: 25 minutos  |  👥 Formato: em duplas  |  Não existe resposta única — o que vale é a justificativa.*

> **Nomes:** ____________________   **Turma:** ____________________   **Data:** ___ / ___ / ______

## CENÁRIO 01 — TechStore — o catálogo camaleão

E-commerce com 80 mil produtos. Cada categoria tem atributos completamente diferentes: livro tem autor e número de páginas; notebook tem RAM e CPU; camiseta tem tamanho e cor.

- A cada categoria nova, o time faz ALTER TABLE e a tabela produtos já tem 92 colunas (a maioria NULL)
- O produto é quase sempre lido INTEIRO, de uma vez, para montar a página
- Novos atributos surgem toda semana — o marketing não espera o DBA
- Relatórios cruzando categorias são raros

**Sua análise:**

1. Modelo recomendado:  Documento

2. Justificativa (mínimo 2 fatores do contexto): Estrutura flexível, com atributos diferentes por categoria; permite adicionar novos campos sem alterar tabelas. O produto pode ser lido inteiro de uma vez.

3. Principal risco da escolha: Consultas e relatórios complexos entre diferentes categorias podem ser mais difíceis.

## CENÁRIO 02 — MegaCart — o carrinho da Black Friday

Serviço de carrinho de compras de um varejista gigante. Na Black Friday são milhões de leituras e escritas por minuto.

- O acesso é SEMPRE pela chave: “carrinho do cliente 12345” — nunca por busca ou filtro
- Todo carrinho expira automaticamente em 48h (TTL)
- Latência precisa ser de poucos milissegundos
- Perder um carrinho é chato, mas NÃO é tragédia — o cliente remonta

**Sua análise:**

1. Modelo recomendado:  Chave-valor

2. Justificativa (mínimo 2 fatores do contexto): Acesso sempre pela chave do cliente e exige baixa latência. Possui suporte a TTL, facilitando a expiração em 48h.

3. Principal risco da escolha:  Perda ou indisponibilidade dos dados do carrinho.

## CENÁRIO 03 — PayBank — dinheiro não pode evaporar

Módulo de transferências de um banco. Uma transferência debita uma conta e credita outra — as duas operações têm que acontecer JUNTAS ou nenhuma acontece.

- Consistência forte exigida por lei — saldo errado é multa do Banco Central
- Auditoria cruza contas, clientes, agências e transações em relatórios complexos (joins)
- O esquema dos dados é estável há 10 anos
- Volume alto, mas previsível

**Sua análise:**

1. Modelo recomendado:   Relacional  

2. Justificativa (mínimo 2 fatores do contexto): Necessita de consistência forte e transações ACID. O esquema é estável e os relatórios precisam de joins complexos.

3. Principal risco da escolha: Maior dificuldade de escalar horizontalmente.

## CENÁRIO 04 — FriendLink — amigos dos seus amigos

Rede social profissional em que o produto principal é a indicação: “pessoas que você talvez conheça” e “quem pode te apresentar à empresa X”.

- As consultas dominantes percorrem RELACIONAMENTOS: amigos dos amigos, caminhos de indicação com até 6 níveis
- Em banco relacional, cada nível vira um self-join — com 6 níveis a consulta já não responde
- Os dados de perfil são simples; o valor está nas CONEXÕES
- O grafo cresce milhões de arestas por dia

**Sua análise:**

1. Modelo recomendado: Grafo

2. Justificativa (mínimo 2 fatores do contexto): O sistema trabalha principalmente com conexões e caminhos entre pessoas. Consultas de vários níveis são mais eficientes em grafos do que com vários self-joins.

3. Principal risco da escolha: Crescimento muito grande do grafo pode aumentar o custo de armazenamento e processamento.

## DESAFIO

1. Escolha um dos cenários e responda: se a rede particionar (metade dos servidores não enxerga a outra metade), o que o sistema deve fazer — parar de responder para não errar, ou continuar respondendo mesmo arriscando dados desatualizados? Qual letra do CAP vocês sacrificariam e por quê?
escolheria parar de responder durante a partição para evitar dados incorretos.

CAP: sacrificaria a Disponibilidade (A) para manter Consistência (C), pois em um banco é mais importante não apresentar ou realizar operações com saldo incorreto.
