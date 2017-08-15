#### 01. About archives

##### 1. What is the distribution of contracts per year/month/day?

###### 1.1 Distribution of contracts per year (api:01_01_distrib_contract_year)
```sparql
SELECT ?year (COUNT (?c) AS ?NbContracts)
WHERE 
{
  ?c a grz-owl:Contract .
  ?c sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
}
GROUP BY ?year
ORDER BY ASC (?year)
```

###### 1.2 Distribution of contracts per month for a certain year (api:01_02_distrib_contract_year_month)
```sparql
# params: ?_year
SELECT ?month (COUNT (?c) AS ?NbContracts)
WHERE 
{
  ?c a grz-owl:Contract .
  ?c sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  BIND(IF(?myDate != "NO DATE", month(?myDate), "NODATE") AS ?month).
  FILTER (?year = ?_year)
}
GROUP BY ?month
ORDER BY ASC (?month)
```

###### 1.3 Distribution of contracts per day for a certain month/year (api:01_03_distrib_contract_year_month_day)
```sparql
# params: ?_year ?_month
SELECT ?day (COUNT (?c) AS ?NbContracts)
WHERE 
{
  ?c a grz-owl:Contract .
  ?c sem:hasTimeStamp ?date .
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

##### 2. What is the average number of contracts per year/month/day? (api:02_avg_contract_year_month_day)
```sparql
SELECT AVG (?contract)
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

##### 3. What is the distribution of contracts per register? (api:03_distrib_contract_register)
```sparql
SELECT ?reg (COUNT (?contract) AS ?NbContracts)
WHERE 
{ 
  ?reg a meta:Register ; meta:hasPage ?page . 
  ?page edm:realizes ?contract .
  ?contract  a grz-owl:Contract .
}
GROUP BY ?reg
ORDER BY ASC (?reg)
```

##### 4. What is the number of contracts given a time window? (api:04_nb_contract_tw)
```sparql
# params: ?_date1 ?_date2
SELECT  (COUNT (?contract) AS ?NbContracts)
WHERE 
{ 
  ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date.
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  FILTER (?year > ?_date1 AND ?year < ?_date2)
}
```

##### 5. Give me all contracts before year Y. (api:05_all_contracts_before_year)
```sparql
# params: ?_date
SELECT ( COUNT (?contract) AS ?NbContracts )
WHERE {
  ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  FILTER (?myDate < "1623-03-15"^^xsd:dateTime)
}
```

##### 6. What is the total number of contracts? (api:06_nb_contracts)
```sparql
SELECT ( COUNT (distinct ?contract) AS ?NbContracts )
WHERE {?contract a grz-owl:Contract .}
```

##### 7. What is the average number of contracts per page? (api:07_avg_nb_contracts_page)
```sparql
SELECT (AVG (?contractPerPage) AS ?AvgContractsPerPage)
WHERE
{
  SELECT (COUNT (?c) AS ?contractPerPage)
  WHERE 
  {
    ?c a grz-owl:Contract .
    ?c ^edm:realizes ?page .
    ?page a meta:Page .
  }
  GROUP BY ?page
}
```

##### 8. How many pages have how many contracts? (api:07_distrib_contracts_page)
```sparql
SELECT ?contractPerPage (COUNT (?page) AS ?nbpage)
WHERE
{
  SELECT ?page (COUNT (?c) AS ?contractPerPage)
  WHERE 
  {
    ?c a grz-owl:Contract .
    ?c ^edm:realizes ?page .
    ?page a meta:Page .
  }
  GROUP BY ?page
  ORDER BY ASC(?page)
}
ORDER BY ASC(?contractPerPage)
```


