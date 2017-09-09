### 03. About gender

##### 01. How many person entities have a gender information or not? (api:03_gender_01_nb_pe_with_genderInfo)
```sparql
#+ tags:
#+   - gender

SELECT ?with ?without
WHERE
{
  {SELECT (COUNT (?pe) AS ?with) WHERE {?pe a common:Person ; foaf:gender ?gender }}
  {SELECT (COUNT (?pf) AS ?without) WHERE {?pf a common:Person . FILTER NOT EXISTS {?pf foaf:gender ?gender} }}
}
```

##### 02. What is the total number of person entities with gender female/male? (api:03_gender_02_nb_pe_with_gender)
```sparql
#+ tags:
#+   - gender

SELECT ?gender ( COUNT (distinct ?pe) AS ?NbPersonEntity )
WHERE
{ ?pe a common:Person ; foaf:gender ?gender }
GROUP BY ?gender
```

##### 03. What is the distribution of woman person entities over time? (api:03_gender_03_women_distrib_per_year)
```sparql
#+ tags:
#+   - gender

SELECT ?year ?gender (COUNT (distinct ?pe) AS ?NbPerson)
{
  ?pe a common:Person ; foaf:gender ?gender ; grz-owl:hasRole ?roleStmt .
  ?roleStmt sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate)
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
  FILTER (?gender = "female")
}
GROUP BY ?year ?gender
ORDER BY ASC (?year)
```

##### 04. What is the distribution of women/men per role? (api:03_gender_04_women_men_per_role)
```sparql
#+ tags:
#+   - gender

SELECT ?role ?numberOfWomen (?numberOfWomen*100/?total AS ?percentWomen) ?numberOfMen (?numberOfMen*100/?total AS ?percentMen)
WHERE
{
  { SELECT (COUNT (distinct ?pe) AS ?total) WHERE {?pe a common:Person. ?pe foaf:gender ?gender} }

  { SELECT ?role (COUNT (distinct ?women) AS ?numberOfWomen )
    WHERE {?women a common:Person; grz-owl:hasRole/rdf:value ?role . ?women foaf:gender "female"}
  }

  { SELECT ?role (COUNT (distinct ?men) AS ?numberOfMen )
    WHERE {?men a common:Person; grz-owl:hasRole/rdf:value ?role . ?men foaf:gender "male"}
  }
}
GROUP BY ?role ?numberOfWomen ?numberOfMen ?total
```

##### 05. What is the gender distribution for a given role and within a given time window (on person entities) ? (api:03_gender_05_gender_distrib_for_role_withTW)
```sparql
#+ tags:
#+   - gender
# params: ?_role ?_date_start ?_date_end

SELECT ?gender (COUNT (distinct ?pe) AS ?NbPerson)
WHERE
{
  ?pe  a common:Person ; grz-owl:hasRole ?roleStmt .
  ?roleStmt sem:hasTimeStamp ?date .
  ?roleStmt rdf:value  grz-owl:Guarantor .
  ?pe foaf:gender ?gender .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate)
  FILTER (?myDate > "1591-03-15"^^xsd:dateTime)
  FILTER (?myDate < "1598-03-15"^^xsd:dateTime)
}
GROUP BY ?gender
```

##### 06. What is the distribution of women/men per profession? (needs profession categories) (api:03_gender_06_distrib_men_women_per_prof)
```sparql
#+ tags:
#+   - gender

TODO
```
