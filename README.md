# MySQL-Dillards
Retail Data Set Queries  

Topic: Greatest decrease in items sold between Aug-Sept by department/city/state.

Database ua_dillards;
SELECT d.deptdesc, m.store, m.city, m.state,

Count(CASE extract(month from t.saledate) WHEN 8 THEN t.saledate END) AS Augnumsales,
Count(CASE extract(month from t.saledate) WHEN 9 THEN t.saledate END) AS Septnumsales,

(Augnumsales-Septnumsales) AS Saleitemdiff

FROM trnsact t

JOIN store_msa m ON t.store=m.store 
JOIN skuinfo s ON t.sku=s.sku
JOIN deptinfo d ON s.dept=d.dept

WHERE t.stype='P' AND t.saledate<'2005-08-01'

GROUP BY d.deptdesc, m.store, m.city, m.state

ORDER BY Saleitemdiff DESC


 Topic: Brand with greatest Standard Dev. in sale price having  over 100 skus transactions. 


Database ua_dillards;
SELECT DISTINCT top10skus.sku, top10skus.sprice_stdev,top10skus.num_transactions, s.style, s.color, s.size, s.packsize, s.vendor, s.brand

FROM(SELECT TOP 1sku,STDDEV_POP(sprice) AS sprice_stdev,count(sprice) AS num_transactions
FROM trnsact
WHERE stype='P'
GROUP BY sku
HAVING num_transactions>100
ORDER BY sprice_stdev DESC) AS top10skus

JOIN skuinfo s
ON top10skus.sku=s.sku

ORDER BY top10skus.sprice_stdev DESC;



Topic: Department with largest decrease in average daily sales volume between November to December grouped by store, city, and state.


Database ua_dillards;
SELECT d.deptdesc, m.store, m.city, m.state,

SUM(CASE extract(month from t.saledate) WHEN 11 THEN t.amt END) AS novsales,
SUM(CASE extract(month from t.saledate) WHEN 12 THEN t.amt END) AS decsales,

(SUM (CASE extract(month from t.saledate) WHEN 11 THEN t.amt END)/(Count(distinct t.saledate)) AS novdailysales,

(SUM(CASE WHEN EXTRACT (month from t.saledate)=12 THEN t.amt END)/Count(distinct t.saledate)) AS decdailysales,

((dsalesavg-nsalesavg)/(nsalesavg)) AS dailysalesdiffpercent

FROM trnsact t

JOIN store_msa m ON t.store=m.store 
JOIN skuinfo s ON t.sku=s.sku
JOIN deptinfo d ON s.dept=d.dept

WHERE t.stype='P'
Having novsales>1000 
AND decsales>1000 
AND salesdiffpercent>0 
AND COUNT(DISTINCT t.saledate)>20)


GROUP BY d.deptdesc, m.store, m.city, m.state

ORDER BY dailysalesdiffpercent DESC




