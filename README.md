📊 Marketing Performance Dashboard | Dashboard de Rendimiento de Marketing

Power BI · Python · DAX

Dashboard overview

⚠️ Data disclaimer / Aviso sobre los datos: 

The dataset is synthetic (generated with Python, seed 42) and mimics the structure of marketing reporting platforms. Findings describe the simulated scenario, not real market behavior.
El dataset es sintético (generado con Python, seed 42) e imita la estructura de plataformas de reporting de marketing. Los hallazgos describen el escenario simulado, no el comportamiento real del mercado.

1. Context

Marketing teams that run several paid and organic channels at once often work with scattered reports, which makes it hard to see where the budget is generating returns and where it is being wasted.

This project simulates that situation. I generated a dataset of 10,000 records (2024) covering 4 channels (Programmatic, Paid Search, Paid Social, Organic), 6 sources (StackAdapt, Amazon Ad Server, Google, Bing, Facebook, LinkedIn Ads) and 10 campaigns, with metrics such as impressions, clicks, spend, conversions and revenue. Each channel was given its own range for CTR, CPC and conversion rate.

2. Objective

Build an interactive dashboard that gives a single view of cross-channel performance and answers:

How much are we spending, and what do we get back (ROAS, CPA)?
Which channels and sources are efficient, and which are not?
How do impressions, CTR and spend evolve month over month?
Where could budget be reallocated?

3. Analysis

Data generation (Python)

Created the dataset with pandas and numpy in improvado_data.ipynb, with fixed seed 42 so anyone can reproduce the same data.
Computed per-row ctr_percent, cpc, spend, conversions, cpa, revenue and roas.

Data modeling (Power BI)

Loaded marketing_data.csv and built a Calendar dimension table related to the date column.
Created calculated columns for period grouping (Año_Mes, Month-Year, Trimestre).
Built DAX measures for the main KPIs: CTR, CPM, Cost per Click, Conversion Rate, Total Spend, Total Revenue, ROAS, CPA, Spend/Revenue Share and month-over-month variations (impressions, CTR, spend).
Ratios are calculated with DIVIDE(SUM(...), SUM(...)) instead of averaging row-level ratios, which would give incorrect results.

```DAX
Total Revenue = SUM(marketing_data[revenue])

ROAS = DIVIDE([Total Revenue], [Total_Spend])

CPA = DIVIDE([Total_Spend], SUM(marketing_data[conversions]))

Spend Share % =
DIVIDE(
    [Total_Spend],
    CALCULATE([Total_Spend], ALLSELECTED(marketing_data[channel]))
)

Net Result = [Total Revenue] - [Total_Spend]
```

Dashboard design

KPI cards: Spend, Revenue, ROAS, CPA, Impressions, CTR, CPM, CPC, Conversions, Conversion Rate.
Monthly trend charts and matrices by channel, source and campaign with month-over-month variation.
Slicers for source, channel, campaign, month and quarter.

Channel performance

4. Findings

| Channel | Share of Spend | Share of Revenue | ROAS | CPA |
|----------|----------:|----------:|----------:|----------:|
| Paid Search | 32.5% | 65.5% | 2.92 | $30.87 |
| Programmatic | 10.4% | 7.4% | 1.03 | $87.14 |
| Paid Social | 57.1% | 27.1% | 0.69 | $130.94 |
| Organic | 0.0% | 0.0% | N/A | N/A |

Spend share vs revenue share

Budget imbalance: Paid Social takes 57% of the spend but generates only 27% of revenue. Paid Search generates 65% of revenue with 33% of the spend.
Profitability by channel: Paid Search returns $2.92 per dollar invested, Programmatic barely breaks even (1.03) and Paid Social loses $0.31 per dollar (0.69).
Net result: $40.4M invested and $58.5M in revenue (overall ROAS 1.45, net +$18.2M). Paid Search contributes +$25.2M, while Paid Social subtracts –$7.2M.
Cost drivers in Paid Social: CPC of $2.60 (vs $0.92 in Paid Search) and conversion rate of 1.99% (vs 2.99%) result in a CPA more than 4 times higher.
Sources behave alike within a channel: Facebook and LinkedIn Ads both show ROAS 0.69; Google and Bing show 2.91 and 2.93. Budget decisions make more sense at channel level than at platform level.
Organic is not measurable here: it represents 25.5% of impressions but records no clicks or conversions, which also lowers the overall CTR (1.85% vs 2.48% for paid channels only).
Reallocation scenario (hypothesis): moving 25% of Paid Social spend (~$5.8M) to Paid Search would raise overall ROAS from 1.45 to ~1.77, roughly +$12.9M in revenue.

Source breakdown

5. Conclusions
   
The dashboard makes the budget imbalance visible in seconds and ties spend to return by channel, replacing manual cross-channel reports.
Recommendation for this scenario: review the Paid Social allocation and test shifting part of it to Paid Search.
Limitations:
The data is synthetic, and channel differences come from parameters I defined. The value of the project is the analytical process and the dashboard, not the market findings.
Differences by campaign or by month are random noise and are not treated as insights.
The reallocation scenario assumes constant ROAS. In reality, returns diminish as spend scales, so it should be validated with a controlled test.
Next steps: repeat the analysis with real campaign data, integrate web analytics to measure Organic, and add attribution and budget-pacing views.


1. Contexto

Los equipos de marketing que operan varios canales pagados y orgánicos a la vez suelen trabajar con reportes dispersos, lo que dificulta ver dónde el presupuesto genera retorno y dónde se está desperdiciando.

Este proyecto simula esa situación. Generé un dataset de 10,000 registros (2024) con 4 canales (Programmatic, Paid Search, Paid Social, Organic), 6 fuentes (StackAdapt, Amazon Ad Server, Google, Bing, Facebook, LinkedIn Ads) y 10 campañas, con métricas como impresiones, clics, gasto, conversiones e ingresos. A cada canal le asigné rangos propios de CTR, CPC y tasa de conversión.

2. Objetivo

Construir un dashboard interactivo que ofrezca una vista única del desempeño entre canales y responda:

¿Cuánto gastamos y qué retorno obtenemos (ROAS, CPA)?
¿Qué canales y fuentes son eficientes y cuáles no?
¿Cómo evolucionan las impresiones, el CTR y el gasto mes a mes?
¿Dónde se podría reasignar presupuesto?

3. Análisis

Generación de datos (Python)

Creé el dataset con pandas y numpy en improvado_data.ipynb, con seed 42 fija para que cualquiera pueda reproducir exactamente los mismos datos.
Calculé por fila ctr_percent, cpc, spend, conversions, cpa, revenue y roas.

Modelado de datos (Power BI)

Cargué marketing_data.csv y construí una tabla de dimensión Calendar relacionada con la columna de fecha.
Creé columnas calculadas para agrupar por periodo (Año_Mes, Month-Year, Trimestre).
Desarrollé medidas DAX para los KPIs principales: CTR, CPM, Costo por Clic, Tasa de Conversión, Gasto Total, Ingresos Totales, ROAS, CPA, participación del gasto/ingresos y variaciones mes contra mes (impresiones, CTR, gasto).
Las razones se calculan con DIVIDE(SUM(...), SUM(...)) y no promediando razones por fila, lo cual daría resultados incorrectos.

``` DAX
Total Revenue = SUM(marketing_data[revenue])

ROAS = DIVIDE([Total Revenue], [Total_Spend])

CPA = DIVIDE([Total_Spend], SUM(marketing_data[conversions]))

Spend Share % =
DIVIDE(
    [Total_Spend],
    CALCULATE([Total_Spend], ALLSELECTED(marketing_data[channel]))
)

Net Result = [Total Revenue] - [Total_Spend]
```
Diseño del dashboard

Tarjetas de KPI: Gasto, Ingresos, ROAS, CPA, Impresiones, CTR, CPM, CPC, Conversiones y Tasa de Conversión.
Gráficas de tendencia mensual y matrices por canal, fuente y campaña con variación mes contra mes.
Segmentadores por fuente, canal, campaña, mes y trimestre.

Desempeño por canal

4. Hallazgos

| Canal        | % del gasto | % de los ingresos | ROAS | CPA     |
|-------------|------------|-------------------|------|---------|
| Paid Search | 32.5%      | 65.5%             | 2.92 | $30.87  |
| Programmatic| 10.4%      | 7.4%              | 1.03 | $87.14  |
| Paid Social | 57.1%      | 27.1%             | 0.69 | $130.94 |
| Organic     | 0%         | 0%                | N/A  | N/A     |

Participación del gasto vs ingresos

Desbalance de presupuesto: Paid Social concentra el 57% del gasto pero solo genera el 27% de los ingresos. Paid Search genera el 65% de los ingresos con el 33% del gasto.
Rentabilidad por canal: Paid Search devuelve $2.92 por dólar invertido, Programmatic apenas cubre su costo (1.03) y Paid Social pierde $0.31 por cada dólar (0.69).
Resultado neto: $40.4M invertidos y $58.5M en ingresos (ROAS global 1.45, neto de +$18.2M). Paid Search aporta +$25.2M, mientras que Paid Social resta –$7.2M.
Causas del problema en Paid Social: un CPC de $2.60 (frente a $0.92 en Paid Search) y una tasa de conversión de 1.99% (frente a 2.99%) generan un CPA más de 4 veces mayor.
Las fuentes se comportan igual dentro de cada canal: Facebook y LinkedIn Ads tienen ROAS 0.69; Google y Bing tienen 2.91 y 2.93. Tiene más sentido decidir el presupuesto por canal que por plataforma.
Organic no es medible con estos datos: representa el 25.5% de las impresiones pero no registra clics ni conversiones, lo que además reduce el CTR global (1.85% frente a 2.48% solo en canales pagados).
Escenario de reasignación (hipótesis): mover el 25% del gasto de Paid Social (~$5.8M) a Paid Search elevaría el ROAS global de 1.45 a ~1.77, unos +$12.9M en ingresos.

Desglose por fuente

5. Conclusiones
   
El dashboard hace visible el desbalance de presupuesto en segundos y conecta gasto con retorno por canal, reemplazando reportes manuales entre canales.
Recomendación para este escenario: revisar la asignación de Paid Social y probar mover parte de ella a Paid Search.
Limitaciones:
Los datos son sintéticos y las diferencias entre canales provienen de parámetros que yo definí. El valor del proyecto está en el proceso analítico y el dashboard, no en los hallazgos de mercado.
Las diferencias por campaña o por mes son ruido aleatorio y no se tratan como insights.
El escenario de reasignación supone un ROAS constante. En la realidad el retorno disminuye al escalar el gasto, por lo que debe validarse con una prueba controlada.
Próximos pasos: repetir el análisis con datos reales de campañas, integrar analítica web para medir Organic y agregar vistas de atribución y de ritmo de gasto.

```
📁 Repository structure | Estructura del repositorio
├── README.md
├── improvado_data.ipynb        # Data generation / Generación de datos
├── marketing_data.csv          # Synthetic dataset / Dataset sintético
├── dashboard.pbix              # Power BI file / Archivo de Power BI
└── img/
    ├── 01_dashboard_overview.png
    ├── 02_channel_performance.png
    ├── 03_spend_vs_revenue_share.png
    └── 04_source_breakdown.png
```

🛠️ Tools | Herramientas

Python (pandas, numpy) · Jupyter Notebook · Power BI · DAX · Data modeling / Modelado de datos

👤 Author | Autor

Cristian Avila · [LinkedIn](https://www.linkedin.com/in/cristian-av-sa-data-analyst2023/)
