# DB-A4

## Group:
* Nille Løgrup
* Ada-mmm Lassie
* Ralle H8gaun

### Dataset used: [Link to dataset](https://neo4j.com/graphgist/learning-cypher-with-san-francisco-bay-map/)

* **a)   Identify the most important nodes, based on their relationships** 
Using the page rank algorithm from gds shown below, you'll recieve the following ranks:

**name**	      **score**
"Sunnyvale"	1.4627125821076332
"Milpitas"	1.1650443203281606
"Fremont"	1.0598599660210313
"Athetron"	0.989260438363999
"Palo Alto"	0.9270565305138007
"Mountain View"	0.8505048173945399
"Cupertino"	0.8188349188305439
"Santa Clara"	0.8188349180389197
"San Jose"	0.6113811442395671

```cql
CALL gds.pageRank.stream('Graph', { maxIterations: 20, dampingFactor: 0.85 })
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC, name ASC
```

* **b)   Detect the close connected communities of nodes** 
```cql
CALL gds.louvain.stream('Graph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC
```

|Name|communityId|
|--------------|----|
|"Mountain View"|	1|
|"Palo Alto"|	1|
|"Athetron"|	1|
|"Fremont"|	4|
|"Milpitas"|	4|
|"San Jose"|	4|
|"Sunnyvale"|	7|
|"Santa Clara"|	7|
|"Cupertino"|	7|

* **c)   Discover similarity between nodes, based on their properties or behaviour** 
```cql
CALL gds.nodeSimilarity.stream('Graph')
YIELD node1, node2, similarity
RETURN gds.util.asNode(node1).name AS city1, gds.util.asNode(node2).name AS city2, similarity
ORDER BY similarity DESCENDING, city1, city2
```

* **d)   Find available routes or optimal paths between the nodes** 
From "Sunnyvale" to "San Jose" you will have to go through the Node / City "Cupertino".
To see this path, run the query below.
Can also use other city names like "San Jose" and "Mountain View", which will take you through "Milpitas" and "Sunnyvale".

```cql
MATCH (start:City {name: 'Sunnyvale'}), (end:City {name: 'San Jose'})
CALL gds.alpha.shortestPath.stream({
 nodeProjection: 'City',
 relationshipProjection: {
 connect_to: { type: 'connect_to', properties: 'distance', orientation: 'UNDIRECTED' } },
 startNode: start, endNode: end, writeProperty: 'distance'})
YIELD nodeId, cost
RETURN gds.util.asNode(nodeId).name AS name, cost
```
