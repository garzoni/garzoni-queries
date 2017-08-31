### 08. About salaries

##### 01. How many salaries are progressive vs. non-progressive? (api:08_01_nbSal_progressive_notProgressive)
```sparql
SELECT (COUNT (distinct ?salNonProg) AS ?NonProgressive) (COUNT (distinct ?salProg) as ?Progressive)
WHERE 
{
  ?salProg a grz-owl:FinancialCondition ; grz-owl:conditionType grz-owl:SingleSalary .
  ?salNonProg a grz-owl:FinancialCondition ; grz-owl:conditionType grz-owl:ProgressiveSalary .
}
```

##### 02. How many salaries are in goods vs. in money? (api:08_02_nbSal_inGood_inMoney)
```sparql
SELECT (COUNT (distinct ?salGood) AS ?SalIngoods) (COUNT (distinct ?salMoney) AS ?SalInMoney)
WHERE 
{
  ?salGood a grz-owl:FinancialCondition ; grz-owl:paidInGoods 1 . 
  ?salMoney a grz-owl:FinancialCondition ; grz-owl:paidInGoods 0 . 
}
```

##### 03. Who pays the salary? (api:08_03_who_pays_sal)
```sparql
SELECT  ?payer (COUNT (distinct ?sal) AS ?NbSal)
WHERE 
{
  ?sal a grz-owl:FinancialCondition .
  ?sal grz-owl:paidBy ?payer .
}
GROUP BY (?payer)
```

##### 04. In which profession the salary is payed by the master, the apprentice? (using profession standard forms) (api:08_04_prof_with_sal_paid_by_master_vs_app)
```sparql
SELECT ?profSF  ?NbSalPaidByApp ?NbSalPaidByMaster
WHERE
{
  {SELECT ?profSF (COUNT (distinct ?sal) AS ?NbSalPaidByApp) 
  WHERE {
    ?c core:hasMention ?master ; grz-owl:hasCondition ?sal .
    ?sal a grz-owl:FinancialCondition ; grz-owl:paidBy grz-owl:ApprenticePayer.
    ?master a common:PersonMention ; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession/common:standardForm ?profSF .
    } GROUP BY ?profSF }

  {SELECT ?profSF (COUNT (distinct ?sal) AS ?NbSalPaidByMaster) 
  WHERE {
    ?c core:hasMention ?master ; grz-owl:hasCondition ?sal .
    ?sal a grz-owl:FinancialCondition ; grz-owl:paidBy grz-owl:MasterPayer.
    ?master a common:PersonMention ; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession/common:standardForm ?profSF .
    } GROUP BY ?profSF }
}
GROUP BY ?profSF
ORDER BY ASC(?profSF)
```

##### 05. How many contracts have which type of financial conditions? (api:08_05_distrib_contract_per_finCond_type)
```sparql
SELECT  ?FinancialCondType COUNT (distinct ?c) AS ?NbContracts
WHERE 
{
  ?c grz-owl:hasCondition ?finCond .
  ?finCond  a grz-owl:FinancialCondition ; grz-owl:conditionType ?FinancialCondType .
}
GROUP BY (?FinancialCondType)
```

##### 06. How many contracts have which type of financial conditions, given profession category x? (api:08_06_distrib_contract_per_finCond_type_with_prof_x)
SELECT  ?FinancialCondType COUNT (distinct ?c) AS ?NbContracts
WHERE 
{
  ?c grz-owl:hasCondition ?finCond ; core:hasMention ?master .
  ?finCond  a grz-owl:FinancialCondition ; grz-owl:conditionType ?FinancialCondType .
  ?master grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession/grz-owl:professionCategory  "pittore" .
}
GROUP BY (?FinancialCondType)
