### 07. About guarantors 

##### 1. How many contracts have and have not a guarantor? (api:07_01_nbContracts_with_without_guar)

```sparql
SELECT ?NbContractsWithoutGuar ?NbContractsWithGuar 
WHERE
{
	{ SELECT (COUNT (?contractWO) AS ?NbContractsWithoutGuar )
          WHERE { ?contractWO a grz-owl:Contract . 
		FILTER (NOT EXISTS 
		{ ?contract core:hasMention ?g .
	  	?g grz-owl:hasRole grz-owl:Guarantor . } ) 
               }
	}

	{ SELECT (COUNT (?contractWITH) AS ?NbContractsWithGuar )
          WHERE { ?contractWITH a grz-owl:Contract ; core:hasMention ?g .
	  	?g grz-owl:hasRole grz-owl:Guarantor . }
	}
}
```


##### 2. What is the contract distribution per number of guarantor?  (api:07_02_contracts_distrib_per_nbGuar)
```sparql
SELECT ?guarCount COUNT(distinct ?contract) 
WHERE 
{ 
	SELECT ?contract count(distinct ?guar) AS ?guarCount 
	WHERE 
	{ 
	    ?contract a grz-owl:Contract ; core:hasMention ?guar . 
	    ?guar grz-owl:hasRole grz-owl:Guarantor . 
	} 
	GROUP BY ?contract 
} 
ORDER BY ASC(?guarCount)
```

##### 3. How many apprentices do guarantor have on average? (api:07_03_avg_nbApp_per_guar)
```sparql
SELECT AVG (?numberApp)
WHERE
{
	SELECT (COUNT (distinct ?app) AS ?numberApp)
	WHERE
	{?g  a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Guarantor ; common:isGuarantorOf ?app .}
	GROUP BY ?g
}
```

##### Same wih time window (api:07_03_avg_nbApp_per_guar_withTW)
```sparql
SELECT AVG (?numberApp)
WHERE
{
	SELECT (COUNT (distinct ?app) AS ?numberApp) 
	WHERE
	{?g  a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Guarantor ; common:isGuarantorOf ?app .
         ?g core:referredBy/core:isMentionedIn ?contract .
    	 ?contract sem:hasTimeStamp ?date . 
	 BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
	 BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
	 FILTER (?year > 1595 AND ?year < 1598)
}
GROUP BY ?g
}
```

###### Same with profession category : TODO when prof ready

##### 4. Give me the top x guarantors with the most apprentices. (api:07_04_guar_with_most_app)
````sparql
# N.B.: update limit number od desired
SELECT ?g COUNT (distinct ?app)
WHERE
{ ?g  a common:Person ; grz-owl:hasRole/rdf:value grz-owl:Guarantor ; common:isGuarantorOf ?app . }
GROUP BY ?g
ORDER BY DESC (COUNT (distinct ?app))
LIMIT 10
```` 




