# Construção de aplicativos com IA

## Objetivo

Implementar a aplicação indicada pelo apresentador para uma demonstração com alunos calouros de engenharia de software. Somente uma das três aplicações será construída.

A escolha da aplicação, a votação e a distribuição do tempo são responsabilidade do apresentador. O agente recebe a opção escolhida e segue diretamente seu contexto, sem organizar votação, controlar o cronograma ou reduzir requisitos por tempo.

Atue como um engenheiro de software sênior, usando explicações simples para iniciantes. Explique como interface, API e banco de dados se conectam.

## Tecnologias e execução

- Backend: Python com FastAPI.
- Frontend: ferramentas executadas com Node.js; adotar uma solução simples e consistente.
- Banco de dados: PostgreSQL.
- Executar frontend, backend e banco em Docker Compose.
- Iniciar com um único comando: `docker compose up --build`.
- Criar as tabelas e os dados fictícios automaticamente, sem duplicar os exemplos ao reiniciar.
- Usar volumes para persistência do banco e dos arquivos enviados.
- Telegram é obrigatório no gestor de filas, conforme seu contexto; não é necessário nas outras aplicações.

## Orientações de implementação

- Implementar somente a aplicação indicada e seguir seu arquivo de contexto.
- Os requisitos funcionais descritos são obrigatórios, inclusive os provenientes dos arquivos originais do repositório.
- Seguir as tabelas, chaves estrangeiras e cardinalidades já estabelecidas em cada contexto.
- Usar chaves primárias inteiras, datas com fuso horário e valores monetários decimais.
- Priorizar simplicidade: duas telas principais, detalhes em seções ou modais e atualização por consultas periódicas à API quando necessário.
- Usar botões simples para as transições de estado e cores para facilitar o acompanhamento.
- Resolver decisões secundárias com escolhas simples. Perguntar somente quando faltar uma informação indispensável.
- Recursos em “Complementos opcionais” são sugestões novas, não substituem requisitos obrigatórios e não precisam ser implementados sem solicitação.
- Fazer uma verificação manual breve do fluxo principal. Não criar etapas extensas de auditoria de segurança ou muitos testes para esta demonstração.
- Usar dados fictícios e explicar brevemente as decisões relevantes.

## Critério de conclusão

A aplicação deve iniciar pelo Docker Compose, persistir os dados e executar o roteiro descrito no contexto específico, incluindo seus requisitos obrigatórios. Informar configurações externas necessárias e qualquer funcionalidade que não pôde ser verificada.
