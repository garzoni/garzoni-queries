
##### Distribution of contracts per year
```sparql
SELECT ?year COUNT (?c) AS ?NbContracts
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

##### Distribution of contracts per month for a certain year
```sparql
SELECT ?month COUNT (?c) AS ?NbContracts
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

##### Distribution of contracts per day for a certain month/year
```sparql
SELECT ?day COUNT (?c) AS ?NbContracts
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

##### Average number of contract per year/month/day
```sparql
SELECT AVG (?contract)
WHERE
{
  SELECT COUNT (?c) AS ?contract
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

##### Distribution of contracts per register 
```sparql
SELECT ?reg COUNT (?contract)
WHERE 
{ 
  ?reg a meta:Register ; meta:hasPage ?page . 
  ?page edm:realizes ?contract .
  ?contract  a grz-owl:Contract .
}
GROUP BY ?reg
ORDER BY ASC (?reg)
```

##### Number of contracts given a time windows
```sparql
SELECT  COUNT (?contract)
WHERE 
{ 
  ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date.
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
  FILTER (?year > ?_date1 AND ?year < ?_date2)
}
```

##### Get all contracts before year Y
```sparql
SELECT COUNT (?contract)
WHERE {
  ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  FILTER (?myDate < "1623-03-15"^^xsd:dateTime)
}
```

##### Total number of contracts
```sparql
SELECT COUNT (distinct ?contract)
WHERE {?contract a grz-owl:Contract .}
```

##### Average number of contracts per page
```sparql
SELECT AVG (?contractPerPage)
WHERE
{
  SELECT COUNT (?c) AS ?contractPerPage
  WHERE 
  {
    ?c a grz-owl:Contract .
    ?c ^edm:realizes ?page .
    ?page a meta:Page .
  }
  GROUP BY ?page
}
```

##### How many pages have how many contracts
```sparql
SELECT ?contractPerPage count (?page) AS ?nbpage
WHERE
{
  SELECT ?page COUNT (?c) AS ?contractPerPage
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


