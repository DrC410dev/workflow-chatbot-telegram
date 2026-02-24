# Chatbot de Clima no Telegram com N8N

Bot automatizado que informa a temperatura atual de cidades brasileiras através do Telegram, utilizando **N8N** e a API gratuita do **OpenWeather**.

> Importante: se você está reutilizando este projeto a partir de um fork, **gere uma nova API key no painel da OpenWeather** e configure-a via credenciais no N8N. Não use nenhuma chave que já tenha sido exposta anteriormente.

---

## 📋 Funcionalidades

- Recebe mensagens no formato `Cidade,UF` (ex.: `São Paulo,SP`).
- Normaliza a entrada (remove acentos, espaços extras, converte para minúsculas).
- Consulta **temperatura atual** via API OpenWeather.
- Retorna mensagem formatada com:
  - Temperatura arredondada (°C).
  - Condição climática (nublado, ensolarado, etc.).
  - Sensação térmica.
- Tratamento de erros com mensagem clara para o usuário.
- Validação do retorno da API (confere se temperatura e nome da cidade realmente existem antes de responder).

---

## 📁 Arquivos do Repositório

- `workflow-chatbot-telegram.json` – Workflow exportado do N8N.
- `README.md` – Documentação completa do projeto.

> Atenção: confirme que o nome do arquivo JSON no repositório **é exatamente** `workflow-chatbot-telegram.json`.  
> Se você exportar um novo workflow com outro nome, lembre-se de **atualizar este README**.

---

## 🔧 Pré-requisitos

- **N8N** instalado (local, Docker ou cloud).
- Conta **Telegram** e bot criado via **@BotFather**.
- Conta **OpenWeather** com **API key ativa**.

---

## 🔐 Variáveis e Credenciais Necessárias

Toda autenticação é feita via **Credenciais do N8N** (nenhuma chave fica hardcoded no workflow).

No N8N, configure:

### 1. Credencial Telegram – Bot Token

- Nome sugerido: `Telegram Bot API`
- Tipo: **Telegram**
- Campo:
  - `Access Token`: Token fornecido pelo **@BotFather** (formato `123456789:ABCdefGHI...`)

### 2. Credencial OpenWeather – API Key

Você pode usar uma credencial de **HTTP Header Auth** ou outra credencial que use a chave como variável segura.

- Nome sugerido: `OpenWeather API`
- Tipo: **HTTP Header Auth** (exemplo mais comum)
- Configuração típica:
  - `Header Name`: `appid`
  - `Header Value`: sua API key gerada no painel da OpenWeather.

> ⚠️ **Segurança**
>
> - Nunca exponha sua API key em campos de texto fixos no workflow.
> - Use sempre credenciais nativas do N8N ou variáveis de ambiente.
> - Caso alguma chave tenha sido colocada diretamente no fluxo, **revogue-a no painel da OpenWeather** e gere uma nova antes de usar em produção.

---

## 📥 Como Importar o Workflow no N8N (Passo a Passo)

### Passo 1: Importar o arquivo JSON

1. Abra o N8N.
2. No topo (ou menu principal), clique em **Import** / **Import from File**.
3. Selecione o arquivo `workflow-chatbot-telegram.json` deste repositório.
4. Clique em **Import**.
5. O workflow aparecerá na sua lista com os nós já estruturados.

### Passo 2: Configurar as credenciais

Após a importação, alguns nós vão mostrar avisos de credenciais ausentes.

**Telegram:**

1. No N8N, vá em **Credentials** (menu lateral).
2. Clique em **Add Credential** → escolha **Telegram**.
3. Configure:
   - **Name**: `Telegram Bot API` (ou outro nome que preferir).
   - **Access Token**: cole o token do **@BotFather**.
4. Salve.

**OpenWeather:**

1. Ainda em **Credentials**, clique em **Add Credential**.
2. Escolha **HTTP Header Auth** (ou o tipo que estiver usando no seu workflow).
3. Configure:
   - **Name**: `OpenWeather API`.
   - **Header Name**: `appid`.
   - **Header Value**: sua API key da OpenWeather.
4. Salve.

### Passo 3: Associar as credenciais aos nós

1. Abra o workflow importado.
2. Clique, um a um, nos nós que exibem o aviso de credencial:
   - `Telegram Trigger`
   - `HTTP Request - OpenWeather`
   - `Telegram Send - Success`
   - `Telegram Send - Error`
3. Em cada nó:
   - Vá até a seção **Credentials**.
   - Selecione:
     - `Telegram Bot API` para os nós de Telegram.
     - `OpenWeather API` para o nó de HTTP Request.
4. Salve o workflow.

### Passo 4: Ativar o workflow

1. No canto superior direito do editor de workflow, ative o toggle para **Active**.
2. Seu fluxo de chatbot de clima está pronto para receber mensagens do Telegram.

---

## 🤖 Como Criar o Bot no Telegram

1. Abra o Telegram e procure por **@BotFather**.
2. Envie o comando: `/newbot`.
3. Escolha um **nome** para o bot (ex.: `Meu Bot de Clima`).
4. Escolha um **username** terminando em `bot` (ex.: `meu_clima_bot`).
5. O BotFather irá retornar um **token de acesso** (ex.: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`).
6. Guarde esse token em local seguro e configure-o no N8N, na credencial **Telegram Bot API**.

---

## 🌤️ Como Obter a API Key da OpenWeather

1. Acesse o site da **OpenWeather** e clique em **Sign Up** para criar sua conta.
2. Confirme o e-mail de cadastro.
3. Acesse a seção **API Keys** no painel da OpenWeather.
4. Copie a **Default API Key** ou crie uma nova.
5. Aguarde alguns minutos até que a key esteja ativa.
6. Configure essa chave na credencial `OpenWeather API` do N8N.

---

## ✅ Como Testar o Chatbot

### Teste de Sucesso

1. Abra o Telegram.
2. Procure seu bot pelo username (ex.: `@meu_clima_bot`).
3. Envie mensagens de teste, no formato `Cidade,UF`:
   - `São Paulo,SP`
   - `Belo Horizonte,MG`
   - `Rio de Janeiro,RJ`
4. Resposta esperada (exemplo):

   > 🌤️ A temperatura em São Paulo é de 22°C.  
   > ☁️ Condição: céu limpo.  
   > 🌡️ Sensação térmica: 21°C.

### Teste de Erro

1. Envie algo como:

text CidadeInexistente,ZZ

2. Resposta esperada (exemplo):

   > ❌ Cidade não encontrada. Use o formato Cidade,UF (ex.: São Paulo,SP).  
   > Dica: incluir também a sigla do país ajuda a deixar a busca mais precisa (ex.: `São Paulo,SP,BR`).

---

## 🔍 Estrutura do Workflow

Fluxo resumido:

1. **Telegram Trigger**  
   Recebe a mensagem do usuário (texto + chat_id).

2. **Set Initial Data**  
   Captura `chat_id` e o texto enviado.

3. **Normalize and Create Queue**  
   - Remove acentos.
   - Remove espaços extras no início e fim.
   - Converte para minúsculas.
   - Cria o campo `queue` para ser usado na busca.

4. **HTTP Request – OpenWeather**  
   - Consulta a API de clima.
   - Usa unidade métrica (°C) e idioma `pt_br`.

5. **IF – Validate Response**  
   Valida se:
   - Código HTTP da resposta é `200`.
   - O objeto `main` existe.
   - O campo `main.temp` existe.
   - O campo `name` (nome da cidade) existe.

   - **Se a validação for verdadeira (TRUE)** → segue para **Build Success Message**.
   - **Se a validação for falsa (FALSE)** → segue para **Telegram Send – Error**.

6. **Build Success Message**  
   Monta uma mensagem amigável com:
   - Temperatura arredondada.
   - Sensação térmica.
   - Descrição da condição do tempo.

7. **Telegram Send – Success**  
   Envia a mensagem de sucesso formatada para o usuário.

8. **Telegram Send – Error**  
   Envia mensagem de erro clara, orientando o formato correto da consulta.

---

## 📝 Validações Implementadas

O workflow valida:

- ✅ Status HTTP `200`.
- ✅ Existência do objeto `main`.
- ✅ Existência do campo `main.temp`.
- ✅ Existência do campo `name` (nome da cidade).

Se qualquer validação falhar, o fluxo envia a mensagem de erro, em vez de tentar montar uma resposta incompleta.

---

## 🛡️ Segurança

- ✅ Nenhuma credencial exposta no arquivo JSON do workflow.
- ✅ Uso de **Credenciais Nativas do N8N** para Telegram e OpenWeather.
- ✅ Possibilidade de usar variáveis de ambiente para configurar as credenciais.
- ✅ Repositório público **sem segredos** – você deve sempre criar suas próprias chaves.

---

## 🚀 Melhorias Futuras

- Integração com banco de dados para histórico de consultas.
- Previsão para próximos dias (não só clima atual).
- Envio de imagens baseadas nas condições climáticas.
- Suporte a múltiplos idiomas.
- Comandos adicionais: `/help`, `/sobre`, etc.
- Diferenciar erros de:
  - cidade não encontrada;
  - erro de conexão/timeout com a API de clima.

---

## 📞 Suporte / Debug

Se algo não funcionar:

1. Verifique se as credenciais estão corretas no N8N.
2. Confirme que a API key da OpenWeather está **ativa**.
3. Teste o bot diretamente pelo Telegram (veja se ele responde a `/start`).
4. Consulte os **logs de execução** do workflow no N8N para ver detalhes de erros.

---

## 📄 Licença

Projeto educacional – Pós-graduação em Automação com N8N.
