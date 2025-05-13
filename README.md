# Data.Viz
## 🧠 **Visão Geral do Notebook**

Esse notebook trata de **visualização de dados sobre ataques e vazamentos**, com foco em 2024. Ele carrega um conjunto de dados, faz filtragens e constrói gráficos como:

* Gráfico de linha
* Sunburst
* Grafos

---

### 📦 **1. Importação de bibliotecas**

```python
import pandas as pd
import plotly.express as px
import matplotlib.pyplot as plt
import seaborn as sns
```

**Função:**

* `pandas`: manipulação de dados (tabelas)
* `plotly.express`: gráficos interativos (linha, sunburst)
* `matplotlib.pyplot` & `seaborn`: gráficos estáticos e estilizados

---

### 📂 **2. Leitura do arquivo Excel**

```python
df = pd.read_excel("Data_Breach_Chronology_sample.xlsx")
```

**Função:** Carrega o arquivo Excel com os dados de vazamentos.

---

### 🧹 **3. Tratamento inicial de datas**

```python
df['reported_date'] = pd.to_datetime(df['reported_date'], errors='coerce')
df = df.dropna(subset=['reported_date'])
```

**Função:**

* Converte a coluna `reported_date` em datas reais
* Remove linhas com datas inválidas (para evitar erros)

---

### 📅 **4. Filtra apenas o ano de 2024**

```python
df_2024 = df[df['reported_date'].dt.year == 2024]
```

**Função:** Mantém apenas os vazamentos ocorridos em 2024.

---

### 📊 **5. Gráfico de linha - Organizações únicas por mês**

```python
df_2024['month'] = df_2024['reported_date'].dt.month
monthly_unique_orgs_2024 = df_2024.groupby('month')['org_name'].nunique().reset_index(name='org_unicas')

fig = px.line(
    monthly_unique_orgs_2024,
    x='month',
    y='org_unicas',
    markers=True,
    title='Organizações com Vazamentos por Mês (2024)',
    labels={'month': 'Mês', 'org_unicas': 'Organizações Únicas'}
)
fig.show()
```

**Objetivo:** Mostra quantas **empresas distintas** sofreram vazamento em cada mês de 2024.

* `groupby`: agrupa os dados por mês
* `nunique()`: conta quantas organizações distintas apareceram
* `px.line()`: plota um gráfico de linha com marcadores

---

### 🌞 **6. Gráfico Sunburst**

```python
sunburst_df = df_2024[['org_name', 'breach_type', 'total_affected']].dropna()
sunburst_df['total_affected'] = pd.to_numeric(sunburst_df['total_affected'], errors='coerce').fillna(0)

fig_sunburst = px.sunburst(
    sunburst_df,
    path=['org_name', 'breach_type'],
    values='total_affected',
    title='Distribuição de Vazamentos por Organização e Tipo (2024)'
)
fig_sunburst.show()
```

**Objetivo:** Visualizar **como os dados de vazamento estão distribuídos** hierarquicamente:

* Nível 1: Organização
* Nível 2: Tipo de vazamento
* Valor: Total de pessoas afetadas

---

### 📊 **7. Gráfico de barras - Top organizações afetadas**

```python
top_orgs = df_2024.groupby('org_name')['total_affected'].sum().sort_values(ascending=False).head(10)

plt.figure(figsize=(10, 6))
sns.barplot(x=top_orgs.values, y=top_orgs.index, palette='viridis')
plt.title('Top 10 Organizações por Pessoas Afetadas (2024)')
plt.xlabel('Total de Pessoas Afetadas')
plt.ylabel('Organização')
plt.show()
```

**Objetivo:** Mostrar as 10 organizações que mais afetaram pessoas em 2024.

* `groupby` + `sum`: soma total de afetados por organização
* `sort_values`: ordena do maior para o menor
* `seaborn.barplot`: cria gráfico de barras horizontal

---

## **Resumo Final**

| Etapa                | O que faz                                                      |
| -------------------- | -------------------------------------------------------------- |
| Leitura e tratamento | Converte datas e limpa os dados                                |
| Filtro por 2024      | Analisa somente os vazamentos ocorridos em 2024                |
| Gráfico de linha     | Mostra quantas organizações diferentes foram afetadas por mês  |
| Gráfico sunburst     | Mostra como os vazamentos se distribuem entre empresas e tipos |
| Gráfico de barras    | Lista as empresas com mais pessoas afetadas                    |
