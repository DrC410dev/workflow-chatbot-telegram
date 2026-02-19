# Bot de Clima – DrC410

Este projeto é um workflow do **n8n** que implementa um **chatbot de clima no Telegram**, utilizando a API do **OpenWeatherMap** para buscar a temperatura atual de uma cidade informada pelo usuário.

---

## Visão Geral

Fluxo básico:

1. O usuário envia uma mensagem para o bot no Telegram com o nome da cidade (ex.: `Maceió, BR`).
2. O n8n recebe a mensagem via **Telegram Trigger**.
3. O texto da mensagem é tratado e usado para chamar a API do **OpenWeatherMap**.
4. Se a cidade é encontrada, o bot responde com a temperatura atual.
5. Se houver erro (cidade não encontrada ou problema na requisição), o bot retorna uma mensagem de erro orientando o formato correto.

---

## Tecnologias Utilizadas

- **n8n** (Automação de workflows)
- **Telegram Bot API**
- **OpenWeatherMap API**
- Node: **HTTP Request**, **Set**, **Telegram Trigger**, **Telegram (Send Message)**

---

## Pré-requisitos

1. **Conta no Telegram** e um **bot criado** via `@BotFather`:
   - Anote o **bot token**.
2. **Conta no OpenWeatherMap**:
   - Crie uma API Key (chave de acesso).
3. Instância do **n8n** rodando (local ou servidor):
   - Com acesso HTTPS/publicamente acessível ou tunelado, para o Telegram alcançar o webhook.

---

## Estrutura do Workflow

O workflow é definido no arquivo `workflowtelegramchatbotjson.json` e contém os seguintes nós principais:

### 1. Telegram Trigger (`Telegram Trigger`)

- Tipo: `telegramTrigger`
- Função: Recebe as mensagens enviadas ao bot.
- Configuração:
  - `updates`: `message`
  - Credenciais: `bot clima` (token configurado no n8n)

### 2. Set – Captura da Mensagem (`pega a msg`)

- Tipo: `set`
- Função: Extrai e prepara dados da mensagem recebida.
- Campos definidos:
  - `queue`: texto da mensagem do usuário (`$json.message.text`)
  - `expressao_tratada`: normaliza o texto removendo acentos e trocando espaços por `%`
  - `appid`: chave fixa da API do OpenWeatherMap
  - `teste`: campo de teste `"Rio de Janeiro, BR"` (não utilizado na requisição final)

> Observação: atualmente a URL usa `{{$json.queue}}`, a normalização (`expressao_tratada`) está disponível caso você queira usar para tratar melhor a cidade na query.

### 3. HTTP Request – Chamada à API do Clima (`HTTP Request`)

- Tipo: `httpRequest`
- Função: Consulta a API do OpenWeatherMap.
- Método: `GET`
- URL:

