## Tarefa de Cypher e o FDA Adverse Event Reporting System (FAERS)

## Exercício 1

Escreva uma sentença em Cypher que crie o medicamento de nome `Metamizole`, código no DrugBank `DB04817`.

### Resolução
~~~cypher
CREATE (:Drug {drugbank: "DB04817", name:"Metamizole"})
~~~

## Exercício 2

Considerando que a `Dipyrone` e `Metamizole` são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo `:SameAs` que ligue os dois.

### Resolução
~~~cypher
MATCH (d:Drug {name:"Dipyrone"})
MATCH (m:Drug {name:"Metamizole"})
CREATE (d)-[:SameAs]->(m)
~~~

## Exercício 3

Use o `DELETE` para excluir o relacionamento que você criou (apenas ele).

### Resolução
~~~cypher
MATCH (d:Drug {name:"Dipyrone"})
MATCH (m:Drug {name:"Metamizole"})
MATCH (d)-[e:SameAs]->(m)
DELETE e
~~~

## Exercício 4

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
MERGE (p1)<-[r:Relates]->(p2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1
~~~

## Exercício 5

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
A partir da tabela [drug-use](data/drug-use.csv), são geradas as arestas entre pessoas e medicamentos (usando MERGE para evitar duplicatas):

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/horodynskivitor/mc536-2s2020/development/lab06/data/drug-use.csv' AS line
MERGE (p:Person {code: line.idperson})
MERGE (d:Drug {code: line.codedrug})
MERGE (p)-[:Takes]->(d)
~~~

Pela tabela [sideeffect](data/sideeffect.csv), relaciona-se pessoas aos seus efeitos colaterais:

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/horodynskivitor/mc536-2s2020/development/lab06/data/sideeffect.csv' AS line
MERGE (p:Person {code: line.idPerson})
MERGE (s:SideEffect {code: line.codePathology})
MERGE (s)-[:Affects]->(p)
~~~

Por transitividade, pode-se relacionar drogas e efeitos colaterais:
~~~cypher
MATCH (s)-[:Affects]->(p)-[:Takes]->(d)
MERGE (d)-[c:Causes]->(s)
ON CREATE SET c.weight=1
ON MATCH SET c.weight=c.weight+1
~~~

## Exercício 6

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

### Resolução
~~~cypher
(escreva aqui a resolução em Cypher)
~~~