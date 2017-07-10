### Garzoni People Search
#### *About Roles*

##### Role distribution for Person Mentions (soon entities)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?role ?numberOfMentions (?numberOfMentions*100/?total as ?percent)
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

##### TO BE REVISED - NOT WORKING - Role distribution for Person Entities for year Y / for time window
```sparql
SELECT ?roleType COUNT (distinct ?pm)
WHERE
{
  ?pm a core:Person .
  ?pm grz-owl:role ?roleStatement .
  ?roleStatement grz-owl:value ?roleType .
  ?roleStatement sem:hasTimeStamp ?date .
  FILTER (year(?date) = 1583)
  [for time window: FILTER (year(?date) > 1583 AND year(?date) < 1599)]
}
GROUP BY ?roleType
```

##### Role distribution per gender (mention for now, entities later)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?role ?countWomen ?countMen
WHERE
{
  {SELECT ?role COUNT (distinct ?women) AS ?countWomen
    WHERE {?women a common:PersonMention; foaf:gender "female"; grz-owl:hasRole ?role .}}

  {SELECT ?role COUNT (distinct ?men) AS ?countMen 
    WHERE {?men a common:PersonMention; foaf:gender "male"; grz-owl:hasRole ?role .}}  
}
GROUP BY ?role 
```

#### Role distribution: all and per gender
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
  { SELECT COUNT (distinct ?pm) AS ?total WHERE {?pm a common:PersonMention.} }
  { SELECT COUNT (distinct ?pm) AS ?totalMen WHERE {?pm a common:PersonMention; foaf:gender "male".} }
  { SELECT COUNT (distinct ?pm) AS ?totalWomen WHERE {?pm a common:PersonMention; foaf:gender "female".} }

  { SELECT ?role COUNT (distinct ?pm) AS ?countMentions 
    WHERE {?pm a common:PersonMention; grz-owl:hasRole ?role .}
  }

  {SELECT ?role COUNT (distinct ?women) AS ?countWomen
    WHERE {?women a common:PersonMention; foaf:gender "female"; grz-owl:hasRole ?role .}}

  {SELECT ?role COUNT (distinct ?men) AS ?countMen 
    WHERE {?men a common:PersonMention; foaf:gender "male"; grz-owl:hasRole ?role .}}  
}
GROUP BY ?role ?countMentions ?total
ORDER BY desc(?percent)
```


#####  TO BE REVISED - NOT WORKING -  Give me all person entities who appear as Master AND Apprentice
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
              ?roleStatement1  grz-owl:value ?value1.
              ?roleStatement2  grz-owl:value ?value2 .
              ?value1  grz-owl:roleType grz-owl:master.
              ?value2  grz-owl:roleType grz-owl:apprentice .
       }
       GROUP BY ?pe 
    }
  }
  GROUP BY ?roleType
}
GROUP BY ?pe 
HAVING (COUNT (distinct ?roleType) > 1)
```

#####  TO BE REVISED - NOT WORKING - Break-down PERSON/ROLE/DATE for person entities having both Apprentice and Master roles
```sparql
SELECT ?pe ?roleType ?year
  WHERE 
  {
    {
       ?pe  grz-owl:role ?roleStatement.        
       ?roleStatement grz-owl:value ?role .
       ?role grz-owl:roleType ?roleType .
       ?roleStatement sem:hasTimeStamp ?date .
       BIND (year(?date) AS ?year )
    }
    {

       SELECT ?pe
       WHERE {?pe a grz-owl:Person . 
              ?pe  grz-owl:role ?roleStatement1.
              ?pe  grz-owl:role ?roleStatement2. 
              ?roleStatement1  grz-owl:value ?value1.
              ?roleStatement2  grz-owl:value ?value2 .
              ?value1  grz-owl:roleType grz-owl:master.
              ?value2  grz-owl:roleType grz-owl:apprentice .
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
