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