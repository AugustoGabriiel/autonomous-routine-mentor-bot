# Autonomous Routine Mentor Bot 🤖⚡

> Um agente autônomo modular de produtividade e mentoria técnica construído no **n8n**, integrado ao **Google Gemini** e operado via **Telegram**, com persistência de dados em **n8n Data Tables**.

---

## 📌 Visão Geral

O **Autonomous Routine Mentor Bot** é um sistema desenvolvido para resolver um dos maiores desafios em transições de carreira e rotinas de aprendizado autônomo: a **quebra de consistência** e a **falta de direcionamento prático**. 

Diferente de alarmes convencionais ou bots estáticos de checklist, este projeto funciona como um **mentor sênior pedagógico**:
- Emite alertas proativos nos horários exatos de transição de blocos.
- Fornece direcionamentos detalhados de **"onde fazer"** (plataformas acessíveis) e **"como fazer"** (passo a passo do zero, com mentalidade para iniciantes).
- Registra e audita a execução diária em banco de dados interno (*Data Tables*).
- Devolve feedbacks reflexivos contextualizados com Customer Experience (CX), Suporte Técnico (N2/N3) e automação de processos.

---

## 🏗️ Arquitetura da Solução

O fluxo opera em dois circuitos integrados no n8n:

```
[Circuito Proativo - Cron / Schedule Trigger]
  └──> [Definir Bloco (JS)] ──> [Gemini Gerar Alerta] ──> [Telegram Bot (Mensagem Proativa)]
                                        │
                                        ├── [Tool: consultar_registro_hoje]
                                        └── [Memory: Conversation Memory]

[Circuito Reativo - Telegram Trigger]
  └──> [Filtro de Segurança] ──> [Gemini Assistant] ──> [Telegram Bot (Resposta/Feedback)]
                                        │
                                        ├── [Tool: registrar_bloco (Upsert)]
                                        ├── [Tool: consultar_registro_hoje]
                                        └── [Memory: Conversation Memory]
```

### Componentes Técnicos:
* **Orquestrador:** n8n (Cloud ou Self-Hosted).
* **Modelo de Linguagem (LLM):** Google Gemini (`models/gemini-3.5-flash-lite`) via Google AI Studio.
* **Interface de Mensageria:** Telegram Bot API.
* **Armazenamento de Estado:** n8n Data Tables (`rotina_registro`).
* **Lógica de Agendamento:** Cron Expressions calibradas para o fuso `America/Fortaleza` (Segunda a Sexta-feira).

---

## ⏰ Matriz da Rotina (Segunda a Sexta)

| Horário | Bloco | Atividade Principal | Foco Prático & Ferramentas |
| :--- | :---: | :--- | :--- |
| **07:55** | — | *Alerta Prévio do Bot* | Instruções didáticas do Bloco 1 |
| **08:00 – 09:15** | **Bloco 1** | Estudos Técnicos | APIs, Webhooks, HTTP Status, JSON, ReqRes, n8n |
| **09:25** | — | *Alerta Prévio do Bot* | Preparação física e pausa de telas |
| **09:30 – 11:30** | **Bloco 2** | Treino & Saúde | Musculação, respiração e hidratação |
| **11:30 – 15:30** | — | **Janela Livre** | Almoço, descanso e compromissos pessoais |
| **15:25** | — | *Alerta Prévio do Bot* | Chamada para execução técnica |
| **15:30 – 17:30** | **Bloco 3** | Projetos Práticos & Portfólio | Construção de automações no n8n e documentação no GitHub |
| **17:25** | — | *Alerta Prévio do Bot* | Orientação de busca estratégica |
| **17:30 – 19:00** | **Bloco 4** | Mercado & Prospecção | Análise de vagas (Suporte N2/N3, CX Ops) no LinkedIn Jobs |
| **18:55** | — | *Alerta Prévio do Bot* | Fechamento reflexivo do dia |
| **19:00 – 20:00** | **Bloco 5** | Expansão Intelectual | Livros (*Hábitos Atômicos*, *The Effortless Experience*) / Talks |

*Sábados e domingos são 100% livres de disparos automáticos.*

---

## 🧠 Engenharia de Prompt & Diretrizes Pedagógicas

O agente foi calibrado com diretrizes comportamentais específicas no **System Prompt**:

1. **Mentalidade Zero Absoluto (Zero Assunção de Domínio Prévio):**  
   O agente nunca exige ferramentas com comandos genéricos ou técnicos sem contextualização. Ele explica em 1 frase o que a ferramenta é, fornece a URL web direta e instrui o primeiro clique sem complexidade.
2. **Feedback Reflexivo (Estilo Professor/Mentor):**  
   Ao receber o relato de conclusão de uma atividade, o bot evita mensagens vazias (ex: *"Parabéns, continue assim"*). Ele conecta o aprendizado prático a conceitos de Suporte Técnico Avançado, governança e experiência do usuário.
3. **Auditoria de Pendências:**  
   Antes de emitir o alerta de um novo bloco, o bot consulta a tabela do dia e cobra eventuais blocos em aberto com firmeza e empatia.

---

## 🗄️ Estrutura da Data Table (`rotina_registro`)

O n8n gerencia o estado diário através de uma Data Table com a seguinte modelagem:

| Coluna | Tipo | Descrição | Chave de Correspondência (Upsert) |
| :--- | :--- | :--- | :---: |
| `dia` | String | Data no formato `YYYY-MM-DD` | Sim |
| `bloco` | String | Identificador do bloco (`1`, `2`, `3`, `4`, `5`) | Sim |
| `status` | String | Situação (`feito`, `parcial`, `nao_feito`) | Não |
| `observacao` | String | Resumo extraído pelo LLM do relato do usuário | Não |

---

## 🚀 Como Replicar este Projeto

### 1. Pré-requisitos
* Uma conta ou instância ativa do [n8n](https://n8n.io).
* Um bot criado no Telegram via [@BotFather](https://t.me/BotFather) (guarde o `API Token`).
* Seu `Chat ID` pessoal do Telegram (obtido via [@userinfobot](https://t.me/userinfobot)).
* Uma chave de API gratuita do Google AI Studio ([Google Gemini](https://aistudio.google.com/)).

### 2. Configuração no n8n
1. No painel lateral do n8n, acesse **Data Tables** e crie uma tabela chamada `rotina_registro` com as colunas: `dia`, `bloco`, `status` e `observacao`.
2. Em **Credentials**, adicione:
   * **Telegram API:** insira o token do seu bot.
   * **Google Gemini (PaLM) Api:** insira a chave de API do Gemini.
3. Baixe o arquivo [`workflow.json`](./workflow.json) deste repositório.
4. No menu superior do n8n, selecione **Import from File** e carregue o `workflow.json`.
5. Abra os nós `Apenas Meu Chat com Texto` e `Enviar Alerta Telegram` e substitua o ID numérico pelo seu Chat ID pessoal.
6. Nos nós de tool `registrar_bloco` e `consultar_registro_hoje`, selecione a sua Data Table `rotina_registro`.
7. Ative o workflow no canto superior direito (**Active: ON**).

---

## 📄 Licença

Distribuído sob a licença MIT. Consulte o arquivo `LICENSE` para mais detalhes.
