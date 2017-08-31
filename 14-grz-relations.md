### 14. About person relations.

##### 01. Give me all persons having a relation with person x. (api:14_01_)
```sparql

```

##### 02. Find all persons and their connection (foaf:knows) between minYear and maxYear
```sparql

```


##### 03. Get relations and their respective occurrence number (api:14_03_about_rel)
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


