##### 0. How many person entities have a gender information or not
```sparql
SELECT ?with ?without
WHERE
{ 
{SELECT count (?pe) AS ?with WHERE {?pe a common:Person ; foaf:gender ?gender }}
{SELECT count (?pf) AS ?without WHERE {?pf a common:Person . FILTER NOT EXISTS {?pf foaf:gender ?gender} }}
}
```

##### 1. What is the total number of person entities with gender female/male?
```sparql
SELECT ?gender count (distinct ?pe)
WHERE
{ ?pe a common:Person ; foaf:gender ?gender }
GROUP BY ?gender
```

##### 2. What is the distribution of women/men per role?
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?role ?numberOfWomen (?numberOfWomen*100/?total as ?percentWomen) ?numberOfMen (?numberOfMen*100/?total as ?percentMen)
WHERE 
{ 
  { SELECT COUNT (distinct ?pe) AS ?total WHERE {?pe a common:Person. ?pe foaf:gender ?gender} }

  { SELECT ?role COUNT (distinct ?women) AS ?numberOfWomen 
    WHERE {?women a common:Person; grz-owl:hasRole/rdf:value ?role . ?women foaf:gender "female"}   
  }

  { SELECT ?role COUNT (distinct ?men) AS ?numberOfMen 
    WHERE {?men a common:Person; grz-owl:hasRole/rdf:value ?role . ?men foaf:gender "male"}   
  }
}
GROUP BY ?role ?numberOfWomen ?numberOfMen ?total
```
