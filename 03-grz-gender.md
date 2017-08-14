#### 03. About gender

##### 1. How many person entities have a gender information or not?
```sparql
SELECT ?with ?without
WHERE
{ 
{SELECT count (?pe) AS ?with WHERE {?pe a common:Person ; foaf:gender ?gender }}
{SELECT count (?pf) AS ?without WHERE {?pf a common:Person . FILTER NOT EXISTS {?pf foaf:gender ?gender} }}
}
```

##### 2. What is the total number of person entities with gender female/male?
```sparql
SELECT ?gender ( COUNT (distinct ?pe) AS ?NbPersonEntity )
WHERE
{ ?pe a common:Person ; foaf:gender ?gender }
GROUP BY ?gender
```

##### 3. What is the distribution of woman entities over time?
```sparql
SELECT ?year ?gender count (distinct ?pe) AS ?NbPerson
{
  ?pe a common:Person ; foaf:gender ?gender ;grz-owl:hasRole ?roleStmt .
  ?roleStmt sem:hasTimeStamp ?date . 
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
  FILTER (?gender = "female")
}
GROUP BY ?year ?gender
ORDER BY ASC (?year)
```

##### 4.1 What is the distribution of women/men per role?
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

##### 4.2. Gender distribution for a given role with time window (on person entities)
```sparql
# params: ?_role
SELECT ?gender COUNT (distinct ?pe) AS ?NbPerson
WHERE
{
  ?pe  a common:Person .
  ?pe grz-owl:hasRole/rdf:value  grz-owl:Guarantor .
  ?pe foaf:gender ?gender .
}
GROUP BY ?gender
```
