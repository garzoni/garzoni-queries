### About person relations.

##### 1. Give me all persons having a relation with person x.

##### 2. Find all persons and their connection (foaf:knows) between minYear and maxYear


##### 3. Get relations and their respective occurrence number
```sparql
PREFIX wikiba: <http://wikiba.se/ontology#>

SELECT ?rel count(?rel)
WHERE 
{
  ?pers1 a common:Person .
  ?pers2 a common:Person .
  ?relStmt a wikiba:Statement .
  ?pers1 ?rel ?relStmt .
  ?relStmt rdf:value ?pers2 .
} 
ORDER BY DESC(count(?rel))
```


