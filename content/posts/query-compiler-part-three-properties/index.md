+++
title = 'A SQL query compiler from scratch in Rust (step by step): Part two, more computed properties'
date = 2023-12-29T18:59:57+01:00
draft = true
+++

In the [first post of this series](../query-compiler-part-one) we introduced a way of attaching
computed properties to our query graph. These properties are evaluated lazily and invalidated
when the subgraph that was used to compute them is modified. We introduced this mechanism with
a very simple `num_columns` property.

In this post we will introduce three new properties that use the same mechanism together with
some optimizations making use of them.

## `row_type` property

## `pulled_up_predicates` property

## `keys` property