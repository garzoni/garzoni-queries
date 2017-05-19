
## Garzoni economy: about professions

### Exploring professions

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

##### Number of contracts per profession category (considering master only)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?profCat AS ?ProfessionCategory COUNT (distinct ?contract) AS ?NumberOfContracts
WHERE 
{
  ?prof a grz-owl:ProfessionMention; grz-owl:professionCategory ?profCat; ^grz-owl:hasProfession ?master .
  ?master grz-owl:hasRole grz-owl:Master ; core:isMentionedIn ?contract .
  ?contract a grz-owl:Contract; sem:hasTimeStamp ?date .
}
GROUP BY ?profCat
ORDER BY DESC(COUNT (distinct ?contract))
```

##### Same as above with time window
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?profCat AS ?ProfessionCategory COUNT (distinct ?contract) AS ?NumberOfContracts
WHERE 
{
  ?prof a grz-owl:ProfessionMention; grz-owl:professionCategory ?profCat; ^grz-owl:hasProfession ?master .
  ?master grz-owl:hasRole grz-owl:Master ; core:isMentionedIn ?contract .
  ?contract a grz-owl:Contract; sem:hasTimeStamp ?date .
  BIND (year(?date) AS ?year).
  FILTER (?year > 1651 AND ?year < 1700)
}
GROUP BY ?profCat
ORDER BY DESC(COUNT (distinct ?contract))
```

## About profession normalisation

##### Number of profession mentions: total, with,  without transcript (TR), standardForm (SF), category (CAT):
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?totalProfMention ?ProfMention_TR_SF_CAT ?ProfMention_TR_SF_NoCAT ?ProfMention_TR_NoSF_NoCAT  ?ProfMention_NoTR_SF_NoCAT ?ProfMention_TR_NoSF_CAT
WHERE 
{ 
{SELECT COUNT (distinct ?prof) AS ?totalProfMention 
 WHERE { ?prof a grz-owl:ProfessionMention .}}

{SELECT COUNT (distinct ?prof_TR_SF_CAT) AS ?ProfMention_TR_SF_CAT
 WHERE {?prof_TR_SF_CAT a grz-owl:ProfessionMention; common:transcript ?t ; common:standardForm ?sf ; grz-owl:professionCategory ?cat .}}

{SELECT COUNT (distinct ?prof_TR_SF_NoCat) AS ?ProfMention_TR_SF_NoCAT
 WHERE {?prof_TR_SF_NoCat a grz-owl:ProfessionMention; common:transcript ?t ; common:standardForm ?sf .
 FILTER NOT EXISTS {?prof_TR_SF_NoCat grz-owl:professionCategory ?cat .}}}

{SELECT COUNT (distinct ?prof_TR_NoSF_NoCat) AS ?ProfMention_TR_NoSF_NoCAT
 WHERE {?prof_TR_NoSF_NoCat a grz-owl:ProfessionMention; common:transcript ?t .
 FILTER NOT EXISTS {?prof_TR_NoSF_NoCat grz-owl:professionCategory ?cat .}
 FILTER NOT EXISTS {?prof_TR_NoSF_NoCat common:standardForm ?sf .}}}

{SELECT COUNT (distinct ?prof_NoTR_SF_NoCat) AS ?ProfMention_NoTR_SF_NoCAT
 WHERE {?prof_NoTR_SF_NoCat a grz-owl:ProfessionMention; common:standardForm ?sf .
 FILTER NOT EXISTS {?prof_NoTR_SF_NoCat grz-owl:professionCategory ?cat .} 
 FILTER NOT EXISTS {?prof_NoTR_SF_NoCat common:transcript ?t .}}}

{SELECT COUNT (distinct ?prof_TR_NoSF_CAT) AS ?ProfMention_TR_NoSF_CAT
 WHERE {?prof_TR_NoSF_CAT a grz-owl:ProfessionMention; common:transcript ?t; grz-owl:professionCategory ?cat .
 FILTER NOT EXISTS {?prof_TR_NoSF_CAT  common:standardForm ?sf . }}}
}
```

##### Display TR/SF/CAT of all profession mentions, grouped and alphabetically orderd by transcripts
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?ProfTrancript ?ProfStandardFrom ?ProfCategory
WHERE 
{
  ?prof a grz-owl:ProfessionMention; common:transcript ?ProfTrancript .
  OPTIONAL {?prof common:standardForm ?ProfStandardFrom .}
  OPTIONAL {?prof grz-owl:professionCategory ?ProfCategory .}
}
GROUP BY ?ProfTrancript
ORDER BY ASC(?ProfTrancript)
```

##### Number of unique transcripts forms that do not have SF nor CAT
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT COUNT (distinct ?ProfTR) AS ?NumberUniqueOrphanTR
WHERE
{
 ?prof a grz-owl:ProfessionMention; common:transcript ?ProfTR .
 FILTER NOT EXISTS {?prof grz-owl:professionCategory ?cat .}
 FILTER NOT EXISTS {?prof common:standardForm ?sf .}
}
```

##### List of unique transcripts that do not have SF nor CAT
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT  distinct ?ProfTR
 WHERE 
{
?prof a grz-owl:ProfessionMention; common:transcript ?ProfTR .
 FILTER NOT EXISTS {?prof grz-owl:professionCategory ?cat .}
 FILTER NOT EXISTS {?prof common:standardForm ?sf .}
}
ORDER BY ASC(?ProfTR)
```
