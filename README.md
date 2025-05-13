### **1. Carregamento e pré-processamento dos dados**

```python
df = pd.read_excel("Data_Breach_Chronology_sample.xlsx")
df['reported_date'] = pd.to_datetime(df['reported_date'], errors='coerce')
df_2024 = df[df['reported_date'].dt.year == 2024]
df_2024 = df_2024.dropna(subset=['org_name', 'breach_type'])
```

**Função:**

* Lê um arquivo `.xlsx` contendo registros de vazamentos.
* Converte a coluna `reported_date` para datetime.
* Filtra os dados para considerar apenas incidentes reportados em 2024.
* Remove registros que não informam organização ou tipo de vazamento.

---

### **2. Visualização em Grafo**

```python
G = nx.Graph()
for _, row in df_2024.iterrows():
    org = row['org_name']
    breach = row['breach_type']
    G.add_node(org, type='organization')
    G.add_node(breach, type='breach')
    G.add_edge(org, breach)
```

**Função:**

* Cria um grafo não direcionado (usando `networkx`) ligando:

  * organizações ↔ tipos de vazamento.
* Cada organização e tipo de vazamento é um nó.
* Um aresta é criada entre a organização e o tipo de vazamento correspondente.

```python
nx.draw_networkx_nodes(...) 
nx.draw_networkx_edges(...)
```

**Resultado:**
Um grafo com dois tipos de nós:

* **Azul claro**: organizações
* **Coral claro**: tipos de vazamento
  Objetivo: visualizar como diferentes organizações estão ligadas a diferentes tipos de brechas.

---

### **3. Gráfico Sunburst**

```python
sunburst_df = df_2024[['org_name', 'breach_type', 'total_affected']].dropna()
sunburst_df['total_affected'] = pd.to_numeric(sunburst_df['total_affected'], errors='coerce').fillna(0)
fig_sunburst = px.sunburst(...)
fig_sunburst.show()
```

**Função:**

* Visualiza a hierarquia:

  * Organização → Tipo de vazamento
* Tamanho dos setores é proporcional ao número de pessoas afetadas (`total_affected`).
* Usa `plotly.express.sunburst` para uma visualização interativa.

---

### **4. Evolução temporal dos incidentes**

```python
df['reported_date'] = pd.to_datetime(df['reported_date'], errors='coerce')
df = df.dropna(subset=['reported_date'])
df['year'] = df['reported_date'].dt.year
df['month'] = df['reported_date'].dt.month
monthly_by_year = df.groupby(['year', 'month']).size().reset_index(name='num_vazamentos')
```

**Função:**

* Reprocessa a coluna de datas.
* Cria colunas `year` e `month`.
* Agrupa por mês e ano para contar o número de vazamentos por período.

```python
fig = px.line(...)
fig.show()
```

**Resultado:**

* Um gráfico de linha que mostra a tendência mensal de incidentes.
* Cores diferentes representam anos distintos.
* Usado para identificar sazonalidade ou aumento/redução de casos.

---

### **Resumo Prático**

| Etapa                         | Objetivo                                                             |
| ----------------------------- | -------------------------------------------------------------------- |
| **Pré-processamento**         | Limpar e filtrar dados relevantes (2024, colunas essenciais).        |
| **Grafo (NetworkX)**          | Visualizar a relação entre organizações e tipos de vazamentos.       |
| **Gráfico Sunburst (Plotly)** | Analisar a gravidade por organização e tipo, via número de afetados. |
| **Linha temporal (Plotly)**   | Acompanhar a evolução mensal dos incidentes ao longo dos anos.       |
