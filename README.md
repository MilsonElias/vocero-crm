# Vocero CRM

[![CI](https://github.com/kevinrivm/vocero-crm/actions/workflows/ci.yml/badge.svg)](https://github.com/kevinrivm/vocero-crm/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

**O CRM de WhatsApp open source para seu agente de IA, com um Laboratório que o põe à prova.**

Vocero é um CRM self-hosted e gratuito para empresas que vendem pelo WhatsApp:
caixa de entrada em tempo real, pipeline de vendas, perfil e conhecimento do seu
agente e um **Laboratório** onde clientes simulados o avaliam antes que ele
converse com clientes reais. Uma instância = uma empresa, no seu próprio servidor,
com seus dados.

Vocero não inclui um agente: quem responde é aquele que você conecta ao lado.
[Nea](https://github.com/kevinrivm/nea-agent) (MIT) é o recomendado e é iniciado
com o mesmo `docker compose`; você também pode conectar o seu pelo
[contrato público](#-tu-agente-al-lado-del-crm). Sem agente, Vocero é um CRM
com caixa de entrada manual, e você responde. O token do WhatsApp nunca sai do CRM.

![Caixa de entrada do Vocero CRM](docs/screenshots/bandeja.png)

<p align="center">
  <img src="docs/screenshots/laboratorio.png" width="49%" alt="Laboratório: relatório com pontuação e constatações" />
  <img src="docs/screenshots/pipeline.png" width="49%" alt="Pipeline kanban" />
</p>

> 🎬 **Vídeo de instalação oficial**: em breve no
> [canal de Kevin Belier](https://www.youtube.com/@KevinBelier)

## Para quem é?

- **Agências de IA/automação** que implementam CRM + agente para seus
  clientes: você implanta uma instância por cliente no VPS dele (o CRM e o Nea, ou seu
  próprio agente), configura e entrega com evidências de qualidade (o
  relatório do Laboratório).
- **Empresas** que querem atender pelo WhatsApp com IA sem entregar seus dados a um
  SaaS: tudo é executado no seu servidor.

## Funcionalidades

### 🧪 Laboratório: o agente se testa sozinho

A atração principal. Seis clientes simulados —o comprador decidido, o que pergunta
preços, o cliente irritado, quem pergunta o que você não sabe, quem exige
falar com uma pessoa e quem escreve "ke onda si benden pintura"— conversam com seu
agente REAL —aquele que você conectou: o CRM despacha para ele cada mensagem simulada
como se viesse de um cliente— em um **sandbox que nunca envia mensagens
reais**. Sem um agente conectado, o Laboratório pede que você o conecte
primeiro. Um juiz LLM independente, com a chave de Configurações → IA, avalia cada
conversa e entrega:

- uma **pontuação de 0–100** sobre o preparo do agente;
- **constatações com evidências** (alucinações, lacunas de conhecimento, falhas
  no escalonamento, tom);
- **sugestões aplicáveis com um clique** à base de conhecimento;
- e **histórico com delta**: execute novamente após cada alteração e veja se houve melhora.

Pare de "esperar que o bot funcione": meça.

### 💬 Caixa de entrada do WhatsApp em tempo real

Três colunas (conversas / conversa / contato), mensagens recebidas em ≤2
segundos sem recarregar, status enviado/entregue/lido, janela de 24 horas
visível e bloqueada corretamente (com envio de modelo aprovado quando estiver
fechada), respostas do agente identificadas como IA e transferência para uma pessoa com um
clique. Os anexos são enviados e recebidos (imagem, áudio, vídeo, documento, localização e
contato) com pré-visualização, e os arquivos são armazenados no seu próprio servidor.

Se a conversa começou por um anúncio Click-to-WhatsApp, a caixa de entrada informa
qual: «Anúncio · título» na lista, um filtro «Anúncios» e, no painel do
contato, a peça criativa, o texto e o link do anúncio. Não exige flag nem
credenciais: a informação chega no próprio webhook.

Em **Contatos**, você pode **importar** uma lista existente (um CSV com nome
e telefone com código do país): veja o que será incluído antes de criar qualquer coisa; ninguém é
duplicado e seu pipeline não é preenchido. Você também pode marcar contatos como **«sem
agente»** —ao importar ou um por um— para atender pessoalmente seus clientes habituais:
as mensagens deles chegam à caixa de entrada e o agente não responde.

Uma conversa pode ser **excluída** no painel do contato (visível para quem
administra a empresa): as mensagens, os anexos e o que o agente
aprendeu nela são removidos, e o contato permanece na etapa atual, a menos que você também peça para excluí-lo.
Se a pessoa voltar a escrever, o agente começa do zero.

### 📊 Contatos e pipeline kanban

Cada pessoa que escreve é registrada automaticamente e entra no pipeline
(Novo → Em conversa → Interessado → Cliente → Perdido, editável). Arraste
cartões, pesquise, adicione notas e arquive.

### 🎯 Resultados

Uma tela para a reunião de segunda-feira: novos leads, negócios ganhos e
perdidos, valor fechado e o funil de quem chegou, em comparação com o período
anterior; se o agente responde, com que rapidez e quando transfere para uma pessoa; de
qual origem e de qual **anúncio** vêm as conversas que terminam em
venda (com a miniatura da peça criativa); e o que está dando errado hoje: leads
sem resposta, mensagens que não chegaram e janelas de 24 h prestes a fechar. Tudo vem
dos dados do próprio CRM: não é preciso conectar nada, e o Laboratório não entra na conta.
Gastos com anúncios e retorno por anúncio não são incluídos de propósito: esses dados não
existem no CRM. Com a agenda ativada, soma os agendamentos (realizados, não
comparecimentos e cancelados). Os dias são definidos pelo fuso horário da agenda; se
não houver uma agenda configurada, pelo de Cidade do México.

### 🤖 Seu agente, com o SEU conhecimento

Na tela **Agente**, você configura nome, tom, instruções e regras de
escalonamento, e fornece conhecimento em pares de pergunta/resposta e blocos livres.
Tudo isso fica no CRM, e o agente lê as informações de lá (`GET /api/bot/profile`):
você edita tudo em um só lugar. O mesmo vale para a chave de IA: cole-a uma vez em
Configurações → IA, e seu agente raciocina **por meio do CRM**, sem copiá-la para nenhum
outro lugar.

O controle nessa tela determina se o CRM encaminha as conversas ao seu
agente. Só pode ser ativado quando há um agente conectado e on-line; desativado, tudo
fica na caixa de entrada para sua equipe.

<a id="-tu-agente-al-lado-del-crm"></a>

### 🔌 Seu agente ao lado do CRM

Quem responde é um serviço separado, no mesmo servidor:
[Nea](https://github.com/kevinrivm/nea-agent) (MIT; entende áudio, imagem e
PDF, transfere para uma pessoa, agenda e faz acompanhamento) ou um cérebro seu. Ele se conecta
com uma `BOT_API_KEY` pela API de serviço: seu agente conversa por meio do
CRM, então **o token do WhatsApp nunca sai daqui** e tudo fica na
caixa de entrada como qualquer outra conversa.

Com docker compose, os dois serviços são iniciados juntos (a receita completa, com Coolify,
está em [`docs/migrar-a-2.0.md`](docs/migrar-a-2.0.md#el-par-crm--nea)):

```bash
# no seu .env: BOT_API_KEY, BRAIN_DISPATCH_URL=http://nea:8000/vocero/dispatch
# e BRAIN_HEALTH_URL=http://nea:8000/health
docker compose up -d
```

Enquanto a imagem 2.0.0 do Nea não é publicada, o serviço fica atrás do perfil
`nea`; com o lançamento final da versão 2.0, ele será iniciado sempre.

| Endpoint | Finalidade |
|---|---|
| `GET /api/bot/context` | Quem é a pessoa, sua etapa, se alguém assumiu a conversa e se a janela de 24 h continua aberta. Com a agenda ativada, também inclui seus agendamentos (`booking`): o próximo, o que já passou sem ser encerrado e o último que foi encerrado (cancelado, não compareceu ou realizado) |
| `POST /api/bot/messages` | Responder. A resposta segue o mesmo caminho do composer e é identificada como IA |
| `GET /api/bot/profile` | O perfil do agente e a base de conhecimento editada no app; em `llm`, como raciocinar e com qual modelo (`null` sem IA configurada) |
| `POST /api/bot/llm/{ruta}` | Raciocinar com a chave de Configurações → IA. Compatível com OpenAI: aponte o `base_url` do seu SDK para cá. Aceita `X-API-Key` ou `Authorization: Bearer`. Sem IA configurada, 402 `sin_credencial` |
| `PUT /api/bot/ficha` | Salvar o que o bot descobre sobre o lead (chaves livres: cada empresa qualifica de um jeito) |
| `POST /api/bot/stage` | Avançar o lead no pipeline: somente para frente e para etapas abertas (as que o contexto traz em `lead.nextStages`). Uma pessoa decide se um negócio será ganho ou perdido. Pode ser desativado na tela Agente («Mover de etapa») |
| `POST /api/bot/handoff` | Devolver a conversa para uma pessoa |
| `POST /api/bot/typing` | Marcar como lida e mostrar "digitando…" |
| `GET /api/bot/media/{id}` | Baixar um anexo recebido sem acessar a Meta |
| `POST /api/bot/reset` | Reiniciar uma conversa de teste |
| `GET /api/bot/availability` | Com a agenda ativada: horários para oferecer, que ficam registrados como oferecidos. Com `date=AAAA-MM-DD`, todos os horários livres desse dia; `query` informa até onde vai a consulta |
| `POST` · `PATCH /api/bot/bookings` | Com a agenda ativada: agendar (201) ou alterar (200) um horário, somente se ele tiver sido oferecido |

O contrato completo, incluindo o despacho e a notificação de esquecimento, está em
[`docs/openapi.json`](docs/openapi.json) (OpenAPI 3.1), e sua instância o serve
em `GET /api/bot/openapi.json`. Se você vem da versão 1.x, consulte
[`docs/migrar-a-2.0.md`](docs/migrar-a-2.0.md).

Os erros 409 são tipados (`ai_paused`, `window_closed`, `sandbox_violation`,
`message_limit`) para que seu bot saiba se deve parar, enviar um modelo ou desistir.
`message_limit` significa que o canal não aceita mais mensagens até que o
cliente volte a escrever (hoje, o limite do TikTok é 10): não tente novamente. O roteiro de testes
está em [`tests/e2e/us-bot-api.md`](tests/e2e/us-bot-api.md).

A API verifica a chave antes de contar as chamadas: com a chave correta, são permitidas 1200
chamadas por minuto; sem chave ou com uma inválida, 30 por minuto do mesmo IP
respondem `401`, e as seguintes respondem `429` («Muitas tentativas malsucedidas»). Quem
sobrecarregar a API sem chave não deixa seu bot fora do ar.

**O CRM encaminha cada turno ao seu agente (o despacho).** Seu agente não
precisa receber o webhook da Meta: com `BRAIN_DISPATCH_URL` (por exemplo,
`http://nea:8000/vocero/dispatch`), a mesma `BOT_API_KEY` e o controle da tela
Agente ativado, a mensagem chega primeiro à caixa de entrada, e o CRM envia ao seu agente um
`POST` assinado com o que o cliente escreveu desde a última resposta, tudo junto,
depois de esperar o fim da sequência de mensagens. Seu agente
responde por `POST /api/bot/messages`. Funciona da mesma forma para WhatsApp, Instagram,
Messenger e TikTok, sem segredo da Meta para copiar nem fila de reenvio para
monitorar. Se seu agente não receber o turno (três tentativas), a conversa
fica com uma pessoa, e a mensagem continua na caixa de entrada. O contrato do evento
e da assinatura está em
[`specs/021-despacho-estandar/contracts/despacho.md`](specs/021-despacho-estandar/contracts/despacho.md)
e seu roteiro em [`tests/e2e/us-despacho.md`](tests/e2e/us-despacho.md).

Se seu bot recebe os webhooks da Meta por um **override de callback da
WABA**, salvar a conexão em Configurações → WhatsApp (ou renovar o token) respeita
essa configuração: Vocero detecta o override em `GET /{WABA}/subscribed_apps`, não volta a inscrever
o app —o que o apagaria— e também não registra seu próprio webhook
no número, pois ele teria prioridade e impediria seu bot de receber mensagens. A
tela informa qual host está sendo respeitado e, se você quiser que as mensagens
voltem ao CRM (por exemplo, ao migrar do Nea 1.x para o despacho), oferece
**Recuperar o webhook**: remove esse override e mantém aqui o webhook do número.

**Seu agente raciocina pelo CRM.** `POST /api/bot/llm/{ruta}` encaminha a
chamada sem alterações ao provedor de Configurações → IA usando a chave salva e retorna
a resposta. A chave não é enviada ao seu agente: se o
servidor dele for comprometido, a chave não será exposta junto. Se o provedor rejeitar a chave (401)
ou ficar sem créditos (402), ela será pausada em Configurações → IA, e a tela informará isso,
assim como quando há uma falha no CRM. O Nea usa esse recurso somente quando você não define
`LLM_API_KEY`; se definir, ele usa seu próprio provedor.

No topo da tela **Agente**, o cartão **«Quem responde aos seus
clientes»** informa se há um agente conectado: a última chamada à API, para
quem os turnos são despachados, quando chegou o último turno e se algum não chegou; e,
com `BRAIN_HEALTH_URL` (por exemplo, `http://nea:8000/health`), se está on-line,
sua versão e modo. Sem agente, informa que o Vocero funciona como caixa de entrada
manual (é um aviso, não um erro). Com o agente conectado e o controle
desativado, avisa que ninguém está respondendo.

### 📅 Agenda com horários disponíveis reais (opcional, desativada por padrão)

Ative-a com `AGENDA=on`, e o CRM saberá quando você está disponível: defina seus horários
em Configurações → Agenda, e seu agente oferece horários específicos, reserva aquele que o
cliente escolher e o registra junto à conversa. Duas garantias inegociáveis:
**só é possível reservar um horário que foi oferecido** (nada de o
modelo inventar uma terça-feira às 10h) e **nunca é confirmado um agendamento que não foi
criado** — se o horário for ocupado durante a conversa, a resposta traz
novas alternativas em vez de uma promessa falsa.

Os agendamentos aparecem na aba **Agendamentos** como um calendário: Dia, Semana, Mês e
Lista, sempre no fuso horário da empresa, mesmo que você acesse o CRM de outro local.
Toque em um agendamento e, no painel, abra a conversa, reagende, marque como realizado ou «não compareceu» ou cancele; toque em um horário vazio da
grade para bloqueá-lo.

Você escolhe como a reunião será realizada, usando um **conector**:

| Conector | O que faz | Requisitos |
|---|---|---|
| **Link fixo** (padrão) | Compartilha sua sala de sempre | Nenhum |
| **Zoom** | Uma reunião por agendamento; ao reagendar, a reunião é movida; ao cancelar, é excluída | Seu app Server-to-Server |
| **Google Calendar + Meet** | Um evento no seu calendário com link do Meet | Seu app do Google Cloud |

Você usa outra ferramenta? O contrato tem quatro operações e é público: escreva
seu conector no seu fork seguindo
[`docs/agenda-conectores.md`](docs/agenda-conectores.md). Se o seu provedor falhar,
o agendamento **é realizado mesmo assim**, com o link pendente de nova tentativa: a indisponibilidade de terceiros não custa uma conversão.

### 📈 Conversões de anúncios (opcional, desativada por padrão)

Se você anuncia com **Click-to-WhatsApp**, a Meta sabe quais conversas começaram
a partir de um anúncio, mas não quais deram resultado: sem alguém para informar, ela otimiza
para o público mais barato de fazer escrever, que raramente é quem compra.

Ative-a com `ATRIBUCION=on`, informe seu dataset em Configurações → Anúncios (o token
é reutilizado da sua conexão do WhatsApp) e indique qual etapa do SEU pipeline significa
"lead qualificado". A partir daí, o CRM reporta à Meta o lead qualificado e
a venda fechada —com o respectivo valor— pela **Conversions API**, e uma tabela de
atividade informa o que foi enviado, qual foi a confirmação e, quando não foi enviado, o motivo.

Não pedimos ao usuário nenhuma informação que o CRM já conheça: a venda depende da etapa
ganha que você já tem, e tudo é disparado pelo mesmo ponto que move leads,
então o reporte funciona independentemente de quem mover o cartão. Se a Meta ficar indisponível, o lead ainda muda de etapa: uma conversão nunca justifica bloquear uma
movimentação. Os detalhes da Meta que são difíceis de descobrir sozinho estão em
[`docs/atribucion-capi.md`](docs/atribucion-capi.md).

O que **não** precisa da flag: saber de qual anúncio veio cada
conversa. A caixa de entrada identifica o anúncio («Anúncio · título», com seu filtro), e o
painel do contato e o painel do negócio mostram a peça criativa, o texto e o
link do anúncio em qualquer instância que receba um clique em anúncio.

### 🎨 Sua marca, nos temas claro e escuro

Em Configurações → Marca, você define o nome, a cor e o
logotipo da sua empresa no CRM; o logotipo aparece na barra lateral, na tela de login e na aba do
navegador. A barra lateral é azul-marinho nos dois temas, e cada pessoa
escolhe o tema claro ou escuro no navegador. No tema escuro, a página, a barra e os elementos
suspensos ficam diferenciados, e o texto atende ao contraste AA. Com uma cor
personalizada, os tons derivados e a cor dos botões são calculados automaticamente para
garantir a legibilidade.

### 📱 No celular, como um app

Vocero é instalado pelo navegador, sem lojas de apps e com o nome e o ícone
da sua empresa:

- **Android e computador (Chrome ou Edge)**: a opção **Instalar
  app** aparece no menu quando o navegador permite.
- **iPhone (Safari)**: Compartilhar → **Adicionar à Tela de Início**.

Abre diretamente na Caixa de entrada, sem a barra do navegador. Se a conexão cair, mostra
«Sem conexão» em vez de conversas antigas: nenhum dado dos seus
clientes é armazenado no telefone.

### 📄 Modelos · 👥 Multiusuário · 🔐 Self-hosted

Modelos com várias variáveis `{{1}}…{{n}}` e aprovação da Meta
sincronizada; contas da equipe criadas pelo proprietário (o registro público
é fechado após a primeira organização); token do WhatsApp criptografado em repouso
(AES-256-GCM),
webhook autenticado em duas camadas e nenhuma dependência de runtime além da
Meta e do seu provedor LLM opcional.

## Pré-requisitos

- Um VPS com Docker (2 GB de RAM são suficientes para executar a imagem publicada) — com
  ou sem [Coolify](https://coolify.io). A imagem é `linux/amd64`; em um VPS
  ARM, você precisa compilar o código, o que leva vários minutos e exige mais memória.
- Um domínio apontando para o VPS (a Meta exige **https** para webhooks).
- Um número de WhatsApp na Cloud API da Meta (consulte [Conexão](#conexión-del-número-de-whatsapp)).
- Opcional: uma API key da [OpenRouter](https://openrouter.ai) (ou de qualquer
  provedor compatível) para seu agente e o juiz do Laboratório.
- Opcional: um agente junto ao CRM ([Nea](https://github.com/kevinrivm/nea-agent)
  ou outro). Sem ele, o Vocero funciona como caixa de entrada manual.

## Instalação (~15 minutos)

Cada versão é publicada como imagem Docker:
`ghcr.io/kevinrivm/vocero-crm:<versión>` (por exemplo, `2.7.0`). A instalação pela
imagem é o método recomendado: seu servidor faz o download, em vez de compilá-la.
Compilar a partir do código continua sendo possível e é necessário em um VPS ARM ou se
seu fork modificar o código.

### 0. Aponte seu domínio

Crie um registro **A** de `crm.tudominio.com` apontando para o IP do VPS e aguarde até
que seja resolvido.

### Rota A — Coolify com orientação por IA (recomendada)

Abra seu assistente de IA (por exemplo, Claude Code com o MCP do Coolify), forneça o
arquivo [`INSTALL-IA.md`](INSTALL-IA.md) e responda a 3 perguntas (domínio, token
opcional da OpenRouter, rota). O assistente cria o banco de dados e um app do
tipo «Docker Image» com a imagem da versão, monta o volume de
`/data`, gera os segredos e verifica o healthcheck. O mesmo guia também inclui a
variante que compila a partir do repositório.

### Rota B — docker compose

```bash
git clone https://github.com/kevinrivm/vocero-crm.git vocero && cd vocero
pnpm install && pnpm init-env --domain crm.tudominio.com   # cria .env com os segredos já gerados
pnpm doctor                                                 # verifica o .env antes de iniciar
docker compose up -d
```

`pnpm init-env` cria o `.env` a partir do `.env.example` com todos os
segredos gerados (nunca sobrescreve um existente); sem `--domain`, define
`DOMAIN=localhost` para testar na mesma máquina. É necessário ter Node 22 e pnpm
(`corepack enable`); se não estiverem no VPS, use `cp .env.example .env` e preencha
manualmente (cada variável inclui o comando `openssl`). `pnpm doctor` valida as
variáveis com o mesmo esquema usado na inicialização, testa o banco de dados, o
diretório de anexos e, se já houver um número conectado, o token do WhatsApp:
cada ✗ é acompanhado do comando para corrigir o problema, e nenhum segredo é impresso.

`docker-compose.yml` declara as duas opções: a imagem publicada (`image`) e o
código (`build: .`). Sem `pull_policy`, a
[especificação do Compose](https://docs.docker.com/reference/compose-file/build/#using-build-and-image)
explica o comportamento: primeiro tenta baixar a imagem e só a compila a partir do
código se não a encontrar no registro nem na sua máquina. Portanto:

- `docker compose up -d` executa a imagem publicada da versão definida no
  compose. Para usar outra versão publicada, defina `VOCERO_CRM_VERSION=X.Y.Z` no seu
  `.env`.
- `docker compose up -d --build` compila a imagem a partir da sua cópia do código. É o
  que você precisa em um VPS ARM e em um fork com alterações próprias; no fork,
  use sempre essa opção: a imagem compilada mantém o mesmo nome, e sem
  `--build` o Compose pode executar a imagem oficial em vez das suas alterações.

Caddy emite o certificado HTTPS automaticamente. Verifique com
`https://crm.tudominio.com/api/health` → `{"ok":true,"version":"2.7.0",…}`.

### Outras plataformas: Render, Fly e Railway

Há um manifest para cada plataforma na raiz do repositório. Os três executam a mesma
imagem publicada, montam `/data` e monitoram `/api/health`; cada um declara o que
sua plataforma permite e documenta, em comentários, o que precisa ser configurado
manualmente. A imagem é fixada em uma versão (um teste exige isso). Verifique como
acima: `curl -s https://<tu-url>/api/health` →
`{"ok":true,"version":"2.7.0",…,"mediaWritable":true}`.

**Render** — [`render.yaml`](render.yaml) é um Blueprint completo: serviço
web a partir da imagem, banco Postgres gerenciado, disco de 1 GB em `/data`,
healthcheck e variáveis obrigatórias; o Render gera `BETTER_AUTH_SECRET` e `ENCRYPTION_KEY`
(256 bits em base64: exatamente os 32 bytes exigidos pela chave), e
`APP_BASE_URL` é obtida da URL `.onrender.com` do serviço. Aplique usando
**New → Blueprint** no seu fork ou pelo botão abaixo. Ainda é necessário configurar:
`META_WEBHOOK_VERIFY_TOKEN` quando o Render solicitar (`openssl rand -hex 32`:
o Render gera base64, com `/`, e o token é um segmento da URL do
webhook), o token da OpenRouter e, se usar domínio próprio, `APP_BASE_URL`. O
disco exige um plano pago (`starter` + `basic-256mb`; o banco `free` expira após
30 dias).

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/kevinrivm/vocero-crm)

**Fly.io** — [`fly.toml`](fly.toml) declara a imagem, o volume `/data`, a porta
3000, o healthcheck, o tamanho da máquina e as variáveis não secretas; o Fly não permite
incluir segredos nem o banco no arquivo. É preciso configurar manualmente uma vez, com os
comandos exatos documentados no próprio `fly.toml`: `fly apps create`,
`fly volumes create`, `fly postgres create` + `fly postgres attach` (define
`DATABASE_URL`), `fly secrets set` com os três segredos e
`fly deploy --ha=false`. Esse Postgres não é gerenciado (é barato; o Fly não
faz mais a manutenção): o gerenciado é criado com `fly mpg create` + `fly mpg attach`. A máquina
não é desligada automaticamente de propósito: o despacho para seu agente e o Laboratório são executados
dentro do processo.

**Railway** — [`railway.toml`](railway.toml) só pode declarar o build
(Dockerfile), o healthcheck e a política de reinicialização; o banco, o volume,
o domínio e as variáveis são criados pela CLI, com os comandos exatos
documentados no próprio arquivo (`railway add --database postgres`,
`railway volume add --mount-path /data`, `railway domain --port 3000`,
`railway variable set …`, `railway up`). Para usar a imagem publicada sem build:
`railway add --image ghcr.io/kevinrivm/vocero-crm:2.7.0`; nesse caso, o
healthcheck deve ser definido no painel. O Railway declarou esse formato obsoleto em
favor do Infrastructure as Code (`.railway/railway.ts`), mas continuará lendo-o
até 2026-12-01.

### Anexos em um bucket (opcional)

Por padrão, os anexos, o logotipo e o ícone ficam no volume `/data`.
Se sua plataforma não tiver disco persistente, ou se preferir armazená-los separadamente,
envie-os para um bucket S3-compatible **seu**, usando `MEDIA_STORE=bucket` e as
variáveis `MEDIA_BUCKET_*` (orientações em [`.env.example`](.env.example)):

- **MinIO no mesmo servidor**: `docker compose --profile bucket up -d`
  inicia o MinIO e cria o bucket; `MEDIA_BUCKET_ENDPOINT=http://minio:9000`.
- **R2, S3, Backblaze, Tigris…**: use o endpoint, a região e uma chave com
  permissão de gravação no bucket.

Os arquivos novos vão para o bucket, e os que já estavam no volume continuam sendo lidos
de lá: não é preciso migrar nada, e desativar a flag também não torna nenhum arquivo ilegível.
Se o bucket não aceitar uma gravação, o arquivo será salvo no volume
(não será perdido), e `/api/health` (`mediaWritable: false`) e `pnpm doctor` informarão o problema.
O Vocero assina as próprias requisições: não inclui o SDK da AWS.

Vercel e plataformas semelhantes continuam fora do escopo: com o bucket, já não dependem do disco, mas
continuam existindo o trabalho em segundo plano (despacho e Laboratório), o SSE em
memória e as migrações na inicialização.

### Atualização

Antes, leia em [`CHANGELOG.md`](CHANGELOG.md) a seção «Atualizar a partir de…» da
nova versão. Com docker compose: `git pull` e `docker compose up -d` (com
`--build` se seu fork tiver alterações próprias). No Coolify: altere a tag da
imagem para a nova versão e faça o redeploy; se a compilação for feita a partir do
repositório, faça o redeploy. Com os manifests: altere a tag da imagem
no arquivo (o Render sincroniza o Blueprint; no Fly, use `fly deploy`; o Railway
compila a partir do código: `git pull` e `railway up`). As migrações são executadas
automaticamente na inicialização.

### Primeira inicialização

1. Acesse e **faça seu cadastro**: o primeiro cadastro cria sua organização e fecha o
   registro público.
2. A primeira tela é o **Guia de início**: configure seu agente, vincule
   seu provedor de IA, conecte seu número do WhatsApp e, opcionalmente, **conecte seu
   agente** (Nea ou outro; o endereço dele vai nas variáveis de ambiente, e a etapa
   leva você às instruções e é concluída automaticamente quando o agente responde ao
   `/health`). Com a agenda ativada (`AGENDA=on`), há mais uma etapa para configurar seus
   horários. Cada botão leva à tela correspondente, e ao voltar você vê seu
   progresso; cada etapa só é concluída quando o recurso realmente funciona. Com um
   agente conectado, aparece a opção **Ativar**: até lá, ninguém responde aos
   clientes automaticamente, e você atende as mensagens recebidas pela Caixa de entrada.
   Sem agente, o guia informa que o Vocero funciona como caixa de entrada manual.
3. Enquanto faltar uma etapa —ou houver um agente conectado, mas desativado—, acessar
   o app leva ao guia (que também aparece no menu, apenas para o
   proprietário). Se você não conectar um agente, ao concluir as outras etapas será direcionado
   diretamente à Caixa de entrada. O guia não impede o acesso: pelo menu, você pode abrir
   qualquer tela a qualquer momento.
4. Opcional: na Caixa de entrada, clique em **"Carregar dados de demonstração"** para
   explorar a **Ferretería El Martillo** (contatos, conversas,
   pipeline, uma base de conhecimento com lacunas intencionais e uma execução de
   exemplo do Laboratório — execute-o e veja como as lacunas são encontradas).
   A demonstração configura o agente (etapa 1 concluída); a chave de IA e o
   número continuam sendo seus.

<a id="conexión-del-número-de-whatsapp"></a>

## Conexão do número do WhatsApp

O Vocero **usa** um token da WhatsApp Cloud API — não implementa o
Embedded Signup. Há duas maneiras de obtê-lo:

### Modo direto (a empresa tem seu próprio app da Meta)

> **Atalho: «Conectar com a Meta».** Com `META_APP_ID`,
> `META_EMBEDDED_CONFIG_ID` e `META_APP_SECRET` na instância, Configurações →
> WhatsApp mostra um botão que abre a janela oficial da Meta (Embedded
> Signup v4) e conecta o número sem colar tokens: registra o número e
> o webhook automaticamente. O que preencher no painel da Meta (domínios, configurações de
> login): [docs/embedded-signup.md](docs/embedded-signup.md). Sem essas
> variáveis, siga o cadastro manual abaixo.

1. Crie um app em [developers.facebook.com](https://developers.facebook.com)
   com o produto WhatsApp e vincule seu número.
2. Crie um **usuário do sistema** (Business Settings → System users) com
   acesso à WABA e gere um token permanente com as permissões
   `whatsapp_business_messaging` e `whatsapp_business_management`.
3. No Vocero: **Configurações → WhatsApp** → cole WABA ID + Phone Number ID +
   token → **Testar conexão** → Salvar. Ao salvar, o Vocero **registra o
   webhook na Meta para você** (override no nível do número, incluindo o handshake)
   e confirma na tela. Se a Meta rejeitar, a conexão ainda será salva, e o aviso informa o motivo (quase sempre, o token não tem
   `whatsapp_business_management`); o botão **«Registrar na Meta»** no
   cartão do webhook tenta novamente sem que você precise colar o token de novo.
4. Somente se seu app ainda não tiver nenhum webhook configurado (o Vocero avisará):
   no painel da Meta (WhatsApp → Configuration → Webhook), cole a
   **URL do webhook** e o **verify token** exibidos pelo Vocero e inscreva
   o campo `messages` (e `message_template_status_update` se você usar
   modelos). Isso só precisa ser feito uma vez por app.
5. Recomendado: adicione `META_APP_SECRET` (App Secret do seu app) às
   variáveis da instância para verificar a assinatura de cada evento.

### Modo agência (Tech Provider) — para agências

Sua plataforma de agência já executa o Embedded Signup e armazena os tokens dos seus
clientes; a instância do Vocero do cliente apenas recebe o token dele. O webhook
do cliente é conectado com o **override de callback por WABA**:

```text
   Meta (WABA do cliente)
        │  webhooks (override_callback_uri)
        ▼
   ┌────────────────────────────┐      ┌─────────────────────────────┐
   │  Instância Vocero          │      │  Backend da SUA agência    │
   │  (VPS do cliente)          │      │  (Embedded Signup + tokens) │
   │  /api/webhooks/wa/<token>  │      └──────────────┬──────────────┘
   └────────────▲───────────────┘                     │
                └────── token do cliente ─────────────┘
                        (colado no assistente de configuração)
```

**Checklist de 5 etapas (a ordem importa):**

1. **Implante primeiro a instância** (Rota A ou B) — o webhook precisa estar
   on-line para o handshake da etapa 3.
2. **Embedded Signup na SUA plataforma**: o cliente conecta o número no seu
   onboarding e seu backend armazena o token dele (troca de código → token).
3. **Cole as credenciais no assistente de configuração** da instância (WABA ID, Phone
   Number ID, token) → **Testar conexão** → **SALVAR**. Ao salvar, o Vocero
   **registra o override do callback no nível do número** apontando para a própria
   instância (e o webhook encaminha cada mensagem pelo Phone Number ID
   salvo). Se seu backend já encaminhar essa WABA para outro lugar, o Vocero respeita a configuração
   e não registra o webhook do número.
4. **Somente se preferir encaminhar pelo seu backend** (ou se o registro automático
   falhar), configure o override do callback no nível da WABA apontando para a instância:

   ```http
   POST https://graph.facebook.com/v25.0/{WABA_ID_DEL_CLIENTE}/subscribed_apps
   Authorization: Bearer {TOKEN_DEL_CLIENTE}
   Content-Type: application/json

   {
     "override_callback_uri": "https://crm.cliente.com/api/webhooks/wa/{VERIFY_TOKEN}",
     "verify_token": "{VERIFY_TOKEN}"
   }
   ```

   A URL exata e o verify token estão em **Configurações → WhatsApp** na
   instância. A Meta faz o handshake nesse momento (a URI precisa responder; caso
   contrário, retorna 422). Salvar novamente a conexão depois (por exemplo, ao renovar o
   token) respeita esse override: o Vocero não reinscreve uma WABA que já o tenha.
5. **Registre o número** na Cloud API, se ainda não estiver registrado
   (`POST /{PHONE_NUMBER_ID}/register`), e envie uma mensagem de teste para o número:
   ela deve aparecer na caixa de entrada em um ou dois segundos. As mensagens do cliente
   chegam diretamente à instância DELE, não ao seu backend.

> ⚠️ **Segurança**: a URL do webhook contém o verify token como segmento
> secreto — trate-a como uma senha (não a publique nem a envie por
> canais inseguros). No modo direto, você pode adicionar a camada extra de assinatura com
> `META_APP_SECRET`.
>
> ℹ️ **Limitação conhecida da Meta**: os eventos de status dos MODELOS
> (`message_template_status_update`) não seguem o override de callback — são enviados ao
> app proprietário. Por isso, o Vocero também **sincroniza modelos pela API do
> Graph** (botão "Sincronizar" em Configurações → Modelos), para que o modo
> agência também veja as aprovações.

## Canais opcionais: Instagram, Messenger e TikTok

O WhatsApp é o canal que deu origem ao Vocero e está sempre ativado. Os
demais fazem parte do mesmo código, **desativados por padrão** ([ADR-001](docs/adr-001-canales-opcionales.md)):
uma instância que não os usa não exibe telas, webhooks nem variáveis relacionadas.
Eles são ativados por uma variável de implantação:

```bash
CHANNELS=whatsapp,instagram,messenger,tiktok   # os canais desejados; whatsapp está sempre incluído
```

Com mais de um canal ativado, a Caixa de entrada mostra o identificador de cada
conversa e permite filtrar por canal. O contato, o pipeline, a ficha
e o agente são os mesmos: um lead é um lead, independentemente do canal por onde escreva.

Seu agente escreve da mesma forma para todos os canais, e o Vocero adapta o texto ao enviá-lo:
no Instagram, Messenger e TikTok, que não exibem o negrito do WhatsApp, o
texto chega sem asteriscos, com marcadores e links legíveis.

**Configurações → Status** reúne todos os canais ativados: se estão
conectados, quando chegou a última mensagem de um cliente e, para os que
usam Zernio, o status da conta e do webhook. É o primeiro lugar a
consultar quando «não estou recebendo as mensagens».

<a id="conectar-por-zernio-instagram-messenger-o-tiktok"></a>

### Conectar pelo Zernio (Instagram, Messenger ou TikTok)

[Zernio](https://zernio.com) é uma API unificada: você vincula suas redes por lá, com
sua própria conta, e uma única chave conecta todas elas. No Vocero, o processo é igual para
os três canais:

1. No Zernio, vincule a rede (**Accounts → Connect**) e crie uma API key
   (**Settings → API Keys**; começa com `sk_` e só é exibida uma vez).
2. No Vocero, abra a tela do canal, escolha *Zernio*, cole a chave e
   clique em **Testar e conectar**. Somente o proprietário pode fazer isso.

Não é necessário copiar nenhum ID nem cadastrar mais nada no Zernio:

- **O Vocero encontra a conta** usando essa chave. Se a chave tiver várias
  contas dessa rede (como em uma agência), ele oferece a lista para você escolher.
- **O Vocero cadastra o webhook** no seu Zernio, com o evento
  `message.received`, um segredo de assinatura gerado por ele e **limitado às suas
  contas conectadas**. Um webhook do Zernio sem esse limite recebe as
  mensagens de todas as contas associadas à chave. Os outros webhooks que você tiver no
  Zernio não são alterados.
- Se um dia o webhook for desativado ou estiver sem alguma conta, **Configurações →
  Status** informa e corrige o problema com um botão.

Cada entrega é verificada pela assinatura do Zernio. Se você atualizar de uma
versão anterior e já tiver criado o webhook manualmente, ele continuará funcionando: ao
abrir **Status**, você verá se falta alguma coisa.

### Messenger (página do Facebook)

Há duas formas de receber mensagens; escolha em **Configurações → Messenger**.

**Com Zernio**: siga [Conectar pelo Zernio](#conectar-por-zernio-instagram-messenger-o-tiktok)
em **Configurações → Messenger**. A página e seu nome vêm do Zernio.

**Com um app próprio da Meta**:

1. Em [developers.facebook.com](https://developers.facebook.com), crie (ou use)
   um app com o produto **Messenger** e gere o **token de acesso da
   página** com a permissão `pages_messaging`. Anote o **ID da página**.
2. No Vocero, em **Configurações → Messenger**, escolha *App próprio da Meta*, cole
   o ID e o token e clique em *Testar e salvar*.
3. No app da Meta, em **Messenger → Webhooks**: objeto `page`, campo
   `messages`, a URL de callback e o token de verificação exibidos na
   tela. Inscreva a página no app.

Desde então, as mensagens enviadas à página entram na caixa de entrada
como `Messenger`, com o nome do perfil, e as respostas enviadas pelo Vocero
(por você ou pelo agente) chegam ao chat da pessoa. Não há modelos: após 24 horas da
última mensagem do cliente, somente uma pessoa pode responder, por até 7 dias
(com a etiqueta `HUMAN_AGENT` da Meta); a caixa de entrada avisa e informa
quanto tempo resta. O agente não responde fora das 24 horas.
Atualmente, o canal aceita apenas texto: os anexos recebidos aparecem como
«📎 Imagem» para você saber que chegaram; ainda não é possível enviar anexos.

Com um app próprio e sem App Review, a página só recebe mensagens de contas com
uma função no app; para atender o público, é preciso aprovar `pages_messaging`.
Pelo Zernio, essa aprovação já foi resolvida por eles.

### Instagram (mensagens diretas do perfil profissional)

Há duas formas de receber mensagens; escolha em **Configurações → Instagram**.
A conta precisa ser profissional (comercial ou de criador). Não é necessário vincular uma
página do Facebook: isso só é exigido pelo Messenger.

**Com Zernio**: siga [Conectar pelo Zernio](#conectar-por-zernio-instagram-messenger-o-tiktok).

**Com um app próprio da Meta**:

1. Em [developers.facebook.com](https://developers.facebook.com), crie (ou use)
   um app com o produto **Instagram** e gere o token da conta com
   as permissões `instagram_business_basic` e
   `instagram_business_manage_messages`. Anote o **ID da conta**.
2. No Vocero, em **Configurações → Instagram**, escolha *App próprio da Meta*, cole
   o ID e o token e clique em *Testar e salvar*.
3. No app da Meta, em **Instagram → Webhooks**: objeto `instagram`, campo
   `messages`, com a URL de callback e o token de verificação exibidos
   na tela.

As mensagens diretas chegam à caixa de entrada; comentários em
publicações, não. Não há modelos: após 24 horas da última mensagem do
cliente, somente uma pessoa pode responder, por até 7 dias (etiqueta
`HUMAN_AGENT`); o agente não. Sem App Review, um
app próprio só recebe mensagens de perfis com uma função no app. Os detalhes
do canal estão em [`specs/014-canal-instagram`](specs/014-canal-instagram/spec.md).

### TikTok (mensagens diretas de uma conta comercial)

O TikTok não entrega mensagens a um app próprio: elas chegam pelo
[Zernio](https://zernio.com), com sua conta do Zernio.

Siga [Conectar pelo Zernio](#conectar-por-zernio-instagram-messenger-o-tiktok)
em **Configurações → TikTok**. Se a conta for pessoal, o Vocero a conecta,
mas avisa nessa tela e em **Status**: o TikTok não entrega mensagens até que ela seja Business.

O que o TikTok exige, e o Vocero informa antes que você descubra por meio de uma
mensagem rejeitada:

- A conta precisa ser **TikTok Business**, registrada fora da União
  Europeia, da Suíça e do Reino Unido, com as mensagens diretas ativadas.
- **Você não inicia a conversa**: só responde a quem escreveu para você.
- Após a última mensagem do cliente, você pode enviar **até 10 mensagens em 48
  horas**. Não há modelo para reabrir a conversa: a décima primeira mensagem é rejeitada (409
  `message_limit`) até que o cliente volte a escrever.
- Cada mensagem é texto, com até 6.000 caracteres. Imagens são recebidas (como
  aviso de que chegaram), mas ainda não podem ser enviadas pelo Vocero.

Os detalhes estão em [`specs/022-canal-tiktok`](specs/022-canal-tiktok/spec.md).

## Configuração da IA

Com essa chave, seu agente (pelo CRM, em `/api/bot/llm`) e o
juiz do Laboratório podem raciocinar. Ela é configurada uma única vez no app: em **Configurações → IA**, escolha
o provedor (OpenRouter ou qualquer API compatível com a OpenAI, com sua
base URL), o modelo e cole
sua chave. «Testar conexão» faz uma chamada mínima com os dados informados antes de
salvar; «Buscar modelos» lista os modelos do provedor, e a ajuda expansível explica
onde obter a chave. Ela é armazenada criptografada (AES-256-GCM, a mesma
`ENCRYPTION_KEY` que protege o token do WhatsApp), e a tela mostra apenas
os últimos 4 caracteres.

Se o provedor rejeitar a chave (401) ou a conta ficar sem créditos (402),
a chave será pausada automaticamente; um banner avisará em todo o app, e Configurações → IA
explicará qual dos problemas ocorreu. Salvar uma nova chave —ou «Testar
conexão» com a chave salva depois de recarregar a página— reativa-a. Um 429 ou 500
do provedor não pausa nada: são falhas temporárias, não problemas com a chave.

As variáveis de ambiente continuam servindo como alternativa (para instalações
automatizadas ou quem já as usava): são utilizadas enquanto não houver nada salvo
em Configurações → IA, e as configurações salvas têm prioridade sobre elas.

```bash
OPENROUTER_API_TOKEN=sk-or-...        # alternativa: sua chave
OPENROUTER_MODEL=anthropic/claude-sonnet-4.5
OPENROUTER_JUDGE_MODEL=               # opcional: modelo diferente para o juiz do Laboratório
OPENROUTER_BASE_URL=https://openrouter.ai/api   # ou seu provedor compatível com OpenAI
```

Sem provedor, todo o restante funciona; Agente e Laboratório direcionam para
Configurações → IA. Depois, configure o comportamento e o conhecimento na
aba **Agente**, conecte seu agente e execute o **Laboratório** antes de
ativá-lo com clientes reais.

## Conformidade com as políticas da Meta

1. **Opt-in**: escreva somente para pessoas que iniciaram a conversa ou
   aceitaram receber mensagens; o Vocero respeita a janela de 24 h e bloqueia o
   texto livre fora dela.
2. **Modelos aprovados** para reabrir conversas: nada de truques para
   contornar a aprovação da Meta.
3. **O Laboratório é 100% interno**: clientes simulados nunca acessam a
   API do WhatsApp (bloqueado por projeto e verificado por testes).
4. **Sem spam nem broadcast**: o Vocero não inclui envios em massa; use-o para
   conversas reais de vendas e suporte.
5. **Dados do cliente no próprio servidor**: cada empresa hospeda sua instância; o
   token é criptografado em repouso, e os webhooks são validados por URL secreta e
   assinatura opcional.

## Perguntas frequentes sobre erros comuns

**O webhook não é verificado pela Meta** — O domínio ainda não foi resolvido, não usa
https ou o token não tem `whatsapp_business_management`: o aviso exibido ao salvar
mostra o motivo informado pela Meta. Corrija e clique em **«Registrar na Meta»** em
Configurações → WhatsApp. Se você fizer isso manualmente, copie os dados exatamente da mesma
tela.

**O webhook foi verificado, mas as mensagens não chegam** — Quase sempre, a
conexão não foi SALVA no assistente de configuração (o handshake não precisa dela, mas a
ingestão sim — o roteamento usa o Phone Number ID salvo). Acesse Configurações →
WhatsApp, salve a conexão e envie outra mensagem. Os logs da instância
exibem um aviso com o Phone Number ID desconhecido.

**As mensagens chegam, mas não são enviadas** — Verifique o status da conexão em
Configurações → WhatsApp. Se aparecer "reconectar", o token expirou: cole um
novo. No modo direto, use um token de usuário do sistema (que não expira).

**Erro 131030 ao enviar** — O número de destino não está na lista de
permitidos (números de teste da Meta) ou o formato é inválido. O Vocero já
normaliza os números do México (521 → 52).

**O agente não responde** — O Vocero não inclui um agente: há algum conectado? O
cartão «Quem responde» na tela Agente informa isso (e se o `/health`
responde). O controle está ativado? A chave de IA em Configurações →
IA está válida? A IA está ativada na conversa e não houve handoff? A janela de 24 h
está aberta? Verifique também os logs da instância e do agente.

**`ENCRYPTION_KEY` inválida na inicialização** — Deve ter exatamente 32 bytes em
base64 (44 caracteres): `openssl rand -base64 32`.

**O app inicia, mas /api/health falha** — O banco de dados não está pronto,
`DATABASE_URL` está incorreta ou alguma variável obrigatória não passou pela
validação (por exemplo, uma `ENCRYPTION_KEY` gerada incorretamente): o healthcheck
retorna 503 nos três casos. Verifique os logs (`docker compose logs app`); os logs de inicialização indicam a
variável que falhou. No repositório, `pnpm doctor` verifica os três problemas e informa
como corrigir cada um.

**O upload do logotipo ou do ícone apresenta erro, ou os anexos não aparecem** — O app não
consegue gravar em `MEDIA_DIR` (`/data/media` na imagem), e o log de
inicialização informa isso (`[boot] MEDIA_DIR … no es escribible`). Monte um volume em
`/data` (o compose já faz isso; no Coolify, use um persistent storage) e não defina
`MEDIA_DIR` na plataforma: na inicialização, o contêiner concede o volume ao usuário do
app.

**Esqueci minha senha e não consigo entrar** — O Vocero não envia e-mails (isso seria uma
dependência externa) e o registro público é fechado quando a primeira
organização é criada, então não há fluxo de "esqueci minha senha". A solução é
reescrever o hash no banco:

```bash
NEW_PASSWORD='tu-contraseña-nueva' node scripts/reset-password.mjs tu@correo.com
```

O script **não acessa o banco**: ele imprime o `UPDATE` para você colar no
console do Postgres. Execute na sua máquina, com o repositório clonado e
`pnpm install` concluído — a nova senha nunca sai dela. A senha é fornecida por variável de
ambiente e não como argumento porque um argumento fica no histórico do shell
e pode ser visto em `ps`.

O resultado deve ser `UPDATE 1`. Se retornar `UPDATE 0`, o e-mail não corresponde;
verifique com `SELECT email FROM "user";`.

## Versões

A versão em execução aparece **na parte inferior da barra lateral** (`v1.1.0 ·
8e62d0b`) e no healthcheck, permitindo confirmar uma implantação com um
`curl` sem abrir o app:

```bash
curl -s https://crm.tudominio.com/api/health
# {"ok":true,"version":"1.1.0","commit":"8e62d0b","commitVerified":true,
#  "features":{"agenda":false,"channels":["whatsapp"],"atribucion":false},
#  "mediaWritable":true}
```

`features` informa quais flags de implantação estão ativadas nesta instância
(`AGENDA`, `CHANNELS`, `ATRIBUCION`): é uma forma de confirmar que a variável
chegou ao contêiner sem iniciar uma sessão. `mediaWritable` informa se é possível gravar em `MEDIA_DIR`
(o volume `/data`); com `false`, o CRM continua respondendo
`ok` (funciona sem anexos), mas o logotipo, o ícone e os anexos não serão
salvos: verifique o volume. Essa sondagem realmente grava dados, e seu resultado fica em cache por
um minuto, para que o healthcheck não acesse o disco a cada chamada.

O número da versão vem de `package.json` e é fixado na **compilação**. O commit
só é incluído se for fornecido **durante o build**. A imagem publicada já o contém (é compilada com
o commit da tag), então, com ela, `/api/health` retorna
`"commitVerified":true` sem nenhuma configuração adicional. Se você compilar a partir do código, informe-o
como argumento de build `SOURCE_COMMIT` em cada implantação. Com docker compose,
`docker compose build --build-arg SOURCE_COMMIT=$(git rev-parse HEAD)` e
depois `docker compose up -d --pull never` (sem `--build`, que recompilaria
sem ele, e sem baixar a imagem publicada em vez da sua); no Coolify ou
em qualquer outra plataforma, o valor deve ser fornecido como argumento de build
`SOURCE_COMMIT`, não apenas como variável de ambiente. Assim, ele é incluído no
binário e aparece como `"commitVerified":true`.

Se o build não o incluir, o app exibirá o `SOURCE_COMMIT` encontrado no
ambiente durante a inicialização, mas **não o considerará confiável**: na barra lateral, aparecerá
como «commit não verificado» e, no healthcheck, como `"commitVerified":false`.
Esse valor é informado pela plataforma, não pelo código, e só é confiável se ela o
atualizar em cada implantação. Uma variável preenchida manualmente uma única vez permanece
inalterada enquanto o app é atualizado, e o selo mostraria um commit que já
não está em execução: uma variável fixa é pior do que deixá-la vazia. Se nenhum commit for informado,
somente a versão será exibida. Se um script comparar commits para confirmar uma
implantação, deve exigir `commitVerified: true`.

SemVer com foco no que importa para quem opera uma instância:

| | Quando aumenta |
|---|---|
| **Maior** (`2.0.0`) | É necessário fazer algo manualmente para atualizar: alterar uma variável de ambiente, migrar dados, reconectar algo. |
| **Menor** (`1.2.0`) | Novos recursos. Para atualizar, basta fazer o redeploy. |
| **Patch** (`1.1.1`) | Correções e ajustes. Para atualizar, basta fazer o redeploy. |

A versão fica em `package.json` e é atualizada no PR que publica a alteração,
junto com o valor padrão de `VOCERO_CRM_VERSION` em `docker-compose.yml`, a
tag da imagem em `render.yaml`, `fly.toml` e `railway.toml` (dois
testes exigem que sejam iguais) e sua entrada em [`CHANGELOG.md`](CHANGELOG.md),
que descreve o conteúdo de cada versão e como atualizar. Ao criar a tag
`vX.Y.Z`, o CI publica a imagem `ghcr.io/kevinrivm/vocero-crm:X.Y.Z`.

## Roadmap

As versões 1.5 e 2.0 têm um plano próprio:
[docs/plan-1.5-y-2.0.md](docs/plan-1.5-y-2.0.md).

- Anexos no Instagram, Messenger e TikTok (atualmente, esses canais aceitam texto; no
  WhatsApp, os anexos já são enviados e recebidos com pré-visualização).
- RAG para bases de conhecimento grandes (atualmente, o conteúdo é injetado por inteiro, com aviso sobre o tamanho).
- Personas configuráveis do Laboratório e comparação entre execuções.
- Exclusão de modelos pelo app.
- Métricas de modelos (as de vendas, origem e conversas já estão em
  Resultados).
- Broadcast com opt-in verificado.

### Antes fora do escopo; agora atrás de uma flag

**O mecanismo de agendamento já faz parte do core**, desativado por padrão. Antes,
esta seção dizia que ele ficaria fora de propósito, por dois motivos. Ambos eram válidos e
ambos mudaram; eles são mantidos aqui porque as razões importam mais do que a
conclusão:

- *"São mil linhas e uma dependência de datas em um projeto cujo argumento é
  ser leve."* — É verdade, e por isso o recurso fica atrás de `AGENDA`: uma instância que
  não agenda não vê telas, rotas nem instruções de agendamento no
  prompt do agente, e não precisa fornecer nenhuma credencial. O custo fica para quem
  ativa o recurso. A questão da dependência de datas não se aplica mais: o mecanismo não adicionou
  nenhuma (os cálculos de fuso usam `Intl` da plataforma).
- *"O estado dos horários oferecidos pertence à conversa, ou seja, ao
  agente, não ao CRM."* — Pertencia ao agente enquanto o CRM não oferecia essa
  garantia. Ao oferecê-la, é o CRM que precisa poder comprová-la: com a
  memória no lado do cliente, qualquer cérebro conectado por `/api/bot/*`
  poderia reservar um horário que nunca foi oferecido, e o CRM aceitaria. Agora,
  a regra é inviolável por construção e vale igualmente para o Nea, para seu bot e
  para qualquer outro.

O que continua fora do escopo, de propósito: **ler calendários externos** para descontar
disponibilidade. O mecanismo calcula com os próprios dados, e compromissos externos são
registrados com bloqueios manuais; incluir o provedor nesse fluxo acoplaria a
latência e as falhas dele à tela mais usada.

## Stack

Next.js 15 (App Router) + React 19 · TypeScript estrito · PostgreSQL +
Drizzle ORM · Better Auth · Tailwind CSS · SSE (sem WebSockets) · Docker
multi-stage com migrações na inicialização. Projetado para que uma agência possa modificá-lo
com um assistente de IA: specs e decisões de projeto em
[`specs/`](specs/), guia de modificações em [`AGENTS.md`](AGENTS.md) (e instruções específicas do Claude Code em [`CLAUDE.md`](CLAUDE.md)).

## Licença

[MIT](LICENSE) — use, venda instalado, modifique. Se for útil para você, uma ⭐ no
repositório ajuda mais pessoas a encontrá-lo.

## Créditos

Criado por [Kevin Belier](https://www.youtube.com/@KevinBelier). Quer
aprender a se tornar Meta Tech Provider e monetizar sua agência de IA?
Participe da [VIBE Community](https://www.skool.com/vibe-community-vip). Os padrões
de produção (webhook assinado, ingestão idempotente, criptografia de tokens) vêm
de um projeto de referência privado em produção, portados e simplificados
para este repositório.
