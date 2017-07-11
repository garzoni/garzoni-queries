##### 1. What is the total number of apprentices? (between date X and date Y? or before/after date X)

###### 1.1 Entity
```sarql
SELECT count (distinct ?pe) AS ?NbApprenticeEntity
WHERE
{ ?pe a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Apprentice .}
```

###### 1.2 Mention
```sparql
SELECT count (distinct ?pm) AS ?NbApprenticeMention
WHERE
{ ?pm a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice .}
```

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

##### 3. About the age of apprentices
 N.B: properties to take into account: `foaf:age` as an `int` and `grz-owl:ageText` as a `string` (e.g. "d'ettà maggiore").

###### 3.1 Distribution of apprentices' ages over the years (based on mentions)
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

###### 3.2 Distribution of apprentices' ages over the years, with a certain profession category (based on mentions)
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
###### 3.3 Same as 3.2 with time window
N.B: not all professions have a category. This is therefore a partial view.

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

##### 4. About average age of apprentices

###### 4.1 Average age of apprentice (all)
```sparql
SELECT AVG (?age)
WHERE
{
  ?app  a common:PersonMention .
  ?app grz-owl:hasRole  grz-owl:Apprentice .
  ?app foaf:age ?age .
}
```

###### 4.2 Average age of apprentice having profession category x
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

###### 4.3 Average age of apprentice having professsion category x in time window
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

