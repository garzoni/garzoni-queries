#####  IGNORE TO UPDATE Distribution of number of apprentice(s) per master given a time windows and a profession category: (half ok, param implem)

```sparql
#+ tags:
#+   - masters

SELECT  ?numberApp COUNT (distinct ?master)
WHERE
{
  SELECT ?master ?numberApp
  WHERE
  {
    SELECT ?master (COUNT (distinct ?app) AS ?numberApp)
    WHERE
    {
      ?master a grz-owl:Person .
      ?master grz-owl:hasApprentice ?appStatement .
      ?appStatement rdf:value ?app .
      ?appStatement sem:hasBeginTimeStamp ?date .
      ?master grz-owl:hasProfession/rdf:value ?lexEntryProf .
      ?lexEntryProf grz-owl:professionCategory "stampa" .
      FILTER (year(?date) > 1600 AND year(?date) < 1640)
    }
    GROUP BY ?master
  }
GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

#### For map creation (collab Rouen and Riccardo)

```sparql
SELECT  ?apprenticeName ?transcriptGeoOrigins  ?standardGeoOrigins ?transcriptProfMaster  ?standardProfMaster ?year ?month ?day
WHERE 
{ 
  ?contract a grz-owl:Contract; sem:hasTimeStamp ?date .
  ?contract core:hasMention ?apprentice , ?master .
  ?apprentice a common:PersonMention; grz-owl:hasGeographicalOrigin ?GeoOrigins ; grz-owl:hasRole grz-owl:Apprentice ; grz-owl:hasName/rdfs:label ?apprenticeName. 
  ?master a common:PersonMention; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?prof .
  ?prof common:transcript ?transcriptProfMaster .
  OPTIONAL {?prof common:standardForm ?standardProfMaster .}
  ?GeoOrigins common:transcript ?transcriptGeoOrigins .
  OPTIONAL { ?GeoOrigins  common:standardForm ?standardGeoOrigins . }

  BIND (year(?date) AS ?year).
  BIND (month(?date) AS ?month).
  BIND (day(?date) AS ?day).
  FILTER (?year > 1500 AND ?year < 1599)
} 
ORDER BY ASC(?year)
```
