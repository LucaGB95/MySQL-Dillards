# MySQL-Dillards
Retail Data Set Queries

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
