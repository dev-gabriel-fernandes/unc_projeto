# Gestor de filas e agendamentos integrado ao Telegram

## Objetivo

Gerenciar atendimentos médicos com ficha, horário estimado, agenda e lembretes pelo Telegram, seguindo o [contexto geral](../skill/context.md).

## Requisitos obrigatórios

### Emissão e acompanhamento

- Cadastrar paciente e emitir uma ficha com número de ticket e horário estimado do atendimento.
- Associar a ficha a um agendamento e ao profissional selecionado.
- Exibir o horário previsto e o estado do atendimento.
- Usar o horário agendado como estimativa inicial; identificar que se trata de uma previsão.

### Tela do atendente

- Identificar o paciente pelo CPF e registrar nome e telefone.
- Agenda por dia e hora.
- Seleção de profissional por dropdown.
- Campo descritivo de sintomas e informações como pressão e oxigenação.
- Painel estilo Jira com cores por estado: Agendado, Confirmado, Em atendimento, Concluído e Cancelado.
- Ações para iniciar e concluir atendimento.
- Exibir se o aviso de confirmação e o lembrete foram enviados, além da resposta do paciente.
- Ordenar os atendimentos ativos pelo horário previsto.

### Telegram

- Vincular cada paciente ao seu chat_id do Telegram, obtido após ele iniciar uma conversa com o bot. Não usar telefone como chat_id.
- Enviar pedido de confirmação um dia antes do atendimento.
- Permitir resposta pelos botões Confirmar e Cancelar.
- Confirmar mantém o horário reservado; cancelar altera o agendamento para Cancelado.
- Enviar lembrete 30 minutos antes do horário previsto.
- Não enviar avisos para atendimentos cancelados ou concluídos.
- Registrar envio bem-sucedido somente após confirmação da API do Telegram e evitar reenvios em verificações posteriores.
- Executar verificação periódica e recebimento de respostas em um único processo dedicado, usando long polling, sem exigir webhook público.
- Configurar o token do bot pela variável `TELEGRAM_BOT_TOKEN` no arquivo `.env` desta pasta. Esse arquivo fica de fora do Git. Se faltar token ou vínculo do paciente, mostrar a pendência; não simular envio como se fosse real.

## Tabelas e relacionamentos preestabelecidos

Todas as tabelas têm `id` como chave primária.

| Tabela | Campos |
| --- | --- |
| pacientes | cpf único, nome, telefone, telegram_chat_id opcional |
| profissionais | nome, especialidade opcional |
| status | nome único, ordem |
| agendamentos | paciente_id (FK pacientes.id), profissional_id (FK profissionais.id), status_id (FK status.id), numero_ticket único, horario_previsto, sintomas_descricao, aviso_enviado_em opcional, lembrete_enviado_em opcional, resposta_confirmacao opcional (CONFIRMADO ou CANCELADO), resposta_em opcional, inicio_atendimento_em opcional, conclusao_em opcional, criado_em |

Relacionamentos:

- pacientes 1:N agendamentos.
- profissionais 1:N agendamentos.
- status 1:N agendamentos.
- Pacientes e profissionais se relacionam N:N ao longo do tempo por agendamentos.

Regras:

- Uma ficha corresponde a um agendamento; não criar tabela de senhas separada.
- Criar os estados fixos na inicialização.
- Derivar “recebeu aviso?” e “recebeu lembrete?” das respectivas datas de envio.
- Impedir dois agendamentos ativos no mesmo horário para o mesmo profissional.
- Permitir apenas um atendimento em andamento por profissional.
- Aceitar confirmação ou cancelamento pelo Telegram apenas enquanto o agendamento estiver Agendado ou Confirmado.
- Usar datas com fuso e apresentar os horários em America/Sao_Paulo.
- Verificar avisos vencidos ainda não enviados: aviso quando faltarem até 24 horas, lembrete quando faltarem até 30 minutos, sempre antes do atendimento. Isso também atende agendamentos criados próximos ao horário.
- Carregar pacientes fictícios, profissionais e agendamentos de exemplo.

## Para o planejamento

- Sem login, usuário ou senha. A apresentação abre direto na tela do atendente: paciente, agenda do dia, ficha e quadro. O Telegram não é uma tela.
- Sintomas, pressão e oxigenação ficam em `sintomas_descricao`. Não criar outras colunas.
- `numero_ticket` é único, gerado ao agendar e exibido com `horario_previsto` identificado como previsão.
- Estados nesta ordem: Agendado, Confirmado, Em atendimento, Concluído e Cancelado. O agendamento nasce Agendado.
- Confirmar leva a Confirmado. Cancelar leva a Cancelado. Os dois só valem a partir de Agendado ou Confirmado.
- Iniciar leva Agendado ou Confirmado para Em atendimento e exige que o profissional não tenha outro atendimento em andamento. Concluir leva Em atendimento para Concluído.
- Concluído e Cancelado não mudam de estado e não recebem Telegram.
- Ativo, para o conflito de horário, é tudo o que não está Concluído nem Cancelado. O conflito é o mesmo profissional com o mesmo `horario_previsto`.
- O `worker` consulta os prazos a cada 30 segundos e usa long polling no mesmo processo. Confirmar e Cancelar chegam por botões do Telegram.
- No `/start`, o bot responde com o `chat_id`. O atendente grava esse valor no paciente. Sem token ou sem `chat_id`, a tela mostra a pendência e nada é marcado como enviado.
- A tela atualiza os dados da API a cada 5 segundos.
- CPF com 11 dígitos, apenas normalizado, sem validar dígitos verificadores.
- Na inicialização: profissionais, pacientes e agendamentos em estados diferentes. Exemplos no passado ficam Concluído ou Cancelado, ou já com as datas de envio preenchidas. Exemplos futuros ficam fora da janela de 24 horas ou sem `chat_id`, para o worker não disparar mensagens ao subir. O horário próximo do roteiro é criado na hora.
- Operações: salvar paciente e vínculo do Telegram; listar pacientes e profissionais; criar agendamento e devolver a ficha; listar a agenda e o quadro; iniciar e concluir.

## Roteiro de verificação manual

1. Cadastrar ou selecionar um paciente fictício e um profissional.
2. Agendar e emitir a ficha com horário previsto.
3. Conferir agenda, sintomas e estado.
4. Com bot configurado, vincular um chat de demonstração e usar um horário próximo para verificar aviso e lembrete sem esperar dias.
5. Confirmar pelo Telegram e verificar a atualização; usar outro agendamento para demonstrar cancelamento.
6. Iniciar e concluir um atendimento pelo painel.
7. Se a integração externa não estiver disponível, informar que o envio não foi verificado.

## Complementos opcionais

- Posição na fila por profissional.
- Botão para adicionar cinco pacientes e agendamentos fictícios.
- Contadores de pessoas aguardando e atendimentos concluídos.
- Destaque visual temporário na chamada.
- Aviso adicional pelo Telegram ao iniciar o atendimento; os lembretes previstos acima permanecem obrigatórios.
