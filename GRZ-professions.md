
### Garzoni economy: about professions

##### Number of profession mentions: total, with, and without profession category:
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?totalProfMention ?profMentionWithoutCategory ?profMentionWithCategory
WHERE 
{ 
{SELECT COUNT (distinct ?prof) AS ?totalProfMention 
 WHERE { ?prof a grz-owl:ProfessionMention .}}

{SELECT COUNT (distinct ?profNonCategorized) AS ?profMentionWithoutCategory 
 WHERE {?profNonCategorized a grz-owl:ProfessionMention. FILTER NOT EXISTS {?profNonCategorized grz-owl:professionCategory ?cat .}}}

{SELECT COUNT (distinct ?profCategorized) AS ?profMentionWithCategory 
 WHERE {?profCategorized a grz-owl:ProfessionMention; grz-owl:professionCategory ?cat .}}
}
```

##### Number of profession mentions per category:
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?cat COUNT (distinct ?prof)
WHERE 
{ 
?prof a grz-owl:ProfessionMention; grz-owl:professionCategory ?cat .
}
GROUP BY ?cat
ORDER BY ASC(?cat)

OR

ORDER BY DESC(COUNT (distinct ?prof))
```


##### Number of master/app/guar/other mentions with more than 1 profession
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?role count (distinct ?pm)
WHERE
{
{ SELECT ?role ?pm COUNT (distinct ?prof) AS ?count 
 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role . } 
 GROUP BY ?role ?pm }
FILTER (?count > 1)
}
```

##### How many person mentions have how many professions (up to 7!)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?NumberOfProfessions COUNT(distinct ?pm) AS ?NumberOfPersonMentions
WHERE
{
{ SELECT ?role ?pm COUNT (distinct ?prof) AS ?NumberOfProfessions 
 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role . } 
 GROUP BY ?role ?pm }
}
ORDER BY ASC(?NumberOfProfessions)
```

##### Person mentions ordered according to their number of professions
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?pm ?count
WHERE
{
{ SELECT ?role ?pm COUNT (distinct ?prof) AS ?count 
 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role . } 
 GROUP BY ?role ?pm }
FILTER (?count > 1)
}
ORDER BY DESC(?count)
```


##### Number of contracts per professions (ok)
```sparql
SELECT ?profSF COUNT (distinct ?contract) AS ?nbContracts
WHERE 
{
  ?contract grz-owl:introduces ?master .
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession/grz-owl:standardForm ?profSF .
}
GROUP BY ?profSF
----
WARNING! might be several prof per master, to be dealt with.
```

##### Number of contracts per profession category (remove or not time window) ok
```sparql
SELECT ?profCAT COUNT (distinct ?contract) AS ?nbContracts
WHERE 
{
  ?contract grz-owl:introduces ?master .
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession/grz-owl:professionCategory ?profCAT .
  ?contract sem:hasTimeStamp ?time .
  FILTER (year(?time) > 1600 AND year(?time) < 1700)
}
GROUP BY ?profCAT
----
WARNING! might be several prof per master, to be dealt with.
```

##### The most present category, with or w/o time window
```sparql
SELECT ?profCAT  (COUNT (distinct ?contract) AS ?nbContracts)
WHERE 
{
  ?contract grz-owl:introduces ?master .
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession/grz-owl:professionCategory ?profCAT .
  ?contract sem:hasTimeStamp ?time .
  FILTER (year(?time) > 1400 AND year(?time) < 1700)
}
GROUP BY ?profCAT
ORDER BY DESC (?nbContracts)
LIMIT 1
```
##### Display info about professions (for norm work) TO UPDATE
```sparql
SELECT ?profWF ?profSF ?profCAT
WHERE 
{
  ?contract grz-owl:introduces ?master .
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession ?prof .
  OPTIONAL {?prof grz-owl:standardForm ?profSF .}
  ?prof grz-owl:writtenForm ?profWF .
  OPTIONAL {?prof grz-owl:professionCategory ?profCAT .
}
```
##### Number of professions without standard form TO UPDATE
```sparql
SELECT count(distinct ?prof)
WHERE 
{
  ?contract grz-owl:introduces ?master .
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession ?prof .
  FILTER NOT EXISTS {?prof grz-owl:standardForm ?profSF .}
}
```
##### Number of professions without category TO UPDATE
```sparql
SELECT count(distinct ?prof)
WHERE 
{
  ?contract grz-owl:introduces ?master .
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession ?prof .
  FILTER NOT EXISTS {?prof grz-owl:professionCategory ?profCAT .}
}
```

##### Number of Masters with 2 professions
```sparql
SELECT count(distinct ?master)
WHERE 
{
  ?master grz-owl:role/grz-owl:roleType grz-owl:master .
  ?master grz-owl:profession ?prof1, ?prof2 .
  FILTER (?prof1 != ?prof2)
}
```

#### Number of distinct profession mentions from Master and Guarantor - STUPID QUERY, TO CORRECT
```sparql
SELECT count(distinct ?prof1)
WHERE 
{
  ?master grz-owl:role ?role .
  {?role grz-owl:roleType grz-owl:master } UNION {?role grz-owl:roleType grz-owl:guarantor}
  ?master grz-owl:profession ?prof1 .
  ?prof1 a grz-owl:ProfessionMention .
}
```
