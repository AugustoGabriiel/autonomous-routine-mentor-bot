# 🤖 Autonomous Daily Routine & Career Mentor Bot (n8n + Gemini + Telegram)

Agente autônomo de rotina e disciplina profissional construído no **n8n**, integrado com a **API do Google Gemini** e com interface em tempo real via **Telegram**.

O sistema opera em duas frentes:
1. **Disparos Proativos (Outbound):** Notificações orientadas por regras Cron que antecipam o início de cada bloco de trabalho com instruções de *como* e *onde* executar as tarefas.
2. **Interação Reativa (Inbound):** Interpretação em linguagem natural das atualizações enviadas pelo usuário, auditoria de pendências e persistência do histórico em banco de dados interno.

---

## 🚀 Funcionalidades Principais

- **Orquestração de Rotina Diária:** Disparos programados respeitando o fuso horário local (`America/Fortaleza`), cobrindo blocos de estudos técnicos, treino, projetos práticos, busca de vagas e expansão intelectual.
- **Raciocínio com LLM (Gemini):** O agente gera mensagens contextualizadas, alternando sugestões práticas de leitura, cursos técnicos e palestras sem repetição mecânica.
- **Tool Calling & Persistência de Dados:** Uso de ferramentas (`consultar_registro_hoje` e `registrar_bloco`) conectadas a Data Tables do n8n para auditar o progresso diário e cobrar tarefas pendentes.
- **Segurança e Filtragem de Mensagens:** Validação de `chat_id` e conteúdo textual antes do acionamento dos nós de IA para prevenir consumo indevido de tokens.
- **Resiliência em Produção:** Configuração de retentativas automáticas (`retryOnFail`) para mitigar instabilidades temporárias de rede e integração com workflow dedicado para captura de erros (`errorWorkflow`).
- **Memória de Conversação:** Manutenção de contexto com janela deslizante de 20 interações.

---

## 🛠️ Tecnologias e Ferramentas

- **n8n:** Orquestração de fluxos e lógica de nós.
- **Google Gemini API:** Modelo de linguagem para geração, raciocínio e execução de ferramentas.
- **Telegram Bot API:** Mensageria móvel instantânea com alertas push sonoros.
- **JavaScript (ES6):** Tratamento de fusos horários e injeção dinâmica de contexto de horário.
- **n8n Data Tables:** Persistência relacional do status diário de cada bloco.

---

## 📋 Arquitetura do Sistema

```text
[Cron Agendador] ────► [Code: Contexto do Horário] ────► [Agente Gemini] ────► [Telegram Send]
                                                               ▲
                                                      [Memória & Tools]
                                                               ▲
[Telegram Trigger] ──► [Filter: Auth Chat ID] ────────────────┘
```
## ⚙️ Instruções de Implantação

1. Clone este repositório ou baixe o arquivo `workflow.json`.
2. No seu n8n, crie um novo workflow e importe o arquivo `workflow.json`.
3. Configure as seguintes credenciais no painel do n8n:
   - **Telegram API:** Token obtido via `@BotFather`.
   - **Google Gemini API:** Chave obtida via Google AI Studio.
4. No nó `Apenas Meu Chat com Texto` e no nó `Enviar Alerta Telegram`, atualize o valor do campo `Chat ID` para o seu identificador numérico pessoal.
5. Crie uma Data Table com o nome `rotina_registro` contendo as seguintes colunas de texto:
   - `dia` (YYYY-MM-DD)
   - `bloco` (1 a 5)
   - `status` (feito, parcial, nao_feito)
   - `observacao` (resumo em texto)
6. Ative o workflow no n8n.
