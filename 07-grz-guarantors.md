### 07. About guarantors 

##### 01. How many contracts have and have not a guarantor? (api:07_guar_01_nbContracts_with_without_guar)
```sparql
#+ tags:
#+   - guarantors

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


##### 02. What is the contract distribution per number of guarantor?  (api:07_guar_02_contracts_distrib_per_nbGuar)
```sparql
#+ tags:
#+   - guarantors

SELECT ?guarCount (COUNT(distinct ?contract) AS ?NbContract)
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

##### 03. How many apprentices do guarantor have on average? (api:07_guar_03_avg_nbApp_per_guar)
```sparql
#+ tags:
#+   - guarantors

SELECT (AVG (?numberApp) AS ?AvgContract)
WHERE
{
	SELECT (COUNT (distinct ?app) AS ?numberApp)
	WHERE
	{?g  a common:Person ; grz-owl:hasRole ?r ; common:isGuarantorOf ?app . ?r rdf:value grz-owl:Guarantor .}
	GROUP BY ?g
}
```

##### 04. How many apprentices do guarantor have on average, within a time window? (api:07_guar_04_avg_nbApp_per_guar_withTW)
```sparql
#+ tags:
#+   - guarantors
#+ params: ?_date_start ?_date_end

SELECT (AVG (?numberApp) AS ?AvgContract)
WHERE
{
	SELECT (COUNT (distinct ?app) AS ?numberApp) 
	WHERE
	{?g  a common:Person ; grz-owl:hasRole ?r ; common:isGuarantorOf ?app . ?r rdf:value grz-owl:Guarantor  .
         ?g core:referredBy ?pm . ?pm core:isMentionedIn ?contract .
    	 ?contract sem:hasTimeStamp ?date . 
	 BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
	 BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
	 FILTER ( ?year > ?_date_start)
	 FILTER ( ?year < ?_date_end)
	}
GROUP BY ?g
}
```
##### 05. How many apprentices do guarantor have on average, within a time window and a specific profession category (TBU)? (api:07_guar_05_avg_nbApp_per_guar_withTW_with_profX)
```sparql
#+ tags:
#+   - guarantors
#+ params: ?_date_start ?_date_end

SELECT (AVG (?numberApp) AS ?AvgContract)
WHERE
{
	SELECT (COUNT (distinct ?app) AS ?numberApp) 
	WHERE
	{?g  a common:Person ; grz-owl:hasRole ?r ; common:isGuarantorOf ?app . ?r rdf:value grz-owl:Guarantor  .
         ?g core:referredBy ?pm . ?pm core:isMentionedIn ?contract .
    	 ?contract sem:hasTimeStamp ?date . 
	 BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
	 BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
	 FILTER ( ?year > ?_date_start)
	 FILTER ( ?year < ?_date_end)
	}
GROUP BY ?g
}
```

##### 06. Give me the top x guarantors with the most apprentices. (api:07_guar_06_guar_with_most_app)
````sparql
#+ tags:
#+   - guarantors
# N.B.: update limit number 

SELECT ?g (COUNT (distinct ?app) AS ?NbApp)
WHERE
{ ?g  a common:Person ; common:isGuarantorOf ?app ; grz-owl:hasRole ?r . ?r rdf:value grz-owl:Guarantor . }
GROUP BY ?g
ORDER BY DESC (?NbApp)
LIMIT 10
```` 




