+++
title = 'A SQL query compiler from scratch in Rust (step by step): Part two, the query rewrite driver'
date = 2023-12-27T22:48:45+01:00
draft = true
+++

In the previous post of this series we introduced a couple of very basic rewrite rules.
In this one we are going to take a look at the rule application driver, responsible for
applying a set of rules while traversing the query graph until the query settles in a fix
point.

The rules we have seen so far implement a `SingleReplacementRule` trait with an `apply`
method that given a node may return a new node that must be used to replace the given
one in the query graph. The replacement
node must be semantically equivalent to the previous one and, hence, must provide the
same amount of columns as the previous one and these columns must have the same data types
as the old ones.

However, this simple interface doesn't work in all cases as illustrated in the following
section.

## Column pruning

Column pruning is the optimization that ultimately makes the table scan operators only
scan the columns of each table that are actually needed. In our offset-based representation 
this optimization works by pushing down _pruning_ projections. A _pruning_ projection is
a projection operator that doesn't forward all the columns from its input.

Consider the following example:

![Join Pruning example](images/join-pruning.png)

The join in node 1 projects 20 columns, 10 columns from each of its inputs. However, only 4 of them
are actually needed: columns 0 and 18 are used by its first parent and columns 3 and 12 are
used by it second parent. Also, the join itself uses columns 4 and 15 from its join condition.
Columns 0, 3 and 4 belong to its first input, while columns 12, 15 and 18 are the columns 2, 5
and 8 respectively from its second input.

![Join Pruning example: final](images/join-pruning-2.png)