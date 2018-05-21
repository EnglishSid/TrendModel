


## Bloom Queries

`Top Technology`

~~~
match (t:Technology) return t order by t.UCCount DESC limit 20
~~~

`TopTechUseCases`
~~~
match (t:Technology)-[r]-(uc:UseCase) where t.UCCount > 3 and uc.PCount >2 return t,uc, r
~~~

`TopTechUseCasesAll`
~~~
match (t:Technology)-[r]-(uc:UseCase) where t.UCCount > 3 return t,uc, r
~~~

`TopUseCases`

~~~
match (i:Industry)-[r1:HAS]->(m:Mission)-[r2:INCLUDES]->(sp:StrategicPriorities)-[r3:CONTAIN]->(p:Program)-[r4:ADDRESS]->(uc:UseCase)-[r5:REALIZEDBY]->(t:Technology) where t.UCCount > 10 return i,m,sp,p,uc,t
~~~

`TopUseCasesNoTech`
~~~
match (i:Industry)-[r1:HAS]->(m:Mission)-[r2:INCLUDES]->(sp:StrategicPriorities)-[r3:CONTAIN]->(p:Program)-[r4:ADDRESS]->(uc:UseCase) return i,m,sp,p,uc
~~~