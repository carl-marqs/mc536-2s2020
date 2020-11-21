## Análise de Redes

### Exercício 1

Calcule o Pagerank do exemplo da Wikipedia em Cypher:

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/network/pagerank/pagerank-wikipedia.csv' AS line
MERGE (p1:WikiPage {name:line.source})
MERGE (p2:WikiPage {name:line.target})
CREATE (p1)-[:WikiLinks]->(p2)
~~~

~~~cypher
CALL gds.graph.create(
  'WikiGraph',
  'WikiPage',
  'WikiLinks'
)
~~~

~~~cypher
CALL gds.pageRank.stream('WikiGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC, name ASC
~~~

Salvando o PageRank em cada um dos nós:

~~~cypher
CALL gds.pageRank.stream('WikiGraph')
YIELD nodeId, score
MATCH (p:WikiPage {name: gds.util.asNode(nodeId).name})
SET p.pagerank = score
~~~


### Exercício 2

Departing from a Drug-Drug graph created in a previous lab, whose relationship determines drugs taken together, apply a community detection in it to see the results.

Criando o grafo a partir dos nós e arestas anteriores:

~~~cypher
CALL gds.graph.create(
  'drugRelationGraph',
  'Drug',
  {
    Relates: {
      orientation: 'UNDIRECTED'
    }
  }
)
~~~

Aplicando o algoritmo de encontro de comunidades:

~~~cypher
CALL gds.louvain.stream('drugRelationGraph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC
~~~

Salvando estas comunidades como propriedades dos nós do grafo:

~~~cypher
CALL gds.louvain.stream('drugRelationGraph')
YIELD nodeId, communityId
MATCH (d:Drug {name: gds.util.asNode(nodeId).name})
SET d.community = communityId
~~~