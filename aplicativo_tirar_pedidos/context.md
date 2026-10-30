# Aplicativo de pedidos para lanchonete e petiscaria

## Objetivo

Criar pedidos e acompanhar o preparo no painel da cozinha, seguindo o [contexto geral](../skill/context.md).

## Requisitos obrigatórios

### Tela de pedidos

- Cardápio previamente cadastrado por categoria: bebidas, pratos e sobremesas, com nome e preço.
- Identificador numérico da mesa e número da comanda.
- Seleção de produtos e quantidades.
- Observação escrita do pedido.
- Total calculado automaticamente e conferido pelo backend.
- Envio do pedido e exibição da comanda.

### Painel da cozinha e gestão

- Pedidos por ordem de chegada, em colunas estilo Jira: Recebido, Em preparo e Entregue.
- Botões para avançar o estado, com cores distintas.
- Exibição da mesa, itens, quantidades, observação e total.
- Painel de vendas totais, somando os pedidos entregues; não há processamento de pagamento.
- Atualização automática a cada poucos segundos.
- Produtos com estoque e registro das movimentações de saída.

## Tabelas e relacionamentos preestabelecidos

Todas as tabelas têm `id` como chave primária.

| Tabela | Campos |
| --- | --- |
| categorias | nome único |
| produtos | categoria_id (FK categorias.id), nome, preco decimal, estoque inteiro |
| status | nome único, ordem |
| pedidos | numero_comanda único, mesa inteiro, observacao, status_id (FK status.id), criado_em |
| itens_pedido | pedido_id (FK pedidos.id), produto_id (FK produtos.id), quantidade, preco_unitario decimal |
| movimentacoes | item_pedido_id (FK itens_pedido.id, único), quantidade, tipo fixo SAIDA, criado_em |

Relacionamentos:

- categorias 1:N produtos.
- status 1:N pedidos.
- pedidos 1:N itens_pedido.
- produtos 1:N itens_pedido; pedidos e produtos se relacionam N:N por itens_pedido.
- itens_pedido 1:0..1 movimentacoes; cada item persistido deve ter sua movimentação de saída.
- O produto de uma movimentação é obtido pelo item relacionado, sem duplicar a referência.

Regras:

- Criar os estados Recebido, Em preparo e Entregue na inicialização.
- Registrar o preço unitário no item para preservar o valor quando o preço do produto mudar.
- Calcular o total pela soma de quantidade × preço unitário dos itens, sem coluna de total duplicada.
- Ao criar o pedido, verificar estoque, salvar os itens, baixar o estoque e registrar uma saída por item na mesma transação.
- Rejeitar pedidos sem estoque suficiente. Alterar o estado não deve baixar o estoque novamente.
- Não incluir cancelamentos ou estornos neste escopo.
- Carregar cardápio e estoques fictícios na inicialização.

## Para o planejamento

- Sem login, usuário ou senha. A apresentação abre direto nas duas telas: pedidos e cozinha. A cozinha inclui o quadro e o total de vendas.
- A mesa é informada por quem pede. `numero_comanda` é único, gerado ao salvar e exibido em seguida. A observação é opcional.
- O pedido nasce em Recebido e só avança: Recebido, Em preparo, Entregue. Não há retorno nem cancelamento.
- É obrigatório ao menos um item. O mesmo produto pode aparecer em itens separados; cada item gera uma movimentação.
- Se qualquer item não tiver estoque, a API recusa o pedido inteiro e não grava baixa parcial. Mudar o estado não mexe no estoque.
- O total é a soma de quantidade × `preco_unitario`. As vendas somam os pedidos Entregues. Não há coluna nem tabela de total.
- Produto com estoque zero aparece indisponível. Preços com duas casas decimais.
- A cozinha consulta a API a cada 5 segundos. A tela de pedidos consulta ao carregar o cardápio e ao enviar.
- Na inicialização: as três categorias e poucos produtos com preço e estoque, o suficiente para o roteiro. Não criar pedidos de exemplo.
- Operações: listar cardápio; criar pedido com itens, baixa e movimentações; listar pedidos do quadro; avançar estado; obter o total de vendas.

## Roteiro de verificação manual

1. Criar um pedido para a mesa 3 com dois produtos e uma observação.
2. Conferir o total e a comanda.
3. Confirmar a baixa de estoque e as movimentações.
4. Localizar o pedido na cozinha e avançar para Em preparo e Entregue.
5. Conferir o resumo de vendas.

## Complementos opcionais

- Botão para preencher um pedido de exemplo.
- Filtro por mesa.
- Tempo decorrido desde a criação.
- Destaque temporário quando o estado muda.
