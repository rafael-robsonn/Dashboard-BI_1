# Guia: dashboard interativo no Power BI (TechVarejo)

O Power BI Desktop não exporta `.pbix` fora do aplicativo, então o caminho é importar o CSV e montar os visuais. Com este guia, leva uns 30–40 minutos.

## 1. Importar os dados

1. Power BI Desktop → **Obter dados → Texto/CSV** → selecione `vendas_techvarejo.csv`.
2. Em **Transformar dados** (Power Query), confira os tipos:
   - `data_pedido` → Data
   - `quantidade`, `tempo_entrega_dias` → Número inteiro
   - `preco_unitario`, `desconto_pct`, `receita_liquida`, `custo_total`, `avaliacao_cliente` → Número decimal
   - Locale: se o Power BI estiver em pt-BR, importe as colunas decimais com **Usando localidade → Inglês (EUA)**, porque o CSV usa ponto como separador decimal.
3. **Fechar e Aplicar**.

## 2. Tabela calendário (para o eixo temporal ficar correto)

Modelagem → Nova tabela:

```dax
Calendario = CALENDAR(MIN(vendas_techvarejo[data_pedido]), MAX(vendas_techvarejo[data_pedido]))
```

Crie a coluna `AnoMes = FORMAT(Calendario[Date], "YYYY-MM")` e relacione `Calendario[Date]` 1:* com `vendas_techvarejo[data_pedido]`.

## 3. Medidas DAX

```dax
Receita = SUM(vendas_techvarejo[receita_liquida])
Custo = SUM(vendas_techvarejo[custo_total])
Pedidos = COUNTROWS(vendas_techvarejo)
Ticket Medio = DIVIDE([Receita], [Pedidos])
Margem % = DIVIDE([Receita] - [Custo], [Receita])
Avaliacao Media = AVERAGE(vendas_techvarejo[avaliacao_cliente])
Entrega Media = AVERAGE(vendas_techvarejo[tempo_entrega_dias])
```

Formate `Margem %` como percentual e `Receita`/`Ticket Medio` como moeda R$.

## 4. Visuais (mesma composição do dashboard HTML)

| Visual no Power BI | Campos | Equivale a |
|---|---|---|
| 6 **Cartões** | Receita, Pedidos, Ticket Medio, Margem %, Avaliacao Media, Entrega Media | faixa de KPIs |
| **Gráfico de colunas e linhas** | Eixo: `Calendario[AnoMes]` · Colunas: Receita · Linha: Pedidos | evolução mensal |
| **Barras horizontais** | Eixo: `categoria_produto` · Valor: Receita · Dica: Margem % | receita por categoria |
| **Rosca** | Legenda: `canal_venda` · Valor: Receita | mix de canais |
| **Mapa coroplético** (ou Shape Map com o TopoJSON de UFs do Brasil) | Local: `uf` · Saturação: Receita | receita por estado |
| **Dispersão** | X: média de `desconto_pct` · Y: Margem % · Tamanho: Receita · Legenda: `categoria_produto` · Detalhe: `id_pedido` | desconto × margem |
| **Colunas** | Eixo: `forma_pagamento` · Valor: Receita | formas de pagamento |
| **Radar** (visual "Radar Chart" da AppSource) | Categoria: métrica normalizada · use o CSV `vendas_techvarejo_normalizado.csv` ou crie medidas normalizadas por região | perfil das regiões |

Para o mapa por UF funcionar bem: selecione a coluna `uf` → guia Ferramentas de Coluna → **Categoria de Dados = Estado ou Província**, e o campo país fixo "Brazil" ajuda a desambiguar.

## 5. Interatividade

- **Segmentações de dados (slicers)**: adicione para `segmento_cliente`, `regiao` e `AnoMes` (estilo dropdown ou botões).
- A filtragem cruzada entre visuais já vem por padrão: clicar numa barra de categoria filtra o resto da página (mesmo comportamento implementado no dashboard HTML).
- Formato → **Editar interações** para escolher entre realçar ou filtrar.
- Extra: **Dica de ferramenta de página** com detalhe do pedido, e **Drill through** de categoria → página de detalhe.

## 6. Normalização no Power BI (item 1.2 do enunciado)

Se preferir normalizar dentro do modelo em vez de usar o CSV já normalizado:

```dax
Receita Norm =
VAR mn = MINX(ALL(vendas_techvarejo), vendas_techvarejo[receita_liquida])
VAR mx = MAXX(ALL(vendas_techvarejo), vendas_techvarejo[receita_liquida])
RETURN DIVIDE(SUM(vendas_techvarejo[receita_liquida]) - mn, mx - mn)
```

Repita o padrão para as demais numéricas que entrarem no radar.
