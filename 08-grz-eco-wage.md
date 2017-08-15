### 08. About wages

##### 1. How many salaries are progressive vs. non-progressive?
```sparql
SELECT (COUNT (distinct ?salNonProg) AS ?NonProgressive) (COUNT (distinct ?salProg) as ?Progressive)
WHERE 
{
  ?salProg a grz-owl:FinancialCondition ; grz-owl:conditionType grz-owl:SingleSalary .
  ?salNonProg a grz-owl:FinancialCondition ; grz-owl:conditionType grz-owl:ProgressiveSalary .
}
```

##### 2. How many salaries are in goods vs. in money?
```sparql
SELECT (COUNT (distinct ?salGood) AS ?SalIngoods) (COUNT (distinct ?salMoney) AS ?SalInMoney)
WHERE 
{
  ?salGood a grz-owl:FinancialCondition ; grz-owl:paidInGoods 1 . 
  ?salMoney a grz-owl:FinancialCondition ; grz-owl:paidInGoods 0 . 
}
```

##### 3. Who pays the salary?
```sparql
SELECT  ?payer (COUNT (distinct ?sal) AS ?NbSal)
WHERE 
{
  ?sal a grz-owl:FinancialCondition .
  ?sal grz-owl:paidBy ?payer .
}
GROUP BY (?payer)
```

#### 4. In which profession the salary is payed by the master, the apprentice?
```sparql

```
