### 10.  About professions

##### 1. How many profession mentions per profession category?
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

##### 2. How many master/app/guar/other mentions have more than 1 profession?
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

##### 3. How many person mentions have how many professions? (up to 7!)
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

##### 4. Give me person mentions ordered according to their number of professions.
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>
 
SELECT ?pm ?pmName ?count
WHERE
{
	{ SELECT ?role ?pm ?pmName  COUNT (distinct ?prof) AS ?count 
	 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role ; grz-owl:hasName/rdfs:label ?pmName } 
	 GROUP BY ?role ?pm ?pmName }
	FILTER (?count > 1)
}
ORDER BY DESC(?count)
```

##### 5. Give me the number of contracts per profession category (considering master professions only)
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

##### 6. Same as above with time window
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

##### 7. Give me all apprentice whose profession contains the string "servir"
```sparql
SELECT ?contract ?dhLink ?app ?appLabel  ?profTranscript ?gender ?age ?ageText ?year
WHERE
{
	?app a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice ; 
    	core:isMentionedIn ?contract ;grz-owl:hasName/rdfs:label ?appLabel ; 
    	grz-owl:hasProfession ?prof ; foaf:gender ?gender .
	OPTIONAL {?app foaf:age ?age }
	OPTIONAL {?app grz-owl:ageText ?ageText }
	?prof common:transcript ?profTranscript .
	?profTranscript bif:contains "'servir*'".
	?contract a grz-owl:Contract; sem:hasTimeStamp ?date ; ^edm:realizes ?page.
	?page a meta:Page; meta:isImagedBy/iiif:service ?dhLink .
	BIND (year(?date) AS ?year).
}
ORDER BY ASC(?year)


```
