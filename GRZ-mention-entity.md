### Garzoni People Search

##### How many person mentions
```sparql
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT COUNT (distinct ?pm)
WHERE { ?pm a grz-owl:PersonMention .}
```

##### How many person entities
```sparql
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT COUNT (distinct ?pe)
WHERE { ?pe a common:Person .}
```

##### Get number of mentions per entities (display all entities)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?pe COUNT (distinct ?pm)
WHERE
{
    ?pm core:refersTo ?pe .
    ?pm a common:PersonMention .
    ?pe a common:Person .
}
GROUP BY ?pe
```

##### How many person entities have how many mentions (distribution of entity per counts of mentions)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT  ?mentionCount COUNT (distinct ?pe) AS ?NbPersonEntities
WHERE
{
	SELECT ?pe COUNT (distinct ?pm)  AS ?mentionCount
	WHERE
	{
	    ?pm core:refersTo ?pe .
	    ?pm a common:PersonMention .
	    ?pe a common:Person .
	}
	GROUP BY ?pe
}
GROUP BY ?mentionCount
ORDER BY ASC(?mentionCount)
```

##### Get the top-10/x most mentioned person entities
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?pe COUNT (distinct ?pm)
WHERE
{
     ?pm a common:PersonMention ; core:refersTo ?pe .
     ?pe a common:Person .
}
GROUP BY ?pe
ORDER BY DESC (COUNT (distinct ?pm))
LIMIT 10
```

##### Get the max number of person mentions for an entity
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT MAX (?mentionCount) AS ?maxMention
WHERE
{
    SELECT ?pe (COUNT (distinct ?pm) AS ?mentionCount)
    WHERE
    {
        ?pm a common:PersonMention ; core:refersTo ?pe .
        ?pe a common:Person .
    }
    GROUP BY ?pe
}
```

##### Retrieve person entities having a number of mention > x
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?pe (COUNT (distinct ?pm) AS ?countMention)
    WHERE
    {
         ?pm a common:PersonMention ; core:refersTo ?pe .
         ?pe a common:Person .
    }
    GROUP BY ?pe
HAVING (count(distinct ?pm) > 5)
ORDER BY DESC (count(distinct ?pm))
```

##### How many person entities are mentioned more than x
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT (COUNT (distinct ?pe))
WHERE
{
   SELECT ?pe
   WHERE
    {
         ?pm a common:PersonMention ; core:refersTo ?pe .
         ?pe a common:Person .
    }
    GROUP BY ?pe
    HAVING (COUNT (distinct ?pm) > 1)
}
```

##### Average number of PersonMentions per Person Entities
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT AVG(?mentionCount)
WHERE
{
    SELECT ?pe (COUNT (distinct ?pm) AS ?mentionCount)
    WHERE
    {
        ?pm a common:PersonMention ; core:refersTo ?pe .
        ?pe a common:Person .
    }
    GROUP BY ?pe
}
```













