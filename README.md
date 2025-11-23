#App de organização de finanças pessoais com Vibe Coding | Gabriel Silva.

## O que é Vibe Coding

**Vibe Coding** é uma forma leve e criativa de desenvolver com IA, baseada em **conversas naturais e bem estruturadas**. Você não precisa escrever código linha por linha. Em vez disso, aprende a **guiar a IA** descrevendo suas ideias de forma clara, com **intenção e contexto**. Em outras palavras:


PRD refinado no chatGPT
``` markdown
1. Contexto
O aplicativo propõe uma forma simples e natural de organizar finanças pessoais usando conversas em linguagem natural. O objetivo é eliminar a complexidade encontrada em apps tradicionais, tornando o controle financeiro acessível para qualquer pessoa, especialmente iniciantes e idosos.

2. Problema
Muitas pessoas desistem de usar aplicativos financeiros porque exigem muita entrada manual, possuem interfaces confusas e não se adaptam ao comportamento real do usuário. A solução proposta é um app orientado por conversa, com automações inteligentes e design acessível.

3. Público-Alvo
- Pessoas de qualquer idade que desejam começar a organizar suas finanças.
- Idosos (+50) e iniciantes no assunto.
- Usuários que preferem conversar em vez de preencher formulários extensos.

4. Proposta de Valor
Oferecer uma experiência de organização financeira simples por meio de um chat intuitivo, com automação de registros, metas e dicas personalizadas.

5. Funcionalidades-Chave

5.1 Registro de gastos via chat
- O usuário informa gastos em linguagem natural.
- O sistema interpreta valor, categoria e data automaticamente.
- Regras de validação de valores:
  - O sistema deve identificar números escritos como "45", "45,90", "45.90" e valores por extenso como "trinta reais".
  - O sistema deve sempre converter o valor para número decimal no formato padrão do banco de dados (ex: 45.90).
  - Se houver mais de um número na frase, o sistema deve escolher o número mais provável como valor com base no contexto.
  - Caso o valor não possa ser interpretado, o sistema pergunta ao usuário: "Qual foi o valor do gasto?"
  - Nenhum gasto pode ser salvo sem validação do valor numérico.
  - Campos obrigatórios para salvar no banco: valor, data, categoria.

5.2 Classificação automática
- IA classifica cada gasto em categorias adequadas.
- O usuário pode corrigir quando desejar.

5.3 Criação e acompanhamento de metas
- O usuário pode solicitar metas diretamente pelo chat.
- O app cria metas automaticamente e leva o usuário à página correspondente.

5.4 Agente Financeiro
- Envia dicas personalizadas.
- Aponta tendências de gastos.
- Sugere ações práticas de economia.

5.5 Relatórios simples
- Relatórios visuais claros e objetivos.
- Cards e gráficos fáceis de interpretar.

5.6 Página de extrato simples
- Lista de transações essencial e organizada.
- Opção de filtros básicos.

5.7 Design universal
- Interface acessível para o maior número possível de pessoas.
- Textos legíveis, botões grandes, navegação direta.
- Ideal para idosos ou pessoas com pouca experiência tecnológica.

5.8 Tela de login e senha conectada ao banco de dados
- Tela simples de autenticação com campos de email e senha.
- Função de criação de conta.
- Armazenamento seguro das credenciais no banco de dados.
- Login necessário para acessar o chat e as funcionalidades do app.

6. Entregável da IA (Lovable)

6.1 MVP
- Telas principais:
  - Tela de login e criação de conta
  - Chat inicial
  - Tela de metas
  - Tela de extrato
  - Tela de relatórios
  - Configurações
- Backend mínimo:
  - Registro e classificação de transações
  - Modelo simples de metas
  - Banco de dados básico
  - Funções iniciais do Agente Financeiro
  - Sistema de autenticação (login, cadastro e validação)
- Fluxos essenciais:
  - Autenticar usuário via login
  - Registrar gasto via chat
  - Criar meta via chat
  - Visualizar extrato e relatórios simples

6.2 Validação Inicial
- Testar se usuários conseguem registrar gastos via chat sem instruções.
- Validar clareza da página de extrato.
- Testar usabilidade com pessoas acima de 50 anos.
- Validar utilidade das sugestões do Agente Financeiro.
- Testar se o fluxo de login e criação de conta é simples e compreensível.

7. Backend — Especificação Completa (adicionada)

7.1 Visão geral técnica mínima
- Arquitetura: API REST (ou GraphQL se preferir) + banco relacional (Postgres recomendado) + serviço de processamento de linguagem/IA para classificação e extração.
- Linguagem/backend: Node.js, Python (FastAPI) ou similar — escolha simples e comum para Lovable.
- Autenticação: JWT com refresh token.
- Criptografia de senha: bcrypt (salt work factor razoável).
- Ambiente: variáveis de ambiente para segredos (DB URL, JWT secret).
- Logs e monitoramento básicos (console + arquivo/serviço de logs).

7.2 Modelo de dados (tabelas mínimas)
-- users
- id: uuid (PK)
- email: string, unique, not null
- password_hash: string, not null
- name: string, nullable
- created_at: timestamp
- updated_at: timestamp
- language: string, default 'pt-BR' (opcional)
- last_login_at: timestamp, nullable

-- transactions
- id: uuid (PK)
- user_id: uuid (FK -> users.id), not null
- amount: decimal(12,2), not null
- currency: string (ex: "BRL"), default 'BRL'
- category: string, not null (categoria padrão se não reconhecida: "Outros")
- description: string, nullable (texto livre do usuário)
- original_text: string, nullable (mensagem do chat que originou a transação)
- date: date (data do gasto), not null
- created_at: timestamp
- updated_at: timestamp

-- metas
- id: uuid (PK)
- user_id: uuid (FK -> users.id), not null
- title: string, not null
- target_amount: decimal(12,2), not null
- current_amount: decimal(12,2), default 0.00
- period: string (ex: 'mensal', 'única'), nullable
- due_date: date, nullable
- created_at: timestamp
- updated_at: timestamp
- status: string (ex: 'ativa','concluida','pausada'), default 'ativa'

-- sessions (opcional para refresh tokens ou auditoria)
- id: uuid (PK)
- user_id: uuid (FK)
- refresh_token_hash: string
- expires_at: timestamp
- created_at: timestamp
- ip_address: string, nullable
- user_agent: string, nullable

7.3 Regras e validações do backend
- Validação de entrada:
  - amount: obrigatório, decimal >= 0.01.
  - date: obrigatório, deve aceitar formatos ISO (YYYY-MM-DD) e datas relativas inferidas (ex: "ontem" -> converter).
  - category: se não houver correspondência automática, gravar "Outros" e marcar para revisão.
  - original_text: salvar sempre que extraído do chat.
- Conversão de valores:
  - Aceitar vírgula ou ponto como separador decimal; converter para decimal com ponto no DB.
  - Permitir valores por extenso: usar NLU para converter "trinta e cinco reais" -> 35.00.
  - Em frases com múltiplos números, priorizar o número que for identificado como valor monetário (heurística: token adjacente a palavras como "reais", "R$", "gastei", "custou", "pago").
- Nenhuma transaction é criada sem amount, date e category válidos.
- Em caso de ambiguidade, retornar mensagem de confirmação via chat antes de persistir.

7.4 Endpoints mínimos (REST)
- POST /auth/register
  - body: { email, password, name? }
  - validação: email format, senha mínima (ex: 8 chars)
  - resposta: 201 + { user: {id,email,name}, token, refreshToken }

- POST /auth/login
  - body: { email, password }
  - validação: checar password_hash; gerar token JWT + refreshToken

- POST /auth/refresh
  - body: { refreshToken }
  - validação: checar sessão/refresh hash

- POST /transactions (autenticado)
  - body: { original_text?, amount?, date?, category?, description? }
  - comportamento: se original_text presente, servidor acionará NLU para extrair amount, date, category; aplicar validações e responder com confirmação se necessário.
  - resposta: 201 + transação criada (ou 202 + pending se precisa de confirmação)

- GET /transactions (autenticado)
  - query params: ?from=YYYY-MM-DD&to=YYYY-MM-DD&category=&limit=&offset=
  - resposta: listagem paginada

- GET /transactions/:id (autenticado)
  - resposta: transação

- PUT /transactions/:id (autenticado)
  - corpo: atualizar campos permitidos (category, description, date, amount)
  - validações aplicáveis

- POST /goals (metas) (autenticado)
  - body: { title, target_amount, period?, due_date? }
  - resposta: 201 + meta criada

- GET /goals (autenticado)
  - listagem de metas do usuário

- PUT /goals/:id (autenticado)
  - atualizar current_amount (quando houver aporte) ou status

- POST /agent/suggestion (autenticado)
  - body: { context? }
  - resposta: { suggestions: [...] } — endpoint que o front chama para renderizar dicas (pode ser simples no MVP: regra baseada em médias de gastos)

- POST /classify (autenticado) (opcional para microserviço)
  - body: { text }
  - resposta: { amount?, date?, category?, confidence_score }

7.5 Fluxos importantes
- Fluxo de cadastro/login:
  - Usuário cria conta -> server cria user + password_hash -> gera JWT (short lived) e refresh token (stored hashed).
  - Antes de qualquer ação sensível, verificar token.
- Fluxo de registrar gasto via chat:
  - Front envia mensagem original_text e token -> backend chama NLU/classificador -> extrai amount/date/category -> aplica validações -> se ambíguo, retorna payload com campo needs_confirmation: true e texto de confirmação ao front -> front mostra pergunta ao usuário -> na confirmação, backend persiste.
- Fluxo de criar meta via chat:
  - Mesmo fluxo de NLU: extrair target_amount, period/due_date -> criar meta -> responder com rota para abrir a tela da meta.
- Fluxo do Agente Financeiro:
  - Serviço agendado (cron diário ou trigger on-demand) que analisa transações recentes e gera recomendações simples (ex: "Você gastou 30% a mais com alimentação neste mês, considere reduzir...").
  - Dicas salvas em tabela de notificações ou retornadas via endpoint /agent/suggestion.

7.6 Regras de segurança e privacidade
- Senhas nunca em texto; usar bcrypt e nunca armazenar refresh token em texto (guardar hash).
- JWT secret forte e rotacionável.
- Rate limiting básico nos endpoints de autenticação e NLU.
- Armazenamento de logs sem dados sensíveis (ou mascarar).
- Backups diários do banco de dados (ou conforme infra).
- Conformidade básica com LGPD: permitir exclusão de conta e exportação de dados do usuário.

7.7 Observações operacionais para Lovable
- Se Lovable gerar o NLU/classificador interno, fornecer endpoint /classify com resposta de confidence_score para possibilitar confirmação quando baixa confiança.
- No MVP, classificador rule-based + regex para valores + expressão para datas (ontem, hoje) é aceitável; documentar limitações.
- Documentar exemplos de mensagens e como serão extraídas: ex.: "Gastei R$ 45 no mercado ontem" -> amount:45.00 date:YYYY-MM-DD category:Alimentação.
- Fornecer responses de fallback claros: "Não consegui identificar o valor. Qual foi o valor do gasto?"

7.8 Testes e critérios de aceitação
- Testes unitários para validação de parsing de valores (exemplos com vírgula, ponto, por extenso).
- Testes de integração cobrindo registro via chat -> persistência no DB.
- Testes de segurança: tentativa de login com senha errada, uso de refresh token inválido.
- Critério para aceitar MVP: usuário autenticado consegue criar pelo menos 5 transações via chat em menos de 10 minutos com pelo menos 80% de parsing automático correto (em testes com usuários reais).

8. Conceitos Importantes

PRD (Product Requirements Document):
Documento que descreve o que o produto deve fazer, sua finalidade, problemas resolvidos, público-alvo, funcionalidades e escopo do MVP. Serve para alinhar visão e orientar o desenvolvimento.

Vibe Coding:
Abordagem usada em ferramentas como Lovable, onde o desenvolvedor cria software conversando com a IA. Em vez de escrever código manualmente, descreve intenções e a IA transforma em telas, lógica e componentes. O fluxo é iterativo, rápido e guiado pela sensação de construir de forma natural e contínua.
```markdown

<img width="1920" height="911" alt="image" src="https://github.com/user-attachments/assets/c99af17d-06aa-4f5b-a0d2-8a464737de70" />
<img width="1920" height="911" alt="image" src="https://github.com/user-attachments/assets/3f527ebc-7a6b-41ba-9c01-b9ee6910237b" />
<img width="1920" height="988" alt="image" src="https://github.com/user-attachments/assets/f22fc157-7c40-46b9-a900-dac732e8ecf6" />


- Dar comando simples no chat para organizar finanças, cria metas automatica ou manualmente.
  - O que funcionou bem?  
      A interface funcionou muito bem e fluída, as transições de páginas.
  - O que não funcionou como o esperado?  
      Em questão do Extrato ele aparece só como gasto e cria a categoria apenas no extrato e não envia para o relátorio.
  - O que aprendeu sobre conversar com IAs?
      Aprendi que com conhecimento e base sólida, podemos criar muitas coisas e ajudar diversas Instituições, vou resolver o problema do   Relatorio e extrato que não fuincionou tão bem, esperar os creditos voltarem ksksks.

## 💬 Conclusão

Vibe Coding é sobre clareza, curiosidade e criatividade, não sobre perfeição técnica. O verdadeiro objetivo aqui é aprender a pensar junto com a IA, transformando ideias em conceitos reais e enxergando a tecnologia como uma extensão do seu raciocínio criativo. Cada interação é um experimento, quanto mais clara for sua intenção, mais surpreendente será o resultado.
