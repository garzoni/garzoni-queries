#### 02. About person mentions and person entities

##### 1. What is the total number of person mentions?
```sparql
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ( COUNT (distinct ?pm) AS ?NbPersMention )
WHERE { ?pm a common:PersonMention .}
```

##### 2. What is the total number of person entities? 
```sparql
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ( COUNT (distinct ?pe) AS ?NbPersEntities )
WHERE { ?pe a common:Person .}
```

##### 3. How many person entities have how many mentions? (distribution of entity per counts of mentions)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ( COUNT (distinct ?pe) AS ?NbPersonEntities) ?mentionCount
WHERE
{
	SELECT ?pe (COUNT (distinct ?pm)  AS ?mentionCount)
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

##### 4. What is the average number of mentions per person entity?
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT (AVG (?mentionCount) AS ?AvgPersonMention)
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

##### 5. How many person entities have more than x mentions?
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

##### 6.1 Give me all mentions of person entity with id x.
```sparql
SELECT ?pm
WHERE
{
     
     ?pe a common:Person ; dhc:uuid '76b3eb35-6b08-4a51-adfe-13495667ca53' .
     ?pe core:referredBy ?pm .
     ?pm a common:PersonMention 
}
```

##### 6.2 Give me all mentions of person entity with name x.
```sparql
SELECT ?pm
WHERE
{
     
     ?pe a common:Person ; rdfs:label 'Marsilio Mesture (spezier)' .
     ?pe core:referredBy ?pm .
     ?pm a common:PersonMention 
}
```

##### 7.1 Who are the 10 most mentioned person entities?
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?pe ( COUNT (distinct ?pm) AS ?NbMentions )
WHERE
{
     ?pm a common:PersonMention ; core:refersTo ?pe .
     ?pe a common:Person .
}
GROUP BY ?pe
ORDER BY DESC (COUNT (distinct ?pm))
LIMIT 10
```

##### 7.2 Get the top-10/x most mentioned person entities with time window.
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?pe ( COUNT (distinct ?pm) AS ?NbMentions )
WHERE
{
     ?pm a common:PersonMention ; core:refersTo ?pe .
     ?pe a common:Person .
     ?pm core:isMentionedIn ?c .
     ?c sem:hasTimeStamp ?date .
     BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
     FILTER (?myDate < "1653-03-15"^^xsd:dateTime AND ?myDate > "1630-03-15"^^xsd:dateTime)
}
GROUP BY ?pe
ORDER BY DESC (COUNT (distinct ?pm))
LIMIT 10
```

##### 8. Who are the person entities mentioned more than X time?
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

##### 9. What is the distribution of roles of person entities mentioned more than X times?
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT  ?roleType COUNT (distinct ?pe)
WHERE
{
	{
	    SELECT ?pe WHERE
	    { ?pm a common:PersonMention ; core:refersTo ?pe . }
	    GROUP BY ?pe 
	    HAVING (count(distinct ?pm) > 5)
	}
	?pe grz-owl:hasRole ?roleStatement . 
	?roleStatement rdf:value ?roleType .
}
GROUP BY ?roleType 
```

##### 10. Get number of mentions per entities (display all entities)
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
ORDER BY DESC (COUNT (distinct ?pm))
```

