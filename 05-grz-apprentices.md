
### 05. About apprentices.

##### 01. What is the total number of apprentice entities? (api:05_app_01_nb_app_entities)

```sparql
#+ tags:
#+   - apprentices

SELECT (COUNT(distinct ?pe) AS ?NbApprenticeEntity)
WHERE
{ ?pe a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice .}
```

##### 02. What is the total number of apprentice entities, with time window? (api:05_app_02_nb_app_entities_withTW)
```sparql
#+ tags:
#+   - apprentices
#+ params: ?_date1 ?_date2

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

##### 03. What is the total number of apprentice mentions? (api:05_app_03_nb_app_mentions)
```sparql
#+ tags:
#+   - apprentices

SELECT (COUNT (distinct ?pm) AS ?NbApprenticeMention)
WHERE
{ ?pm a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice .}
```

##### 04. What is the total number of apprentice mentions, with time window? (api:05_app_04_nb_app_mentions_withTW)
```sparql
#+ tags:
#+   - apprentices

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

##### 05. Get the number of apprentices (entity) over the years. (api:05_app_05_distrib_app_per_year)
```sparql
#+ tags:
#+   - apprentices

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

##### 06. What is the distriution of apprentices per age (based on mentions)? (api:05_app_06_distrib_app_ages)
```sparql
#+ tags:
#+   - apprentices

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

##### 07. What is the distribution of apprentices per age (based on mentions), with time window? (api:05_app_07_distrib_app_ages_withTW)
```sparql
#+ tags:
#+   - apprentices
#+ params: ?_date_start ?_date_end

SELECT ?age COUNT (distinct ?app) 
WHERE
{
  ?app  a common:PersonMention ;  core:isMentionedIn ?contract .
  ?app grz-owl:hasRole  grz-owl:Apprentice .
  ?app foaf:age ?age .
  ?contract sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  FILTER (?year > 1590 AND ?year < 1598)
}
GROUP BY ?age
ORDER BY ASC (?age)
```

##### 08. What is the distribution of apprentices per age (based on mentions), with a certain profession category? (api:05_app_08_distrib_app_ages_with_profX)
```sparql
#+ tags:
#+   - apprentices
#+ params: ?_prof_cat
# N.B: not all professions have a category. This is therefore a partial view.

SELECT ?age COUNT (distinct ?app) 
WHERE
{
  ?app  a common:PersonMention ;  core:isMentionedIn ?contract .
  #?_profCategory is a string: "specchiaio, cappellaio, etc."
  ?app grz-owl:hasRole  grz-owl:Apprentice ; grz-owl:hasProfession/grz-owl:professionCategory ?_profCategory .
  ?app foaf:age ?age .
}
GROUP BY ?age
ORDER BY ASC (?age)
```

##### 09. What is the distribution of apprentices per age (based on mentions), within a time window and with a certain profession category? (api:05_app_09_distrib_app_ages_with_profX_withTW)
```sparql
#+ tags:
#+   - apprentices
#+ params: ?_prof_cat ?_date_start ?_date_end
 
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
  FILTER (?year > 1400 AND ?year < 1900)
}
GROUP BY ?age
ORDER BY ASC (?age)
```

##### 10. What is the average age of apprentices (all, for ages indicated in integers)? (api:05_app_10_avg_app_age)
```sparql
#+ tags:
#+   - apprentices

SELECT AVG (?age)
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice .
  ?app foaf:age ?age .
}
```

##### 11. What is the average age of apprentices having profession category x? (api:05_app_11_avg_app_age_with_prof_x)
```sparql
#+ tags:
#+   - apprentices
#+ params: ?_prof_cat

SELECT AVG (?age)
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice ; grz-owl:hasProfession/grz-owl:professionCategory 'specchiaio' .
  ?app foaf:age ?age .
}
```

##### 12. What is the average age of apprentices having profession category x, with time window? (api:05_app_12_avg_app_age_with_profX_withTW)
```sparql
#+ tags:
#+   - apprentices
#+ params: ?_prof_cat ?_date_start ?_date_end

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

##### 13. Get the apprentices who are mentioned in more than x contracts. (api:05_app_13_app_with_several_mentions)
```sparql
#+ tags:
#+   - apprentices
#+  params: ?_nbappMentions

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

##### 14. Who are the apprentices mentioned in more than 1 contract with different roles? (api:05_app_14_app_with_several_mentions_with_several_roles)
```sparql
#+ tags:
#+   - apprentices
#+  params: ?_nbappMentions

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

##### 15. Give me the possible apprentice combinations of roles with their frequency (currently not working, to be revised) (api:05_15_app_role_combinations)
```` sparql
#+ tags:
#+   - apprentices

SELECT (COUNT (distinct ?appMention) AS ?nbMentions) ?roles
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

