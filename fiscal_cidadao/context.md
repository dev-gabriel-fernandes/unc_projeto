# Fiscal Cidadão: registro e acompanhamento de ocorrências

## Objetivo

Registrar um problema da cidade, receber um protocolo e acompanhar a resposta da gestão, seguindo o [contexto geral](../skill/context.md).

## Requisitos obrigatórios

### Tela do cidadão

- Formulário com e-mail e/ou telefone, categoria e descrição.
- Categorias em dropdown: iluminação, lixo e buracos.
- Adicionar imagens.
- Coletar localização pelo navegador, com latitude e longitude. Permitir informar endereço por texto se a localização estiver indisponível ou for negada.
- Gerar protocolo único.
- Consultar pelo protocolo os dados, as imagens, o estado e as respostas.

### Painel de gestão

- Ocorrências em colunas estilo Jira: Aberto, Em análise e Resolvido.
- Contadores por estado.
- Filtros por categoria e estado.
- Visualizar detalhes e alterar o estado.
- Enviar respostas e anexar fotos a elas.
- Exibir as respostas e suas fotos na consulta do cidadão.

## Tabelas e relacionamentos preestabelecidos

Todas as tabelas têm `id` como chave primária.

| Tabela | Campos |
| --- | --- |
| categorias | nome único |
| status | nome único, ordem |
| ocorrencias | protocolo único, email opcional, telefone opcional, categoria_id (FK categorias.id), status_id (FK status.id), descricao, endereco opcional, latitude opcional, longitude opcional, criado_em |
| respostas | ocorrencia_id (FK ocorrencias.id), texto, criado_em |
| imagens_ocorrencia | ocorrencia_id (FK ocorrencias.id), caminho_arquivo, criado_em |
| imagens_resposta | resposta_id (FK respostas.id), caminho_arquivo, criado_em |

Relacionamentos:

- categorias 1:N ocorrencias.
- status 1:N ocorrencias.
- ocorrencias 1:N respostas.
- ocorrencias 1:N imagens_ocorrencia.
- respostas 1:N imagens_resposta.

Regras:

- Exigir ao menos um contato: e-mail ou telefone.
- Exigir coordenadas válidas ou endereço textual; latitude e longitude devem ser preenchidas em conjunto.
- Salvar os arquivos em volume Docker e apenas seus caminhos no banco.
- Usar tabelas separadas de imagens para manter explícito se a foto pertence ao relato ou à resposta.
- Criar categorias, estados e ocorrências fictícias na inicialização.
- Contatos são para registro; não implementar envio de notificações.

## Para o planejamento

- Sem login, usuário ou senha. A apresentação abre direto nas duas telas: cidadão (registrar e consultar) e gestão (quadro, filtros, detalhe, resposta e mudança de estado).
- A ocorrência nasce em Aberto. O painel pode movê-la entre Aberto, Em análise e Resolvido. A consulta do cidadão não altera dados.
- O protocolo é curto, legível e único, gerado ao salvar e usado na consulta exata.
- Imagens do relato e da resposta são opcionais. Aceitar JPEG, PNG e WebP. A API grava o arquivo no volume, guarda só o caminho e devolve uma URL para a interface. O roteiro envia uma foto; o carregamento inicial não precisa de imagens.
- Uma resposta tem texto e fotos opcionais, em qualquer estado.
- Operações: listar categorias; criar ocorrência; consultar por protocolo; listar ocorrências com filtros; alterar estado; criar resposta; obter imagem.
- Na inicialização: as três categorias, os três estados nesta ordem e ocorrências fictícias cobrindo os três estados, pelo menos uma com resposta.

## Roteiro de verificação manual

1. Registrar um poste apagado com contato, localização e foto.
2. Copiar o protocolo e consultar a ocorrência.
3. Localizar a ocorrência no painel usando os filtros.
4. Alterar para Em análise, registrar uma resposta com foto e concluir como Resolvido.
5. Consultar novamente e conferir estado, resposta e anexos.
6. Conferir os contadores.

## Complementos opcionais

- Histórico das mudanças de estado, em tabela historico_status: id (PK), ocorrencia_id (FK ocorrencias.id), status_id (FK status.id), criado_em; relação ocorrencias 1:N historico_status e status 1:N historico_status.
- Visualização em mapa; não é necessária para coletar a localização.
