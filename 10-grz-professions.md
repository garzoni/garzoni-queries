### 10.  About professions

##### 01. How many profession mentions per profession category? (api:10_prof_01_distrib_nbProf_per_profCat)
```sparql
#+ tags:
#+   - professions 
 
SELECT ?cat (COUNT (distinct ?prof) AS ?NbProf)
WHERE 
{ ?prof a grz-owl:ProfessionMention; grz-owl:professionCategory ?cat }
GROUP BY ?cat
ORDER BY ASC(?cat)

#OR: ORDER BY DESC(COUNT (distinct ?prof))
```

##### 02. How many master/app/guar/other mentions have more than 1 profession? (api:10_prof_02_nbPers_with_role_x_with_several_prof)
```sparql
#+ tags:
#+   - professions 
 
SELECT ?role (COUNT (distinct ?pm) AS ?NbPm)
WHERE
{
	{ SELECT ?role ?pm (COUNT (distinct ?prof) AS ?count)
	 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role . } 
	 GROUP BY ?role ?pm }
	FILTER (?count > 1)
}
```

##### 03. How many person mentions have how many professions? (up to 7!) (api:10_prof_03_nbPers_several_prof)
```sparql
#+ tags:
#+   - professions 
 
SELECT ?NumberOfProfessions (COUNT(distinct ?pm) AS ?NumberOfPersonMentions)
WHERE
{
	{ SELECT ?role ?pm (COUNT (distinct ?prof) AS ?NumberOfProfessions) 
	 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role . } 
	 GROUP BY ?role ?pm }
}
ORDER BY ASC(?NumberOfProfessions)
```

##### 04. Give me person mentions ordered according to their number of professions. (api:10_prof_04_pers_several_prof)
```sparql
#+ tags:
#+   - professions 
 
SELECT ?pm ?pmName ?count
WHERE
{
	{ SELECT ?role ?pm ?pmName  (COUNT (distinct ?prof) AS ?count)
	 WHERE { ?pm a common:PersonMention; grz-owl:hasProfession ?prof ; grz-owl:hasRole ?role ; grz-owl:hasName ?n . ?n rdfs:label ?pmName } 
	 GROUP BY ?role ?pm ?pmName }
	 FILTER (?count > 1)
}
ORDER BY DESC(?count)
```

##### 05. What is the number of contracts per profession category? (considering master professions only) (api:10_prof_05_nbContract_per_profCat)
```sparql
#+ tags:
#+   - professions 

SELECT (?profCat AS ?ProfessionCategory) (COUNT (distinct ?contract) AS ?NumberOfContracts)
WHERE 
{
  ?prof a grz-owl:ProfessionMention; grz-owl:professionCategory ?profCat; ^grz-owl:hasProfession ?master .
  ?master grz-owl:hasRole grz-owl:Master ; core:isMentionedIn ?contract .
  ?contract a grz-owl:Contract; sem:hasTimeStamp ?date .
}
GROUP BY ?profCat
ORDER BY DESC(?NumberOfContracts)
```

##### 06. What is the number of contracts per profession category, with time window? (api:10_prof_06_nbContract_per_profCat_withTW)
```sparql
#+ tags:
#+   - professions 
#+ params: ?_date_start ?_date_end

SELECT (?profCat AS ?ProfessionCategory) (COUNT (distinct ?contract) AS ?NumberOfContracts)
WHERE 
{
  ?prof a grz-owl:ProfessionMention; grz-owl:professionCategory ?profCat; ^grz-owl:hasProfession ?master .
  ?master grz-owl:hasRole grz-owl:Master ; core:isMentionedIn ?contract .
  ?contract a grz-owl:Contract; sem:hasTimeStamp ?date .
  BIND (year(?date) AS ?year).
  FILTER (?year > ?_date_start)
  FILTER (?year < ?_date_end)
}
GROUP BY ?profCat
ORDER BY DESC(COUNT (distinct ?contract))
```

##### 07. Give me all apprentices, along with their related information, whose profession contains the string "servir" (could also be another string) (api:10_prof_07_app_withProf_withString)
```sparql
#+ tags:
#+   - professions 

SELECT (STR(?contractUUID) AS ?contractUUID) (STR(?appUUID) AS ?appUUID) ?DHCLink 
(?app AS ?appLODVIEW) (STR(?appLabel) AS ?appLabel) (GROUP_CONCAT(?otherProfTR;separator=" // ") as ?appProfTranscripts) 
(STR(?g) AS ?gender) ?age (STR(?at) AS ?ageText) (STR(?appGeoOriginTranscript) AS ?appGeoOriginTranscript) 
(STR(?appGeoOriginStandardForm) AS ?appGeoOriginStandardForm ) (STR(?ptMaster) AS ?profMasterTranscript) 
(STR(?ptsf) AS ?profMasterStandardForm) ?year (STR(?date) AS ?fullDate)
WHERE
{
	# info app
	?app dhc:uuid ?appUUID ; core:isMentionedIn ?contract ; grz-owl:hasName ?n . ?n rdfs:label ?appLabel .
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
	?page a meta:Page; meta:isImagedBy ?x . ?x iiif:service ?DHCLink .
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
ORDER BY ASC(?date) ?appLabel
```
