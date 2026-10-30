# Construção de aplicativos com IA

## Objetivo

Implementar a aplicação indicada pelo apresentador para uma demonstração com alunos calouros de engenharia de software. Somente uma das três aplicações será construída.

A escolha da aplicação, a votação e a distribuição do tempo são responsabilidade do apresentador. O agente recebe a opção escolhida e segue diretamente seu contexto, sem organizar votação, controlar o cronograma ou reduzir requisitos por tempo.

Atue como um engenheiro de software sênior, usando explicações simples para iniciantes. Explique como interface, API e banco de dados se conectam.

## Tecnologias e execução

- Backend: Python com FastAPI e SQLAlchemy. O esquema é criado na subida da API.
- Frontend: Me pergunte na hora, e sugira opções.
- Banco de dados: PostgreSQL.
- Executar frontend, backend e banco em Docker Compose.
- Iniciar com um único comando: `docker compose up --build`.
- Criar as tabelas e os dados fictícios automaticamente, sem duplicar os exemplos ao reiniciar.
- Usar volumes para persistência do banco e dos arquivos enviados.
- Telegram é obrigatório no gestor de filas, conforme seu contexto; não é necessário nas outras aplicações.

## Orientações de implementação

- Implementar somente a aplicação indicada e seguir seu arquivo de contexto.
- Evitar qualquer tipo de login nas três aplicações. As telas abrem direto, sem usuário, senha, sessão ou rota protegida, para a apresentação não depender de cadastro nem de credencial.
- Os requisitos funcionais descritos nestes contextos são obrigatórios. Não há outro documento de especificação no repositório.
- Seguir as tabelas, chaves estrangeiras e cardinalidades já estabelecidas em cada contexto.
- Usar chaves primárias inteiras, datas com fuso horário e valores monetários decimais.
- Priorizar simplicidade: duas telas principais, detalhes em seções ou modais e atualização por consultas periódicas à API quando necessário.
- Usar botões simples para as transições de estado e cores para facilitar o acompanhamento.
- Resolver decisões secundárias com escolhas simples. Perguntar somente quando faltar uma informação indispensável.
- Recursos em “Complementos opcionais” são sugestões novas, não substituem requisitos obrigatórios e não precisam ser implementados sem solicitação.
- Fazer uma verificação manual breve do fluxo principal. Não criar etapas extensas de auditoria de segurança ou muitos testes para esta demonstração.
- Usar dados fictícios e explicar brevemente as decisões relevantes.

## Planejamento

Antes de criar arquivos, monte um plano curto e siga para a implementação na mesma tarefa, sem aguardar aprovação. O plano tem só estes itens:

- serviços do Docker Compose;
- telas, ações do usuário e operações da API;
- transições de estado;
- dados inseridos na inicialização;
- ordem: banco e dados iniciais, API, interface, roteiro de verificação.

Decisões já fechadas, que não precisam ser rediscutidas:

- Sem login, usuário, senha, sessão ou permissão. A apresentação entra direto nas telas da aplicação.
- Textos da interface em português. Datas em ISO-8601 com fuso e horários exibidos em America/Sao_Paulo.
- API REST em JSON. Nomes de rotas ficam a cargo da implementação.
- Serviços `db`, `api` na porta 8000 e `web` na porta 8080. O gestor de filas acrescenta o serviço `worker`, com a mesma imagem da API e outro comando.
- Volumes para os dados do PostgreSQL e para os arquivos enviados. Dados fictícios só entram se ainda não existirem.
- CORS liberado para esta demonstração. A interface chama a API por uma URL configurável.
- Variáveis: `DATABASE_URL` e, somente no gestor de filas, `TELEGRAM_BOT_TOKEN` no `.env` dessa pasta, fora do Git.
- Não implementar os complementos opcionais.
- Se uma escolha não mudar o roteiro nem as tabelas, adotar a opção mais simples e mencioná-la em uma frase.

## Critério de conclusão

A aplicação deve iniciar pelo Docker Compose, persistir os dados e executar o roteiro descrito no contexto específico, incluindo seus requisitos obrigatórios. Informar configurações externas necessárias e qualquer funcionalidade que não pôde ser verificada.
