
##### 1. What is the role distribution for person mentions 
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT STRAFTER(STR(?role), "#") AS ?role ?numberOfMentions (?numberOfMentions*100/?total as ?percent)
WHERE 
{ 
  { SELECT COUNT (distinct ?pm) AS ?total WHERE {?pm a common:PersonMention.} }

  { SELECT ?role COUNT (distinct ?pm) AS ?numberOfMentions 
    WHERE {?pm a common:PersonMention; grz-owl:hasRole ?role .}
  }
}
GROUP BY ?role ?numberOfMentions ?total
ORDER BY desc(?percent)
```

##### 2. What is the total number of person entities having role X ? 
```sparql
# param: ?_role (in this ex, apprentice)
SELECT count (distinct ?pe) AS ?NbEntity
WHERE
{ ?pe a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice .}
```

###### Idem with time window:
```sparql 
SELECT ?year count (distinct ?pe) AS ?NbApprenticeEntity
# param: ?_role (in this ex, apprentice)
WHERE
{
  ?pe a common:Person ; grz-owl:hasRole ?roleStmt.
  ?roleStmt sem:hasTimeStamp ?date ; rdf:value grz-owl:Apprentice .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
}
GROUP BY ?year
ORDER BY ASC (?year)
```

##### 3. What is the role distribution per gender (on entities)?
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?role ?countWomen ?countMen
WHERE
{
  {SELECT ?role COUNT (distinct ?women) AS ?countWomen
    WHERE {?women a common:Person; foaf:gender "female"; grz-owl:hasRole/rdf:value ?role .}}

  {SELECT ?role COUNT (distinct ?men) AS ?countMen 
    WHERE {?men a common:Person; foaf:gender "male"; grz-owl:hasRole/rdf:value ?role .}}  
}
GROUP BY ?role 
```

###### 4. Role distribution: Give me an overview of all and per gender
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT 
?role 
?countMentions (?countMentions*100/?total AS ?percentAll) 
?countWomen (?countWomen*100/?totalWomen AS ?percentWomen)
?countMen (?countMen*100/?totalMen As ?percentMen)
WHERE 
{ 
  { SELECT (COUNT (distinct ?pm) AS ?total) WHERE {?pm a common:PersonMention.} }
  { SELECT (COUNT (distinct ?pm) AS ?totalMen) WHERE {?pm a common:PersonMention; foaf:gender "male".} }
  { SELECT (COUNT (distinct ?pm) AS ?totalWomen) WHERE {?pm a common:PersonMention; foaf:gender "female".} }

  { SELECT ?role (COUNT (distinct ?pm) AS ?countMentions) 
    WHERE {?pm a common:PersonMention; grz-owl:hasRole ?role .}
  }

  {SELECT ?role (COUNT (distinct ?women) AS ?countWomen)
    WHERE {?women a common:PersonMention; foaf:gender "female"; grz-owl:hasRole ?role .}}

  {SELECT ?role (COUNT (distinct ?men) AS ?countMen) 
    WHERE {?men a common:PersonMention; foaf:gender "male"; grz-owl:hasRole ?role .}}  
}
GROUP BY ?role ?countMentions ?total
```
  
##### 5. Give me all person entities having a the double role master/apprentice.
N.B: roles can be changed/added
```sparql
SELECT ?pe COUNT (distinct ?pm) AS ?nbMentions
WHERE
{
  ?pe  a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice ; grz-owl:hasRole/rdf:value grz-owl:Master .
  ?pe core:referredBy ?pm .
}
GROUP BY ?pe
HAVING  COUNT (distinct ?pm) > 1
ORDER BY DESC (COUNT (distinct ?pm))
```

###### 6. How many entities have a double role?
N.B: roles can be changed/added
```sparql
SELECT COUNT (distinct ?pe)
WHERE
{
  ?pe  a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice ; grz-owl:hasRole/rdf:value grz-owl:Master .
}
```

#####  TO BE REVISED - NOT WORKING - Break-down PERSON/ROLE/DATE for person entities having both Apprentice and Master roles
```sparql
SELECT ?pe ?roleType ?year
WHERE 
  {
    {
       ?pe  grz-owl:hasRole ?roleStatement.        
       ?roleStatement grz-owl:value ?roleType .
       ?roleStatement sem:hasTimeStamp ?date .
       BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
       BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
    }
    {

     SELECT ?pe
     WHERE {
      ?pe a common:Person . 
      ?pe  grz-owl:hasRole ?roleStatement1.
      ?pe  grz-owl:hasRole ?roleStatement2. 
      ?roleStatement1  rdf:value grz-owl:Apprentice .
      ?roleStatement2  rdf:value grz-owl:Master .
      }
      GROUP BY ?pe   
    }
  }
GROUP BY ?roleType
ORDER BY ASC ( UCASE (str(?pe)))
```
#####  TO BE REVISED - NOT WORKING - Give me the persons who appear as Master AND Apprentice AND Guarantor:
```sparql
SELECT ?pe 
WHERE 
{
  SELECT ?pe ?roleType
  WHERE 
  {
    {
       ?pe  grz-owl:role ?roleStatement.        
       ?roleStatement grz-owl:value ?role .
       ?role grz-owl:roleType ?roleType .
    }
    {
       SELECT ?pe
       WHERE 
       {
              ?pe a grz-owl:Person . 
              ?pe  grz-owl:role ?roleStatement1.
              ?pe  grz-owl:role ?roleStatement2.
              ?pe  grz-owl:role ?roleStatement3.
              ?roleStatement1  grz-owl:value ?value1.
              ?roleStatement2  grz-owl:value ?value2 .
              ?roleStatement3  grz-owl:value ?value3 .
              ?value1  grz-owl:roleType grz-owl:master.
              ?value2  grz-owl:roleType grz-owl:apprentice .
              ?value3  grz-owl:roleType grz-owl:guarantor .
       }
       GROUP BY ?pe 
    }
  }
  GROUP BY ?roleType
}
GROUP BY ?pe 
HAVING (COUNT (distinct ?roleType) > 2)
```

#####  TO BE REVISED - NOT WORKING - Number of guarantor per contract given a profession
```sparql
SELECT ?numberOfGuar COUNT (distinct ?app)
WHERE
{
SELECT ?app COUNT (distinct ?guar) AS ?numberOfGuar
WHERE 
{
?guar a grz-owl:Person . 
?app a grz-owl:Person .
?master a grz-owl:Person .
?guar  grz-owl:role/grz-owl:value/grz-owl:roleType grz-owl:guarantor .
?master  grz-owl:role/grz-owl:value/grz-owl:roleType grz-owl:master .
?app  grz-owl:role/grz-owl:value/grz-owl:roleType grz-owl:apprentice .
?app grz-owl:has_master/grz-owl:value ?master .
?app grz-owl:has_guarantor/grz-owl:value ?guar .
?master grz-owl:profession/grz-owl:value/grz-owl:professionCategory "stampa" .
}
GROUP BY ?app
}
GROUP BY ?numberOfGuar
ORDER BY ASC (?numberOfGuar)
```

#####  TO BE REVISED - NOT WORKING - Give me apprentices who have the same guarantor in 2 different contracts
```sparql

SELECT ?app ?guar COUNT (distinct ?contract) AS ?cnb
WHERE 
{
?guar a grz-owl:Person . 
?app a grz-owl:Person .
?guar  grz-owl:role/grz-owl:value/grz-owl:roleType grz-owl:guarantor .
?app  grz-owl:role/grz-owl:value/grz-owl:roleType grz-owl:apprentice .
?app grz-owl:has_guarantor/grz-owl:value ?guar .
?guar grz-owl:has_mention/grz-owl:is_entityLink_of/grz-owl:introduced_in ?contract .
?app grz-owl:has_mention/grz-owl:is_entityLink_of/grz-owl:introduced_in ?contract .
}
GROUP BY ?app ?guar
HAVING (COUNT (distinct ?contract) > 1)
```
