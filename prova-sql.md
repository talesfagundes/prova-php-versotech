# Devolutiva: Banco de Dados

#### Lista de funcionários ordenando pelo salário decrescente.
SELECT id_vendedor, nome, cargo, salario <br>
FROM VENDEDORES <br>
ORDER BY salario DESC; <br>

#### Lista de pedidos de vendas ordenado por data de emissão.
SELECT id_pedido, id_empresa, id_cliente, valor_total, data_emissao, situacao <br>
FROM PEDIDO <br>
ORDER BY data_emissao; <br>

#### Valor de faturamento por cliente.
SELECT id_cliente, razao_social AS cliente, SUM(valor_total) AS faturamento <br>
FROM PEDIDO <br>
WHERE situacao = 'Concluído' <br>
GROUP BY id_cliente, razao_social; <br>

##### Valor de faturamento por empresa.
SELECT id_empresa, SUM(valor_total) AS faturamento <br>
FROM PEDIDO <br>
WHERE situacao = 'Concluído' <br>
GROUP BY id_empresa; <br>

##### Valor de faturamento por vendedor.
SELECT id_vendedor, nome AS vendedor, SUM(valor_total) AS faturamento <br>
FROM PEDIDO P <br>
INNER JOIN VENDEDORES V ON P.id_vendedor = V.id_vendedor <br>
WHERE P.situacao = 'Concluído' <br>
GROUP BY id_vendedor, nome; <br>



#####Exercício de Junção:
WITH UltimoPedido AS ( <br>
    SELECT id_cliente, MAX(data_emissao) AS ultima_data_emissao <br>
    FROM PEDIDO <br>
    WHERE situacao = 'Concluído' <br>
    GROUP BY id_cliente <br>
)
SELECT IP.id_produto, <br>
       PR.descricao, <br>
       P.id_cliente, <br>
       C.razao_social AS razao_social_cliente, <br>
       P.id_empresa, <br>
       E.razao_social AS razao_social_empresa, <br>
       P.id_vendedor, <br>
       V.nome AS nome_vendedor, <br>
       CP.preco_minimo, <br>
       CP.preco_maximo, <br>
       CASE
           WHEN CP.preco_minimo <= IP.preco_praticado AND IP.preco_praticado <= CP.preco_maximo THEN IP.preco_praticado <br>
           ELSE CP.preco_minimo <br>
       END AS preco_base <br>
FROM ITENS_PEDIDO IP <br>
JOIN PEDIDO P ON IP.id_pedido = P.id_pedido <br>
JOIN UltimoPedido UP ON P.id_cliente = UP.id_cliente AND P.data_emissao = UP.ultima_data_emissao <br>
JOIN CLIENTES C ON P.id_cliente = C.id_cliente <br>
JOIN EMPRESA E ON P.id_empresa = E.id_empresa <br>
JOIN VENDEDORES V ON P.id_vendedor = V.id_vendedor <br>
JOIN PRODUTOS PR ON IP.id_produto = PR.id_produto <br>
JOIN CONFIG_PRECO_PRODUTO CP ON IP.id_produto = CP.id_produto AND P.id_vendedor = CP.id_vendedor; <br>
