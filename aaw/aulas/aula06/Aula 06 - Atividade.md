# Atividade — AULA 06

## Síncrono ou Assíncrono?

*Análise de fluxos de comunicação entre serviços — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

Vocês são os arquitetos dos 4 fluxos abaixo. Para CADA cenário:

- Decidam o estilo de comunicação: síncrono (request/response), assíncrono (fila/evento) ou API Gateway/BFF
- Desenhem o fluxo com caixas (serviços) e setas (chamadas/mensagens) no espaço indicado
- Justifiquem com pelo menos 2 fatores (urgência da resposta, tolerância a atraso, picos, falhas...)
- Apontem o principal risco da escolha de vocês

*⏱️ Tempo: 25 minutos  |  👥 Formato: em duplas  |  Não existe resposta única — o que vale é a justificativa.*

> **Nomes:** ____________________   **Turma:** ____________________   **Data:** ___ / ___ / ______

## CENÁRIO 01 — PagFácil — aprovar ou negar AGORA

No checkout do PagFácil, ao clicar em “Pagar”, o serviço de Pagamentos precisa consultar o saldo/limite do cliente no serviço de Contas — e a resposta define se a venda acontece neste exato momento.

- O cliente está na tela, esperando o resultado da compra
- Sem a resposta de Contas, não há decisão possível: aprovar às cegas é proibido
- Tempo de resposta do serviço de Contas: ~80 ms em condições normais

**Sua análise:**

1. Estilo recomendado:   ☐ Síncrono

2. Desenhe o fluxo (caixas = serviços, setas = chamadas/mensagens): Cliente → Pagamentos → Contas → Pagamentos → Cliente

3. Justificativa (mínimo 2 fatores): A resposta é necessária na hora para aprovar ou negar a compra; o serviço de Contas normalmente responde rápido.

4. Principal risco da escolha: Se o serviço de Contas cair ou ficar lento, o pagamento também será afetado.

## CENÁRIO 02 — CadastraJá — o e-mail de boas-vindas

Após criar a conta no CadastraJá, o sistema envia um e-mail de boas-vindas. O provedor de e-mail às vezes demora 8 segundos para responder e falha em 2% das tentativas.

- O usuário quer começar a usar o app imediatamente após o cadastro
- O e-mail chegar 1 minuto depois não incomoda ninguém
- Se o provedor falhar, o envio deve ser tentado de novo — sem o usuário perceber

**Sua análise:**

1. Estilo recomendado:   assíncrono

2. Desenhe o fluxo (caixas = serviços, setas = chamadas/mensagens): Cadastro → Fila → Serviço de E-mail → Provedor

3. Justificativa (mínimo 2 fatores): O usuário não precisa esperar o e-mail; a fila permite novas tentativas em caso de falha.

4. Principal risco da escolha:  O e-mail pode atrasar ou ser enviado mais de uma vez.

## CENÁRIO 03 — MegaMarket — baixa de estoque nos picos

No marketplace MegaMarket, cada venda gera uma baixa no serviço de Estoque. Nas grandes promoções o tráfego sobe 10x e o Estoque não dá conta de responder na velocidade das vendas.

- Atraso de alguns segundos na baixa é aceitável
- PERDER uma baixa de estoque não é aceitável (gera venda sem produto)
- O checkout não pode ficar lento nem cair porque o Estoque está sobrecarregado

**Sua análise:**

1. Estilo recomendado:  ☐ Assíncrono

2. Desenhe o fluxo (caixas = serviços, setas = chamadas/mensagens): Checkout → Fila/Eventos → Estoque

3. Justificativa (mínimo 2 fatores): A fila absorve picos de tráfego e evita deixar o checkout lento. A baixa pode acontecer alguns segundos depois.

4. Principal risco da escolha: Mensagens duplicadas ou acúmulo da fila podem causar inconsistências.

## CENÁRIO 04 — AppBanco — uma tela, cinco serviços

A tela inicial do AppBanco mostra saldo, fatura do cartão, investimentos, empréstimos e cashback — dados de 5 serviços diferentes. O time mobile reclama: são 5 chamadas, 5 formatos de resposta e 5 pontos de falha em cada abertura do app.

- A tela precisa abrir rápido, inclusive em redes móveis ruins
- Cada serviço tem equipe, formato e autenticação próprios
- Amanhã nasce a versão web, que precisa de MAIS dados que a mobile

**Sua análise:**

1. Estilo recomendado:  ☐ API Gateway/BFF

2. Desenhe o fluxo (caixas = serviços, setas = chamadas/mensagens): App → BFF → Saldo / Cartão / Investimentos / Empréstimos / Cashback

3. Justificativa (mínimo 2 fatores): O app faz apenas uma chamada e recebe uma resposta já organizada. Mobile e web podem ter BFFs diferentes.

4. Principal risco da escolha: O BFF pode virar um ponto central de falha ou gargalo.

## DESAFIO

1. Escolha um cenário em que vocês indicaram ASSÍNCRONO. Os brokers de mensagens costumam garantir entrega “pelo menos uma vez” — ou seja, a MESMA mensagem pode chegar duas vezes. O que aconteceria no seu fluxo? Como o consumidor deveria se proteger?
No MegaMarket, uma mensagem duplicada poderia baixar o estoque duas vezes.

O consumidor deve usar idempotência, identificando cada venda por um ID único e ignorando mensagens já processadas.