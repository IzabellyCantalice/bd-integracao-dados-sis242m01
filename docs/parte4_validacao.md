# Parte 4: Roteiro de validação com o cliente (fevereiro/2025)

## 1. Pontos a validar
- **Volume e faturamento de fevereiro/2025:** Conferir com o fechamento financeiro do cliente o total de vendas realizadas, quantidade total de itens vendidos e valor bruto faturado em fevereiro de 2025.
- **Integridade cadastral de produtos e fornecedores:** Validar itens vendidos ou pedidos que não constam no cadastro da filial (`produtos_filial`) e produtos vinculados a códigos de fornecedores ausentes na tabela `fornecedor`.
- **Conciliação entre compras e entradas de NFe:** Revisar notas fiscais registradas em `entradas_mercadoria` sem vínculo válido de `ordem_compra` em `pedido_compra`, bem como pedidos com saldo pendente de entrega.

## 2. Técnicas para garantir exatidão e precisão
- **Batimento de totais (Reconciliação):** Comparação direta entre contagens (`COUNT`) e somatórios (`SUM`) da planilha original `base_teste.xlsx` e os registros carregados no PostgreSQL.
- **Auditoria de integridade referencial:** Uso de consultas com `LEFT JOIN ... WHERE chave IS NULL` para identificar registros órfãos entre vendas, produtos, pedidos, entradas e fornecedores.
- **Validação de domínio e consistência temporal:** Verificação de quantidades ou preços zerados/negativos e datas invertidas (`data_entrega < data_pedido`).

## 3. Consultas preparadas para a reunião
- **Consulta 1 (`sql/04_validacao.sql`):** Lista vendas cujo `produto_id` não possui cadastro na respectiva filial em `produtos_filial`.
- **Consulta 2 (`sql/04_validacao.sql`):** Identifica produtos em `produtos_filial` cujo `idfornecedor` não está cadastrado em `fornecedor`.
- **Consulta 3 (`sql/04_validacao.sql`):** Mostra notas fiscais em `entradas_mercadoria` cuja `ordem_compra` não existe em `pedido_compra`.
- **Consulta 4 (`sql/04_validacao.sql`):** Verifica se existem vendas com quantidade ou valor unitário menor ou igual a zero.
- **Consulta 5 (`sql/04_validacao.sql`):** Aponta pedidos de compra com `data_entrega` anterior à `data_pedido`.
- **Consulta 6 (`sql/04_validacao.sql`):** Exibe o consolidado de vendas e faturamento de fevereiro de 2025 para aprovação do cliente.

## Erros encontrados nos dados

| Tabela | Problema | Quantidade | Consulta que mostra |
| :--- | :--- | :--- | :--- |
| venda | Vendas de produtos sem cadastro correspondente em `produtos_filial` | Ver retorno da Consulta 1 no DBeaver | Consulta 1 (`sql/04_validacao.sql`) |
| produtos_filial | Produtos vinculados a `idfornecedor` inexistente em `fornecedor` | Ver retorno da Consulta 2 no DBeaver | Consulta 2 (`sql/04_validacao.sql`) |
| entradas_mercadoria | Entradas de mercadoria sem `ordem_compra` correspondente em `pedido_compra` | Ver retorno da Consulta 3 no DBeaver | Consulta 3 (`sql/04_validacao.sql`) |
| venda | Registros com quantidade ou valor unitário zerado/negativo | Ver retorno da Consulta 4 no DBeaver | Consulta 4 (`sql/04_validacao.sql`) |
| pedido_compra | Pedidos com `data_entrega` anterior à `data_pedido` | Ver retorno da Consulta 5 no DBeaver | Consulta 5 (`sql/04_validacao.sql`) |