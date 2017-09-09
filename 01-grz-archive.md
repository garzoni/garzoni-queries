### 01. About archives

##### 01. What is the distribution of contracts per year? (api:01_archive_01_distrib_contract_year)
```sparql
#+ tags:
#+   - barchart
#+   - archive

SELECT ?year (COUNT (?c) AS ?NbContracts)
WHERE 
{
  ?c a grz-owl:Contract ; sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
}
GROUP BY ?year
ORDER BY ASC (?year)
```

##### 02. What is the distribution of contracts per month for a certain year? (api:01_archive_02_distrib_contract_year_month)
```sparql

#+ tags:
#+   - barchart
#+   - archive
#+ params: ?_year

SELECT ?month (COUNT (?c) AS ?NbContracts)
WHERE 
{
  ?c a grz-owl:Contract ; sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  BIND(IF(?myDate != "NO DATE", month(?myDate), "NODATE") AS ?month).
  FILTER (?year = ?_year)
}
GROUP BY ?month
ORDER BY ASC (?month)
```

##### 03. What is the distribution of contracts per day for a certain month/year? (api:01_archive_03_distrib_contract_year_month_day)
```sparql
#+ tags:
#+   - barchart
#+   - archive
#+ params: ?_year ?_month

SELECT ?day (COUNT (?c) AS ?NbContracts)
WHERE 
{
  ?c a grz-owl:Contract ; sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  BIND(IF(?myDate != "NO DATE", month(?myDate), "NODATE") AS ?month).
  BIND(IF(?myDate != "NO DATE", day(?myDate), "NODATE") AS ?day).
  FILTER (?year = ?_year)
  FILTER (?month = ?_month)
}
GROUP BY ?day
ORDER BY ASC (?day)
```

##### 04. What is the average number of contracts per year/month/day, in absolute? (api:01_archive_04_avg_contract_year_month_day_absolute)
```sparql
#+ tags:
#+   - number
#+   - archive
#+ params: ?_year 

SELECT (AVG (?contract) AS ?AvgContractNb )
WHERE
{
  SELECT (COUNT (?c) AS ?contract)
  WHERE 
  {
    ?c a grz-owl:Contract .
    ?c sem:hasTimeStamp ?date .
    BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
    BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
    BIND(IF(?myDate != "NO DATE", month(?myDate), "NODATE") AS ?month).
    BIND(IF(?myDate != "NO DATE", day(?myDate), "NODATE") AS ?day).
  }
  # for average per day:
  # GROUP BY ?year ?month ?day
  # for avergae per month:
  # GROUP BY ?year ?month
  # for average per year:
  # GROUP BY ?year
}
```

##### 05. What is the distribution of contracts per register? (api:01_archive_05_distrib_contract_register)
```sparql
#+ tags:
#+   - barchart
#+   - archive
#+ params: ?_year 

SELECT (STR(?label) AS ?label) (COUNT (?contract) AS ?NbContracts)
WHERE 
{ 
  ?reg a meta:Register ; meta:hasPage ?page ; rdfs:label ?label .
  ?page edm:realizes ?contract .
  ?contract  a grz-owl:Contract .
}
GROUP BY ?label
ORDER BY ASC (?label)
```

##### 06. What is the total number of contracts between date x and date y? or before/after date x? (api:01_archive_06_nb_contract_withTW)
```sparql
#+ tags:
#+   - number
#+   - archive
#+ params: ?_date1 ?_date2

SELECT (COUNT (?contract) AS ?NbContracts)
WHERE 
{ 
  ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date.
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year) 
  FILTER (?year > ?_date1)
  FILTER (?year < ?_date2)
}
```

##### 07. Give me all contracts before year x. (api:01_archive_07_all_contracts_before_year)
```sparql
#+ tags:
#+   - number
#+   - archive
# params: ?_date

SELECT (COUNT (?contract) AS ?NbContracts)
WHERE {
  ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  #FILTER (?myDate < ?_date^^xsd:dateTime)
  FILTER (?myDate < ?_date)
}
```

##### 08. What is the total number of contracts? (api:01_archive_08_nb_contracts)
```sparql
#+ tags:
#+   - number
#+   - archive

SELECT (COUNT (distinct ?contract) AS ?NbContracts)
WHERE {?contract a grz-owl:Contract .}
```

##### 09. What is the average number of contracts per page? (api:01_archive_09_avg_nb_contracts_page)
```sparql
#+ tags:
#+   - number
#+   - archive

SELECT (AVG (?contractPerPage) AS ?AvgContractsPerPage)
WHERE
{
  SELECT (COUNT (?c) AS ?contractPerPage)
  WHERE 
  {
    ?c a grz-owl:Contract ; ^edm:realizes ?page .
    ?page a meta:Page .
  }
  GROUP BY ?page
}
```

##### 10. How many pages have how many contracts? (api:01_archive_10_distrib_contracts_page)
```sparql
#+ tags:
#+   - number
#+   - archive

SELECT ?contractPerPage (COUNT (?page) AS ?nbpage)
WHERE
{
  SELECT ?page (COUNT (?c) AS ?contractPerPage)
  WHERE 
  {
    ?c a grz-owl:Contract ; ^edm:realizes ?page .
    ?page a meta:Page .
  }
  GROUP BY ?page
  ORDER BY ASC(?page)
}
ORDER BY ASC(?contractPerPage)
```


##### 11. Give me some information about the registers (api:01_archive_11_info_register)
```sparql
#+ tags:
#+   - table
#+   - archive

SELECT ?reg STR(?label) AS ?label ?start ?end STR(?pageNb) AS ?pageNb
WHERE 
{ ?reg a meta:Register ; sem:hasBeginTimeStamp  ?start ;  sem:hasEndTimeStamp ?end ; rdfs:label ?label ; dhc:pageCount ?pageNb .}
ORDER BY ?reg
```


