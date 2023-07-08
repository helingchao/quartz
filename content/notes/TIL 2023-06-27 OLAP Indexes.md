---
tags: TIL
author: lche
alias: 
---

#### 关于 OLAP Indexes
- [ ] 补充小结

##### OLTP Index, OLAP Index 的差异:
- OLTP Index/B+Tree, 针对选择率低的查询 (low selectivity predicates) , 需要考虑增量 update.
- OLAP 相反, 不需要查找 individual tuples, read-only, 通常是 [[Sequential Scan]].

##### [[Sequential Scan]] 的优化方法 (speed up)
- data prefetch
- clustering/sorting
- task parallelization / multithread
- data parallelization / vectorization
- code specialization / compilation
- materialized/ result caching
- data skipping/Approximate Queries, data pruning

##### Data Pruning
其中 [[Data Pruning]] 使用辅助的数据, 在 evaluating predicates 时跳过一些 data 的处理 (a portions of table), 避免 examining tuples individually, 例如使用 [[Zone Maps ]] 可以跳过一个 page 扫描.

一些 [[Data Pruning]] 的方法:
- [[Zone Maps]]
- [[Bitmap Index]]
- Column Imprints, Column Sketches

Data Pruning 的方法可以使用 lossy 的 meta data, 也可以使用 lossless 的 meta data.
##### Bitmap Index
Bitmap Index 是一个 vector, offset 对应 a tuple, value 关联 table column values(用于处理 query predicate).

设计 Bitmap Index, 重点在于如何设计 encoding scheme, 即 how to represent、organize data in Bitmap.
