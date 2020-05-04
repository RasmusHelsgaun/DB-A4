# DB-A4

## Group:
* Nille Lørup
* Adam Lassie
* Rasmus H8gaun

### Dataset used:
[Link to dataset](https://neo4j.com/graphgist/learning-cypher-with-san-francisco-bay-map/)

* **a)   Identify the most important nodes, based on their relationships** 
```cypher
MATCH (a)-->(b)
RETURN b, COLLECT(a) as relationships
ORDER BY SIZE(relationships) DESC LIMIT 10
```

* **b)   Detect the close connected communities of nodes** 

* **c)   Discover similarity between nodes, based on their properties or behaviour** 

* **d)   Find available routes or optimal paths between the nodes** 
