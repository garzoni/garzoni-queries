
### 05. About apprentices.

##### 01. What is the total number of apprentice entities?
```sparql
SELECT (COUNT(distinct ?pe) AS ?NbApprenticeEntity)
WHERE
{ ?pe a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice .}
```

##### 02. What is the total number of apprentice entities, with time window?
```sparql
# params: 2 dates
SELECT (COUNT (distinct ?pe) AS ?NbApprenticeEntity)
WHERE
{ 
  ?pe a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice ; core:referredBy/core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  FILTER (?year > 1590 AND ?year < 1598)
}
```

##### 03. What is the total number of apprentice mentions?
```sparql
SELECT (COUNT (distinct ?pm) AS ?NbApprenticeMention)
WHERE
{ ?pm a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice .}
```

##### 04. What is the total number of apprentice mentions, with time window?
```sparql
SELECT (COUNT (distinct ?pm) AS ?NbApprenticeMention)
WHERE
{ 
?pm a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice ; core:isMentionedIn ?contract .
 ?contract sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  FILTER (?year > 1590 AND ?year < 1598)
}
```

##### 05. Get the number of apprentices (entity) over the years.
```sparql
SELECT ?year (COUNT (distinct ?pe) AS ?NbApprenticeEntity)
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

###### 06. What is the distribution of apprentices' ages over the years (based on mentions)?
```sparql
SELECT ?age COUNT (distinct ?app) 
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice .
  ?app foaf:age ?age .
}
GROUP BY ?age
ORDER BY ASC (?age)
```

###### 07. What is the distribution of apprentices' ages over the years, with a certain profession category (based on mentions)?
N.B: not all professions have a category. This is therefore a partial view.
```sparql
# params: ?_prof_cat
SELECT ?age COUNT (distinct ?app) 
WHERE
{
  ?app  a common:PersonMention .
  #?_profCategory is a string: "specchiaio, cappellaio, etc."
  ?app grz-owl:hasRole  grz-owl:Apprentice ; grz-owl:hasProfession/grz-owl:professionCategory ?_profCategory .
  ?app foaf:age ?age .
}
GROUP BY ?age
ORDER BY ASC (?age)
```
###### 08. Same as query 07, with time window.
```sparql
# params: ?_prof_cat, ?_date_start ?_date_end
SELECT ?age COUNT (distinct ?app) 
WHERE
{
  ?app  a common:PersonMention .
  #?_profCategory is a string: "specchiaio, cappellaio, etc."
  ?app grz-owl:hasRole  grz-owl:Apprentice ; grz-owl:hasProfession/grz-owl:professionCategory 'specchiaio' .
  ?app foaf:age ?age .
  ?app core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?date . 
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
  FILTER (?year > 1530 AND ?year < 1700)
}
GROUP BY ?age
ORDER BY ASC (?age)
```

###### 09. What is the average age of apprentices (all, for ages indicated in integers)?
```sparql
SELECT AVG (?age)
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice .
  ?app foaf:age ?age .
}
```

###### 10. What is the average age of apprentices having profession category x?
```sparql
# params: ?_prof_cat
SELECT AVG (?age)
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice ; grz-owl:hasProfession/grz-owl:professionCategory 'specchiaio' .
  ?app foaf:age ?age .
}
```

###### 11. What is the average age of apprentices having profession category x, with time window?
```sparql
# params: ?_prof_cat, ?_date_start ?_date_end
SELECT AVG (?age)
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice ; grz-owl:hasProfession/grz-owl:professionCategory 'specchiaio' .
  ?app foaf:age ?age .
  ?app core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?date . 
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
  FILTER (?year > 1530 AND ?year < 1700)
}
```

##### 12. Get the apprentices who are mentioned in more than x contracts.
```sparql
# params: ?_nbappMentions
SELECT ?app ?appName (COUNT (distinct ?appMention) AS ?nbMentions)
WHERE
{
  ?app  a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice ; rdfs:label ?appName.
  ?app core:referredBy ?appMention .
  ?appMention grz-owl:hasRole grz-owl:Apprentice .
}
GROUP BY ?app ?appName
HAVING  COUNT (distinct ?appMention) > 2
ORDER BY DESC (COUNT (distinct ?appMention))
```

##### 13. Who are the apprentices mentioned in more than 1 contract with different roles?
```sparql
SELECT ?app ?appName (COUNT (distinct ?appMention) AS ?nbMentions)
WHERE
{
  ?app  a common:Person ; grz-owl:hasRole/rdf:value ?role1, ?role2 ; rdfs:label ?appName .
  ?app core:referredBy ?appMention .
  FILTER (?role1 != ?role2)
}
GROUP BY ?app ?appName
HAVING  COUNT (distinct ?appMention) > 1
ORDER BY DESC (COUNT (distinct ?appMention))
```

###### Trying to capture combinations of roles, not working 
```` sparql
SELECT COUNT (distinct ?appMention) AS ?nbMentions ?roles
WHERE
{
  ?app  a common:Person ; grz-owl:hasRole/rdf:value ?role1, ?role2 .
  ?app core:referredBy ?appMention .
  FILTER (?role1 != ?role2)
  BIND (CONCAT(STRAFTER(STR(?role1),"#"), STRAFTER(STR(?role2), "#")) AS ?roles)
}
GROUP BY ?app ?roles
HAVING  COUNT (distinct ?appMention) > 1
ORDER BY DESC (COUNT (distinct ?appMention))
```

