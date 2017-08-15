### 07. About guarantors 

###### 1. How many contracts have and have not a guarantor?

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
