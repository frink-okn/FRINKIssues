# Graph Exploration Queries

The automatic portions of the graph analyses do not involve running queries (due to the time it takes to run them for larger graphs), but queries for them might be written as follows:

* Number of types:
```sparql
select (count(distinct ?type) as ?types) { [] a ?type }
```
* Most common types:
```sparql
select ?type (count(?object) as ?objects) {
  ?object a ?type
} group by ?type order by desc(?objects)
```
* Type-predicate combinations of triples where both subject and object have a type:
```sparql
select ?subject_type ?predicate ?object_type (count(?object) as ?objects) {
  ?subject ?predicate ?object . ?subject a ?subject_type . ?object a ?object_type
} group by ?subject_type ?predicate ?object_type order by desc(?objects)
```
* Type-predicate combinations of triples where only subject has a type:
```sparql
select ?subject_type ?predicate (count(?object) as ?objects) {
  ?subject ?predicate ?object . ?subject a ?subject_type . minus { ?object a [] }
} group by ?subject_type ?predicate order by desc(?objects)
```
* Type-predicate combinations of triples where only object has a type:
```sparql
select ?predicate ?object_type (count(?subject) as ?subjects) {
  ?subject ?predicate ?object . ?object a ?object_type . minus { ?subject a [] }
} group by ?predicate ?object_type order by desc(?subjects)
```
* Type-predicate combinations of triples where neither subject nor object have a type:
```sparql
select ?predicate (count(?subject) as ?subjects) {
  ?subject ?predicate ?object . minus { ?subject a [] } minus { ?object a [] }
} group by ?predicate order by desc(?subjects)
```
* List of entities (subjects of triples) without a type:
```sparql
select distinct ?subject {
  ?subject ?predicate [] . minus { ?subject a [] }
}
```
