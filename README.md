# TechVarejo · Dashboard Comercial

Dashboard interativo de vendas para um varejo fictício de eletrônicos, construído a partir de dados sintéticos como atividade de visualização de dados (CEUB, Ciência de Dados e Machine Learning).

**[▶ Abrir o dashboard](./Dashboard-BI_1_techvarejo.html)** — baixe o arquivo e abra no navegador, ou ative o GitHub Pages deste repositório (veja abaixo).

![Preview do dashboard](./Dashboard-BI_1_techvarejo.png)

## Conteúdo do repositório

| Arquivo | Descrição |
|---|---|
| `dashboard_techvarejo.html` | Dashboard interativo (HTML + Chart.js, sem dependências externas). Filtro cruzado: clique em barras, fatias ou estados do mapa para filtrar todos os visuais. |
| `dashboard_techvarejo.png` | Captura estática do dashboard em alta resolução, sem filtros aplicados. |
| `vendas_techvarejo.csv` | Base de dados sintética — 100 pedidos × 15 colunas (8 categóricas, 7 numéricas). |
| `vendas_techvarejo_normalizado.csv` | Mesma base com as colunas numéricas normalizadas (min-max, escala 0–1). |
| `guia_power_bi.md` | Passo a passo para reproduzir o mesmo painel no Power BI Desktop (medidas DAX, tabela calendário, mapeamento de visuais). |

## Sobre os dados

Base sintética gerada com seed fixa (42) para reprodutibilidade, simulando pedidos de um varejo de eletrônicos no Brasil entre setembro/2025 e agosto/2026.

**Colunas categóricas:** `id_pedido`, `data_pedido`, `uf`, `regiao`, `canal_venda`, `categoria_produto`, `forma_pagamento`, `segmento_cliente`
**Colunas numéricas:** `quantidade`, `preco_unitario`, `desconto_pct`, `receita_liquida`, `custo_total`, `tempo_entrega_dias`, `avaliacao_cliente`

Regras de negócio embutidas na geração (não é ruído aleatório uniforme):
- Sazonalidade com pico em novembro/dezembro (Black Friday e Natal)
- Concentração de receita no Sudeste
- Margem estruturalmente diferente por categoria (periféricos > áudio > monitores > smart home > notebooks > smartphones)
- Prazo de entrega maior no Norte e Nordeste, com avaliação do cliente correlacionada negativamente ao prazo

## Dashboard — composição

| Painel | Visual | Por quê |
|---|---|---|
| Visão geral | 6 cartões de KPI | Leitura executiva imediata (receita, pedidos, ticket, margem, avaliação, entrega) |
| Evolução temporal | Combinado barras + linha | Receita (barra) e volume de pedidos (linha, eixo secundário) — escalas incompatíveis, eixo duplo necessário |
| Geografia | Cartograma de azulejos por UF | Todos os estados com peso visual igual, sem a distorção de área que um mapa geográfico real introduziria |
| Geografia | Radar de regiões | Comparação de 5 regiões em 5 métricas normalizadas simultaneamente |
| Mix comercial | Barras horizontais (categoria) | Ranking de magnitude entre classes nominais |
| Mix comercial | Rosca (canal) | Composição parte-do-todo, 4 categorias |
| Mix comercial | Barras verticais (pagamento) | Comparação simples entre 4 itens |
| Rentabilidade | Dispersão de bolhas | Relação contínua entre desconto e margem por pedido; tamanho = receita, cor = categoria |

## Como usar

**Local:** clone o repositório e abra `dashboard_techvarejo.html` direto no navegador — não precisa de servidor, build ou instalação.

```bash
git clone <url-do-repo>
cd <repo>
open dashboard_techvarejo.html   # macOS
# ou apenas dê duplo clique no arquivo
```

**GitHub Pages:** em Settings → Pages, selecione a branch `main` e a pasta raiz. O dashboard fica acessível publicamente em `https://<usuario>.github.io/<repo>/dashboard_techvarejo.html`.

**Power BI:** siga o `guia_power_bi.md`, importando `vendas_techvarejo.csv`.

## Stack

HTML + CSS + JavaScript puro, [Chart.js](https://www.chartjs.org/) para os gráficos. Sem framework, sem bundler, sem dependência de rede em tempo de execução.

## Licença dos dados

Dados 100% sintéticos, gerados para fins acadêmicos. Nenhuma informação real de clientes, pedidos ou empresas.
