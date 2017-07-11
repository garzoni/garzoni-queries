##### 1. What is the total number of apprentices? (between date X and date Y? or before/after date X)

###### Entity
SELECT count (distinct ?pe) AS ?NbApprenticeEntity
WHERE
{ ?pe a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice .}

###### Mention
SELECT count (distinct ?pm) AS ?NbApprenticeMention
WHERE
{ ?pm a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice .}


##### 2. Number of apprentices (entity) over the years
```sparql
SELECT ?year count (distinct ?pe) AS ?NbApprenticeEntity
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
