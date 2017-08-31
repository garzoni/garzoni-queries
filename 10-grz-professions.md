### 10.  About professions

##### 01. How many profession mentions per profession category?
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

##### 02. How many master/app/guar/other mentions have more than 1 profession?
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

##### 03. How many person mentions have how many professions? (up to 7!)
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

##### 04. Give me person mentions ordered according to their number of professions.
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

##### 05. Give me the number of contracts per profession category (considering master professions only)
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

##### 06. Same as above with time window
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

##### 07. Give me all apprentice whose profession contains the string "servir" (or another string)
```sparql
SELECT (STR(?contractUUID) AS ?contractUUID) (STR(?appUUID) AS ?appUUID) ?DHCLink (?app AS ?appLODVIEW) (STR(?appLabel) AS ?appLabel) (GROUP_CONCAT(?otherProfTR;separator=" // ") as ?appProfTranscripts) (STR(?g) AS ?gender) ?age (STR(?at) AS ?ageText) (STR(?appGeoOriginTranscript) AS ?appGeoOriginTranscript) (STR(?appGeoOriginStandardForm) AS ?appGeoOriginStandardForm ) (STR(?ptMaster) AS ?profMasterTranscript) (STR(?ptsf) AS ?profMasterStandardForm) ?year STR(?date) AS ?fullDate
WHERE
{
	# info app
	?app dhc:uuid ?appUUID ; core:isMentionedIn ?contract ; grz-owl:hasName/rdfs:label ?appLabel .
        OPTIONAL {?app foaf:gender ?g .}
	OPTIONAL {?app foaf:age ?age }
	OPTIONAL {?app grz-owl:ageText ?at }
        OPTIONAL {?app grz-owl:hasGeographicalOrigin ?appGeoOrigin.
                 ?appGeoOrigin common:transcript ?appGeoOriginTranscript .
                 OPTIONAL {?appGeoOrigin common:standardForm ?appGeoOriginStandardForm} }
         
	# get other professions
        OPTIONAL {?app grz-owl:hasProfession ?otherProf .
	?otherProf common:transcript ?otherProfTR .}
         
        
	# info contract
	?contract a grz-owl:Contract; dhc:uuid ?contractUUID ; sem:hasTimeStamp ?date ; ^edm:realizes ?page ; core:hasMention ?master .
	?page a meta:Page; meta:isImagedBy/iiif:service ?DHCLink .
	BIND (year(?date) AS ?year).

        # info master 
        ?master grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?profMaster .
        ?profMaster common:transcript ?ptMaster .
        OPTIONAL { ?profMaster common:standardForm ?ptsf.}
	
	# nested query to select app with "servir"
	{SELECT ?app  ?profTR
	WHERE {?app a common:PersonMention ;  grz-owl:hasRole grz-owl:Apprentice ;  grz-owl:hasProfession ?prof.
		?prof common:transcript ?profTR .
		?profTR bif:contains "'servir*'".}
	}
}
ORDER BY ASC(?year) ?appLabel
```
