# Chatbot de Clima no Telegram com N8N

Bot automatizado que informa a temperatura atual de cidades brasileiras através do Telegram, utilizando N8N e a API gratuita do OpenWeather.

---

## 📋 Funcionalidades

- Recebe mensagens no formato `Cidade,UF` (ex.: `São Paulo,SP`)
- Normaliza entrada (remove acentos, espaços extras, converte para minúsculas)
- Consulta temperatura atual via API OpenWeather
- Retorna mensagem formatada com:
  - Temperatura arredondada
  - Condição climática (nublado, ensolarado, etc.)
  - Sensação térmica
- Tratamento de erros com mensagem clara

---

## 📁 Arquivos do Repositório

- `workflow-chatbot-telegram.json` - Workflow exportado do N8N
- `README.md` - Documentação completa do projeto

---

## 🔧 Pré-requisitos

- **N8N** instalado (local, Docker ou cloud)
- **Conta Telegram** e bot criado via @BotFather
- **Conta OpenWeather** com API key ativa

---

## 🔐 Variáveis e Credenciais Necessárias

### No N8N, você precisa configurar:

#### 1. **Credencial Telegram** (`TELEGRAM_BOT_TOKEN`)
- Nome da credencial no N8N: `Telegram Bot API`
- Tipo: `Telegram`
- Campo: `Access Token`
- Valor: Token fornecido pelo @BotFather

#### 2. **Credencial OpenWeather** (`OPENWEATHER_API_KEY`)
- Nome da credencial no N8N: `OpenWeather API`
- Tipo: `HTTP Request` ou credencial customizada
- Campo: `API Key`
- Valor: Chave obtida em https://home.openweathermap.org/api_keys

**⚠️ IMPORTANTE:** Nunca exponha essas chaves no código ou repositório!

---

## 📥 Como Importar o Workflow no N8N

### Passo 1: Importar o arquivo JSON

1. Abra o N8N
2. Clique no menu superior direito (☰)
3. Selecione **Import from File**
4. Escolha o arquivo `workflow-chatbot-telegram.json`
5. Clique em **Import**

### Passo 2: Configurar credenciais

Após importar, você verá avisos de credenciais faltando. Configure:

#### **Telegram:**
1. No N8N, vá em **Credentials** (menu lateral)
2. Clique em **Add Credential** → **Telegram**
3. Nome: `Telegram Bot API`
4. Access Token: Cole o token do @BotFather
5. Salve

#### **OpenWeather:**
1. Vá em **Credentials** → **Add Credential**
2. Escolha **HTTP Header Auth** ou crie credencial customizada
3. Nome: `OpenWeather API`
4. Configure:
   - Header Name: `appid` (se usar Header Auth)
   - Header Value: Sua API key da OpenWeather
5. Salve

### Passo 3: Associar credenciais aos nós

1. Abra o workflow importado
2. Clique em cada nó que exibe aviso vermelho:
   - `Telegram Trigger`
   - `HTTP Request - OpenWeather`
   - `Telegram Send - Success`
   - `Telegram Send - Error`
3. No campo **Credential**, selecione a credencial correspondente
4. Salve o workflow

### Passo 4: Ativar o workflow

1. No canto superior direito, mude o toggle para **Active**
2. O workflow agora está rodando!

---

## 🤖 Como Criar o Bot no Telegram

1. Abra o Telegram e procure por **@BotFather**
2. Envie `/newbot`
3. Escolha um nome para o bot (ex.: `Meu Bot de Clima`)
4. Escolha um username terminando em `bot` (ex.: `meu_clima_bot`)
5. Copie o **token** fornecido (formato: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`)
6. Guarde esse token em local seguro

---

## 🌤️ Como Obter a API Key da OpenWeather

1. Acesse https://home.openweathermap.org/users/sign_up
2. Crie sua conta
3. Confirme o email
4. Acesse https://home.openweathermap.org/api_keys
5. Copie a **Default API Key** ou crie uma nova
6. Aguarde até 10 minutos para ativação

---

## ✅ Como Testar o Chatbot

### Teste de Sucesso:

1. Abra o Telegram
2. Procure seu bot pelo username
3. Envie mensagens de teste:
   - `São Paulo,SP`
   - `Belo Horizonte,MG`
   - `Rio de Janeiro,RJ`

**Resposta esperada:**
🌤️ A temperatura em São Paulo é de 22°C. ☁️ Condição: Céu limpo. 🌡️ Sensação térmica: 21°C.

### Teste de Erro:

Envie:
- `CidadeInexistente,ZZ`

**Resposta esperada:**
❌ Cidade não encontrada. Use o formato Cidade,UF (ex.: São Paulo,SP).

---

## 🔍 Estrutura do Workflow

1. Telegram Trigger ↓
2. Set Initial Data (captura chat_id e texto) ↓
3. Normalize and Create Queue (cria campo 'queue') ↓
4. HTTP Request - OpenWeather (consulta API) ↓
5. IF - Validate Response (valida cod=200 + campos) ↓ ↓ TRUE FALSE ↓ ↓
6. Build Success Message 8. Telegram Send - Error ↓
7. Telegram Send - Success

---

## 📝 Validações Implementadas

O workflow valida:
- ✅ Status HTTP 200
- ✅ Existência do objeto `main`
- ✅ Existência do campo `main.temp`
- ✅ Existência do campo `name` (nome da cidade)

Se qualquer validação falhar → mensagem de erro

---

## 🛡️ Segurança

- ✅ Nenhuma credencial exposta no JSON
- ✅ Uso de credenciais nativas do N8N
- ✅ Variáveis de ambiente protegidas
- ✅ Repositório público sem segredos

---

## 🚀 Melhorias Futuras

- [ ] Banco de dados para histórico de consultas
- [ ] Previsão para próximos dias
- [ ] Envio de imagens baseadas no clima
- [ ] Suporte a múltiplos idiomas
- [ ] Comandos adicionais (/help, /sobre)

---

## 📞 Suporte

Caso encontre problemas:
1. Verifique se as credenciais estão corretas
2. Confirme que a API key da OpenWeather está ativa
3. Teste o bot diretamente no Telegram
4. Verifique os logs de execução no N8N

---

## 📄 Licença

Projeto educacional - Pós-graduação em Automação com N8N
