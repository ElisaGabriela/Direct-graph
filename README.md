# Directed Graph: Building a network with wikipedia

Grupo:

* **Elisa Gabriela Machado de Lucena**
* **Vinicius Soares Fernandes**

Este trabalho tem como objetivo a análise de um rede obtida através da plataforma wikipedia. Para a formação da rede, foi escolhido o tema [termoeconomia](https://en.wikipedia.org/wiki/Thermoeconomics). O projeto foi realizado na linguagem Python com o auxilio da plataforma de desenvolvimento Google Colaboratory. Além disso, foram utilizadas bibliotecas Python como networkx, matplotlib, seaborn. 

Para tratar e manipular os dados da rede escolhida, foi feito uma pipeline de dados, que consiste em uma série de etapas de processamento de dados. Os pipelines de dados consistem em três elementos principais: uma fonte, que neste caso será nossa semente (seed) e nossa lista de paradas - apresentados na sequência) uma ou mais etapas de processamento e um destino, que será nosso arquivo graphml e as análises da rede.

A partir dessa página da Wikipedia, pode-se gerar uma rede, cuja representação visual foi feita com o auxilio do software Gephi.

![Representação visual da rede](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/representa%C3%A7%C3%A3o_visual_da_rede.jfif)

```
  SEED = "Thermoeconomics".title()
  STOPS = ("International Standard Serial Number",
           "International Standard Book Number",
           "National Diet Library",
           "International Standard Name Identifier",
           "International Standard Book Number (Identifier)",
           "Pubmed Identifier",
           "Issn (Identifier)", 
           "Pubmed Central",
           "Digital Object Identifier", 
           "Arxiv",
           "Proc Natl Acad Sci Usa", 
           "Bibcode",
           "Library Of Congress Control Number", 
           "Jstor",
           "Doi (Identifier)",
           "S2Cid (Identifier)",
           "Jstor (Identifier)",
           "Oclc (Identifier)",
           "Isbn (Identifier)",
           "Pmid (Identifier)",
           "Arxiv (Identifier)",
           "Bibcode (Identifier)")


  todo_lst = [(0, SEED)]
  todo_set = set(SEED)
  done_set = set()

  g = nx.DiGraph()
  layer, page = todo_lst[0]

```

Durante a etapa de pré-processamento do pipeline, vamos retirar os nós com apenas 1 vizinho e os nós duplicados, pois apenas vão atrapalhar as análises que desejam ser feitas.

```
while layer < 2:
  del todo_lst[0]
  done_set.add(page)
  
    print(layer, page) 
  
  try:
    wiki = wikipedia.page(page, auto_suggest=False)
  except:
    print("Could not load", page)
    layer, page = todo_lst[0]
    continue
  
  for link in wiki.links:
    link = link.title()
    if link not in STOPS and not link.startswith("List Of"):
      if link not in todo_set and link not in done_set:
        todo_lst.append((layer + 1, link))
        todo_set.add(link)
      g.add_edge(page, link)
  layer, page = todo_lst[0]

```

```
original = g.copy()

g.remove_edges_from(nx.selfloop_edges(g))

duplicates = [(node, node + "s") 
              for node in g if node + "s" in g
             ]

for dup in duplicates:
  g = nx.contracted_nodes(g, *dup, self_loops=False)

print(duplicates)

duplicates = [(x, y) for x, y in 
              [(node, node.replace("-", " ")) for node in g]
                if x != y and y in g]
print(duplicates)

for dup in duplicates:
  g = nx.contracted_nodes(g, *dup, self_loops=False)

nx.set_node_attributes(g, 0,"contraction")
nx.set_edge_attributes(g, 0,"contraction")
```

Agora que nossos dados já foram pré processados e truncados, podemos iniciar as análises da rede, de acordo com as métricas escolhidas. A primeira análise feita, foram dos termos "mais citados", ou seja, os nós mais significativos. Os 10 nós mais significativos foram:

* 71 Ecological Economics
* 68 Thermoeconomics
* 63 Economic
* 56 Degrowth
* 53 Mainstream Economics
* 53 Environmental Economics
* 52 Critique Of Political Economy
* 52 Heterodox Economics
* 52 Political Economy
* 50 Neoclassical Economics

Em seguida, foi feito uma representação dos nós com **Degree Centrality** maior que 47. Degree centrality é uma métrica que representa o número de conexões (ou vizinhos) de um nó.

![Nós com Degree centrality maior que 47](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/degree_centrality.jfif)

A próxima métrica analisada foi a **Closeness Centrality**. Foi feito uma representação dos nós com a closeness centrality maior do que 0,5. Essa métrica verifica a distância média de um nó em relação a todos os outros nós.

![Nós com closeness centrality maior do que 0,5](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/ClosenessGreaterThanHalf.png)

Uma outra métrica analisada foi a **Betweenns Centrality**. Para isso, foram representados os nós com Betweenness centrality maior que 4156. Essa métrica verifica a posição do nó em seu menor caminho.

![Nós com Betweenness centrality maior que 4156](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/BetweennessGreaterThan4156.png)

Por fim, a última métrica escolhida foi a **Eigenvector Centrality**. Essa métrica verifica a importancia de um nó baseado na importancia dos seus vizinhos. Foram representados na figura abaixo todos os nós com Eigenvector Centrality maior que 0.84.

![Nós com Eigenvector Centrality maior que 0.84](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/EigenvectorGreaterThan084.png)

Visando aprofundar as análises da rede, foram construidos gráficos que mostram a distribuição de centralidade, com base no grau. Foram feitas as análises PDF e a CDF. A análise PDF (Função de densidade de probabilidade) foi realizada seguindo os seguintes passos: 

```
plt.style.use("default")
degree_sequence = sorted([d for n, d in gsub.degree()], reverse=True)  

fig, ax = plt.subplots(1,2,figsize=(8,4))

all_data = ax[0].hist(degree_sequence,bins=7)
ax[1].hist(degree_sequence,bins=7,density=True)

ax[0].set_title("Degree Histogram")
ax[0].set_ylabel("Count")
ax[0].set_xlabel("Degree")

ax[1].set_title("Probability Density Function")
ax[1].set_ylabel("Probability")
ax[1].set_xlabel("Degree")

plt.tight_layout()
plt.show()

```

![Probability Density Function](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/PDF_1.png)

```
plt.style.use("fivethirtyeight")

fig, ax = plt.subplots(1,1,figsize=(7,6))

sns.histplot(degree_sequence,bins=7,label="Count",ax=ax)
ax2 = ax.twinx()
sns.kdeplot(degree_sequence,color='r',label="Probability Density Function (PDF)",ax=ax2)

lines, labels = ax.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax2.legend(lines + lines2, labels + labels2, loc=0)

ax.grid(False)
ax2.grid(False)
ax.set_xlabel("Degree")
ax2.set_ylabel("Probability")

plt.show()
```

![Análise PDF](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/PDF_2.png)

A análise CDF (Função de densidade acumulativa) foi realizada seguindo os seguintes passos: 

```
plt.style.use("fivethirtyeight")

fig, ax = plt.subplots(1,1,figsize=(7,6))

sns.histplot(degree_sequence,bins=7,label="Count",ax=ax)
ax2 = ax.twinx()
sns.kdeplot(degree_sequence,color='r',label="Cumulative Density Function (CDF)",ax=ax2,cumulative=True)

lines, labels = ax.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax2.legend(lines + lines2, labels + labels2, loc=0)

ax.grid(False)
ax2.grid(False)
ax.set_xlabel("Degree")
ax2.set_ylabel("Probability")

plt.show()
``` 
![Análise CDF](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/CDF_1.png)

Após essas análises, foi feito um filtro para melhorar a visualização dos resultados:

```
core = [node for node, deg in dict(gsub.degree()).items() if deg > 2]
gsub2 = nx.subgraph(gsub, core)
```

```
plt.style.use("default")
degree_sequence = sorted([d for n, d in gsub2.degree()], reverse=True)  

fig, ax = plt.subplots(1,2,figsize=(8,6))

all_data = ax[0].hist(degree_sequence,bins=7)
ax[1].hist(degree_sequence,bins=7,density=True)

ax[0].set_title("Degree Histogram")
ax[0].set_ylabel("Count")
ax[0].set_xlabel("Degree")

ax[1].set_title("Probability Density Function")
ax[1].set_ylabel("Probability")
ax[1].set_xlabel("Degree")

plt.tight_layout()
plt.show()
```

![Análise PDF com filtro](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/PDF_3.png)


```
plt.style.use("fivethirtyeight")

fig, ax = plt.subplots(1,1,figsize=(7,6))

sns.histplot(degree_sequence,bins=7,label="Count",ax=ax)
ax2 = ax.twinx()
sns.kdeplot(degree_sequence,color='r',label="Probability Density Function (PDF)",ax=ax2)

lines, labels = ax.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax2.legend(lines + lines2, labels + labels2, loc=0)

ax.grid(False)
ax2.grid(False)
ax.set_xlabel("Degree")
ax2.set_ylabel("Probability")

plt.show()
```

![Análise final PDF](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/PDF_4.png)

```
plt.style.use("fivethirtyeight")

fig, ax = plt.subplots(1,1,figsize=(7,6))

sns.histplot(degree_sequence,bins=7,label="Count",ax=ax)
ax2 = ax.twinx()
sns.kdeplot(degree_sequence,color='r',label="Cumulative Density Function (CDF)",ax=ax2,cumulative=True)

lines, labels = ax.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax2.legend(lines + lines2, labels + labels2, loc=0)

ax.grid(False)
ax2.grid(False)
ax.set_xlabel("Degree")
ax2.set_ylabel("Probability")

plt.show()
```
![Análise CDF com filtro](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/CDF_2.png)

Após essas análises de centralidade baseadas no grau, foi utilizada outra métrica, a de Closeness Centrality, obtendo-se os seguintes resultados:

```
plt.style.use("default")
degree_sequence = sorted([v for k, v in nx.closeness_centrality(gsub).items()], reverse=True)  

fig, ax = plt.subplots(1,2,figsize=(8,4))

all_data = ax[0].hist(degree_sequence,bins=7)
ax[1].hist(degree_sequence,bins=7,density=True)

ax[0].set_title("Closeness centrality Histogram")
ax[0].set_ylabel("Count")
ax[0].set_xlabel("Closeness centrality")

ax[1].set_title("Probability Density Function")
ax[1].set_ylabel("Probability")
ax[1].set_xlabel("Closeness centrality")

plt.tight_layout()
plt.show()
```
![PDF closeness](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/PDF_closeness.png)

```
plt.style.use("fivethirtyeight")

fig, ax = plt.subplots(1,1,figsize=(7,6))

sns.histplot(degree_sequence,bins=7,label="Count",ax=ax)
ax2 = ax.twinx()
sns.kdeplot(degree_sequence,color='r',label="Probability Density Function (PDF)",ax=ax2)

lines, labels = ax.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax2.legend(lines + lines2, labels + labels2, loc=0)

ax.grid(False)
ax2.grid(False)
ax.set_xlabel("Closeness centrality")
ax2.set_ylabel("Probability")

plt.show()
```
![PDF closeness](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/PDF_closeness_2.png)

```
plt.style.use("fivethirtyeight")

fig, ax = plt.subplots(1,1,figsize=(7,6))

sns.histplot(degree_sequence,bins=7,label="Count",ax=ax)
ax2 = ax.twinx()
sns.kdeplot(degree_sequence,color='r',label="Cumulative Density Function (CDF)",ax=ax2,cumulative=True)

lines, labels = ax.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax2.legend(lines + lines2, labels + labels2, loc=0)

ax.grid(False)
ax2.grid(False)
ax.set_xlabel("Closeness centrality")
ax2.set_ylabel("Probability")

plt.show()
```
![CDF closeness](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/CDF_closeness.png)

Mais uma análise da rede, foi a comparação entre as métricas. Para isso, seguimos o seguinte passo a passo:

```
bc = pd.Series(nx.betweenness_centrality(gsub))
dc = pd.Series(nx.degree_centrality(gsub))
ec = pd.Series(nx.eigenvector_centrality(gsub))
cc = pd.Series(nx.closeness_centrality(gsub))

df = pd.DataFrame.from_dict({"Betweenness": bc,
                            "Degree": dc,
                            "EigenVector": ec,
                            "Closeness": cc})
df.reset_index(inplace=True,drop=True)
df.head()
```
![Análise das métricas](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/table_1.png)

```
dc = pd.Series(nx.degree_centrality(gsub))
ec = pd.Series(nx.eigenvector_centrality(gsub))
cc = pd.Series(nx.closeness_centrality(gsub))

df = pd.DataFrame.from_dict({"Degree": dc,
                            "EigenVector": ec,
                            "Closeness": cc})
df.reset_index(inplace=True,drop=True)
df.head()

```
![Análise das métricas](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/table_2.png)

```
fig = sns.PairGrid(df)
fig.map_upper(sns.scatterplot)
fig.map_lower(sns.kdeplot, cmap="Reds_r")
fig.map_diag(sns.kdeplot, lw=2, legend=False)

plt.show()

```
![Análise final das métricas](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/analise_1.png)

A análise seguinte foi a 50-core decomposition, que resultou na representação:

![Core decomposition](https://github.com/ElisaGabriela/Direct-graph/blob/main/Imagens/50_core_decomposition.png)

## Pipeline
![Pipeline_Representation](https://user-images.githubusercontent.com/62516296/206930238-5fb56696-afa6-43c9-b0b9-94175884a86e.png)

A pipeline foi usada para obter os dados via web scraping, em seguida, processá-los e obter algumas outras análises tais como:

```
11-12-2022 19:25:53 [INFO] Exploring core composition
11-12-2022 19:25:53 [INFO] K-cores: {2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 43, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 63, 64, 66, 69, 70, 83, 85, 86, 94, 97, 101}
11-12-2022 19:25:53 [INFO] K-cores length: 66
11-12-2022 19:25:53 [INFO] K-shell: ['Birmingham School (Economics)', 'Feminist Economics', 'Thermoeconomics', 'New Neoclassical Synthesis', 'Social Credit', 'Marxism And Keynesianism', 'Rational Expectations', 'Evolutionary Economics', 'Monetarism', 'Structuralist Economics', 'School Of Salamanca', 'Socialist Economics', 'New Institutional Economics', 'Real Business-Cycle Theory', 'Manchester Liberalism', 'Disequilibrium Macroeconomics', 'Carnegie School', 'American School (Economics)', 'Modern Monetary Theory', 'Behavioral Economics', 'Economic System', 'Neoclassical Synthesis', 'Malthusianism', 'Austrian School', 'Neo-Keynesian Economics', 'Lausanne School', 'Constitutional Economics', 'Monetary Circuit Theory', 'Heterodox Economics', 'Mercantilism', 'Schools Of Economic Thought', 'Supply-Side Economics', 'Neo-Malthusianism', 'Kraków School Of Economics', 'Distributism', 'Capability Approach', 'Physiocracy', 'New-Keynesian Economics', 'New Classical Macroeconomics', 'Marginalism', 'Ancient Economic Thought', 'Regulation School', 'French Liberal School', 'Historical School Of Economics', 'Mutualism (Economic Theory)', 'Chartalism', 'Cameralism', 'Public Choice', 'Ricardian Economics', 'Anarchist Economics', 'History Of Economic Thought', 'Saltwater And Freshwater Economics', 'Institutional Economics', 'Neo-Ricardianism', 'Virginia School Of Political Economy', 'Neo-Marxian Economics', 'Freiburg School', 'Buddhist Economics', 'Keynesian Economics', 'Marxian Economics', 'Neoclassical Economics', 'Post-Autistic Economics', 'English Historical School Of Economics', 'Chicago School Of Economics']
11-12-2022 19:25:53 [INFO] K-shell length: 64
```
