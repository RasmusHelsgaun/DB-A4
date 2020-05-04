# DB-A4

## Group:
* Nille Løgrup
* Ada-mmm Lassie
* Ralle H8gaun

### Dataset used can be found: [here](https://neo4j.com/graphgist/learning-cypher-with-san-francisco-bay-map/)

* **a)   Identify the most important nodes, based on their relationships**  
```cql
CALL gds.pageRank.stream('Graph', { maxIterations: 20, dampingFactor: 0.85 })
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC, name ASC
```

|Name |Score|
|---|---|
|"Sunnyvale"|	1.4627125821076332|
|"Milpitas"|	1.1650443203281606|
|"Fremont"|	1.0598599660210313|
|"Athetron"|	0.989260438363999|
|"Palo Alto"|	0.9270565305138007|
|"Mountain View"|	0.8505048173945399|
|"Cupertino"|	0.8188349188305439|
|"Santa Clara"|	0.8188349180389197|
|"San Jose"|	0.6113811442395671|

* **b)   Detect the close connected communities of nodes** 
```cql
CALL gds.louvain.stream('Graph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC
```

|Name|CommunityId|
|---|---|
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

|City1|	City2|	Similarity|
|---|---|---|
|"San Jose"|	"Sunnyvale"|	0.75|
|"Sunnyvale"|	"San Jose"|	0.75|
|"Cupertino"|	"Santa Clara"|	0.5|
|"Santa Clara"|	"Cupertino"|	0.5|
|"Athetron"|	"Milpitas"|	0.3333333333333333|
|"Athetron"|	"Mountain View"|	0.3333333333333333|
|"Fremont"|	"Palo Alto"|	0.3333333333333333|
|"Milpitas"|	"Athetron"|	0.3333333333333333|
|"Milpitas"|	"Mountain View"|	0.3333333333333333|
|"Mountain View"|	"Athetron"|	0.3333333333333333|
|"Mountain View"|	"Milpitas"|	0.3333333333333333|
|"Palo Alto"|	"Fremont"|	0.3333333333333333|
|"Cupertino"|	"Milpitas"|	0.25|
|"Cupertino"|	"Mountain View"|	0.25|
|"Fremont"|	"San Jose"|	0.25|
|"Milpitas"|	"Cupertino"|	0.25|
|"Milpitas"|	"Santa Clara"|	0.25|
|"Mountain View"|	"Cupertino"|	0.25|
|"Mountain View"|	"Santa Clara"|	0.25|
|"San Jose"|	"Fremont"|	0.25|
|"Santa Clara"|	"Milpitas"|	0.25|
|"Santa Clara"|	"Mountain View"|	0.25|
|"Cupertino"|	"San Jose"|	0.2|
|"Fremont"|	"Sunnyvale"|	0.2|
|"Palo Alto"|	"Sunnyvale"|	0.2|
|"San Jose"|	"Cupertino"|	0.2|
|"San Jose"|	"Santa Clara"|	0.2|
|"Santa Clara"|	"San Jose"|	0.2|
|"Sunnyvale"|	"Fremont"|	0.2|
|"Sunnyvale"|	"Palo Alto"|	0.2|
|"Cupertino"|	"Sunnyvale"|	0.16666666666666666|
|"Santa Clara"|	"Sunnyvale"|	0.16666666666666666|
|"Sunnyvale"|	"Cupertino"|	0.16666666666666666|
|"Sunnyvale"|	"Santa Clara"|	0.16666666666666666|

* **d)   Find available routes or optimal paths between the nodes** 
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

From "Sunnyvale" to "San Jose" you will have to go through the Node / City "Cupertino".
To see this path, run the query below.
Can also use other city names like "San Jose" and "Mountain View", which will take you through "Milpitas" and "Sunnyvale".

|Name|	Cost|
|---|---|
|"Sunnyvale"|	0.0|
|"Cupertino"|	1.0|
|"San Jose"|	2.0|

```cql
MATCH (start:City {name: 'San Jose'}), (end:City {name: 'Mountain View'})
CALL gds.alpha.shortestPath.stream({
 nodeProjection: 'City',
 relationshipProjection: {
 connect_to: { type: 'connect_to', properties: 'distance', orientation: 'UNDIRECTED' } },
 startNode: start, endNode: end, writeProperty: 'distance'})
YIELD nodeId, cost
RETURN gds.util.asNode(nodeId).name AS name, cost
```

|Name|	Cost|
|---|---|
|"San Jose"|	0.0|
|"Milpitas"|	1.0|
|"Sunnyvale"|	2.0|
|"Mountain View"|	3.0|
