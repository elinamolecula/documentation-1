---
id: intersect
title: Intersect()
sidebar_label: Intersect()
---

The `Intersect()` query takes one or more arguments -- each a [row call](/data-querying/pql#row-calls){:target="_blank"} -- and intersects them. You can think of `Intersect()` as **AND** in set theory. It returns the set of record IDs / keys that is in the first set of record IDs / keys AND is in the second set of record IDs / keys AND so on and so forth. 

`Intersect()` is a [row call](/data-querying/pql#row-calls){:target="_blank"}.

## Call Definition

```
Intersect(ROW_CALL, ... )
```

#### Mandatory Arguments
- `ROW_CALL` : the output of any [row call](/data-querying/pql#row-calls){:target="_blank"} (set of record IDs / keys)

#### Optional Arguments
- `...` : Any number of additional [row calls](/data-querying/pql#row-calls){:target="_blank"} seperated by commas

#### Returns
- list of record IDs or record keys

## Examples

### Data:
```
Index: customer (non keyed index)

 _id | age (Int) | has_purchased (Set) | last_purchase (Timestamp)
-----+-----------+---------------------+---------------------------
 0   |    23     | ["brand1","brand2"] | 2021-01-05T08:30:00Z
 1   |    31     | ["brand1","brand3"] | 2020-09-12T12:30:00Z
 2   |    28     | ["brand1","brand3"] | 2021-08-06T16:15:00Z
 3   |    19     | []                  | null
 4   |    25     | ["brand1","brand4"] | 2021-10-01T20:45:00Z
 5   |    40     | ["brand4"]          | 2022-01-13T11:00:00Z
```
-------
### Example 1
What is the set of customers / records who's age is over 26 and who have made a purchase since the start of 2021?

#### Query
```
[customer]Intersect(
  Row(age > 26), 
  Row(last_purchase > '2021-01-01T00:00:00Z')
)
```
#### Tabular Response
```
 _id
-----
 2
 5
```
#### HTTP Response
```json
{
  "results": [
    {
      "columns": [
        2,
        5
      ]
    }
  ]
}
```
#### Explanation
Row(age > 26) returns [1,2,5] and Row(last_purchase > '2021-01-01T00:00:00Z') returns [0,2,3,4,5]. The Intersect call does a set intersect on these two sets which is [2,5].
<hr>
### Example 2
What is the set of customers / records who's age is over 30, has purchased from brand1, and has purchased from brand3?

```
[customer]Intersect(
  Row(age > 30), 
  Row(has_purchased = brand1)
  Row(has_purchased = brand3)
)
```
#### Tabular Response
```
 _id
-----
 1
```

#### HTTP Response
```json
"results": [
    {
       "columns": [
         1
       ]
    }
]
```

#### Explanation
Row(age > 30) returns [1,5], Row(has_purchased = brand1) returns [0,1,2,4], and Row(has_purchased = brand3) returns [1,2]. The Intersect call does a set intersection of these three sets which is [1]. 

