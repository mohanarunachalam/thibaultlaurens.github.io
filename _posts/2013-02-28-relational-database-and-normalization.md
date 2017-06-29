---
layout: post
title: "Relational database and Normalization"
description: "A simple reminder of the normal forms"
category: database
---

In relational database design, the goal of normalization is to check whether or not a relationship fills the prerequisites for a given normal form. This will automatically minimize redundancy and anomalies in insertion, deletion and update.

There are 4 basics and commonly used normal forms : **first (1NF), second (2NF), third (3NF) and Boyce-Codd (BCNF)** normal forms.

***

#### First Normal Form

**1NF** makes reference to the concept of **atomicity** : the table represents a relation in which intersections of each rows and columns contain one and only one value.

#### Second Normal Form

A **2NF** is a 1NF where every non primary key attributes are fully **functionally dependents** on the primary key. This involves identifying partial dependencies on the primary key and removing them by creating a new relation.

#### Third Normal Form

A **3NF** is a 2NF based on the concept of **transitive dependency** (attributes A->B and B->C therefore A->C). Like in 2NF, this involves identifying transitive dependencies on the primary key and removing them by creating a new relation.

#### Boyce-Codd Normal Form

**BCNF**  makes reference to the concept of **functional dependency** and **candidate key** (the minimal superkey for the relation). In BCNF, every determinants have to be a candidate key. If the relation has functional dependencies where determinants are not candidate keys we have to remove them and create a new relation with a copy of their determinants. A 3NF relation with only one candidate key is automatically in BCNF.

***

A good relational model should be at least in 2NF, higher normal forms have their pros and cons. After 3NF, access time could be longer (we can fix this problem with index on primary key) and the database structure could be difficult to maintain. But, you ensure non redondant and inconsistent data. The best things to do is to choose the level of normalization over the type of access: if the data is more often edited than read you should normalize as much as possible. Otherwise, if the data is more often read, then you should probably denormalize a little bit to improve data access, but keep in mind that redundancy can increase the amount of data and, in the end push down performances.



Note: 1NF, 2NF, 3NF and BCNF focus on eliminating data redundancy based on undesirable functional dependencies. The are other normal forms after BCNF : **Fourth (4NF), Fifth (5NF), Domain/Key (DKNF) and Sixth (6NF)** normal forms wich are focus on other things like multivalued dependency (4NF) or joind dependency implied by candidate keys (5NF).
