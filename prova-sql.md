# Devolutiva: Banco de Dados

#### Lista de funcionários ordenando pelo salário decrescente.
SELECT id_vendedor, nome, cargo, salario
FROM VENDEDORES
ORDER BY salario DESC;

#### Lista de pedidos de vendas ordenado por data de emissão.
SELECT id_pedido, id_empresa, id_cliente, valor_total, data_emissao, situacao
FROM PEDIDO
ORDER BY data_emissao;

#### Valor de faturamento por cliente.
SELECT id_cliente, razao_social AS cliente, SUM(valor_total) AS faturamento
FROM PEDIDO
WHERE situacao = 'Concluído'
GROUP BY id_cliente, razao_social;

##### Valor de faturamento por empresa.
SELECT id_empresa, SUM(valor_total) AS faturamento
FROM PEDIDO
WHERE situacao = 'Concluído'
GROUP BY id_empresa;

##### Valor de faturamento por vendedor.
SELECT id_vendedor, nome AS vendedor, SUM(valor_total) AS faturamento
FROM PEDIDO P
INNER JOIN VENDEDORES V ON P.id_vendedor = V.id_vendedor
WHERE P.situacao = 'Concluído'
GROUP BY id_vendedor, nome;



#####Exercício de Junção:
WITH UltimoPedido AS (
    SELECT id_cliente, MAX(data_emissao) AS ultima_data_emissao
    FROM PEDIDO
    WHERE situacao = 'Concluído'
    GROUP BY id_cliente
)
SELECT IP.id_produto,
       PR.descricao,
       P.id_cliente,
       C.razao_social AS razao_social_cliente,
       P.id_empresa,
       E.razao_social AS razao_social_empresa,
       P.id_vendedor,
       V.nome AS nome_vendedor,
       CP.preco_minimo,
       CP.preco_maximo,
       CASE
           WHEN CP.preco_minimo <= IP.preco_praticado AND IP.preco_praticado <= CP.preco_maximo THEN IP.preco_praticado
           ELSE CP.preco_minimo
       END AS preco_base
FROM ITENS_PEDIDO IP
JOIN PEDIDO P ON IP.id_pedido = P.id_pedido
JOIN UltimoPedido UP ON P.id_cliente = UP.id_cliente AND P.data_emissao = UP.ultima_data_emissao
JOIN CLIENTES C ON P.id_cliente = C.id_cliente
JOIN EMPRESA E ON P.id_empresa = E.id_empresa
JOIN VENDEDORES V ON P.id_vendedor = V.id_vendedor
JOIN PRODUTOS PR ON IP.id_produto = PR.id_produto
JOIN CONFIG_PRECO_PRODUTO CP ON IP.id_produto = CP.id_produto AND P.id_vendedor = CP.id_vendedor;
