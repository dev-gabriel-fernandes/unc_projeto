# gestor de filas integrado ao telegram



- gestão de filas de atendimento médico, onde o paciente retira uma ficha que gera um ticket, com horário estimado do atendimento, e envia uma mensagem automática ao paciente pelo telegram, 30 minutos antes do atendimento.

- funções da mensagem:
    - avisar o usuário um dia antes, e pode receber resposta de confirmação. Se sim, nada acontece, se não, cancela o atendimento.
    - 30 minutos antes, enviar um lembrete.

- tela do atendente
    - código do paciente (cpf)
    - agendamentos (calendário por hora)
    - quem vai atender (dropdown)
    - sintomas descritivo (pressão, oxigenação, etc)
    - telefone
    - situação dos alertas (se o paciente cancelou, confirmou, já recebeu aviso de lembrete)
        - estilo Jira, com alterações de cor, conforme o status

- tabelas
    - agendamentos
        - recebeu lembrete?
        - recebeu aviso?
    - pacientes