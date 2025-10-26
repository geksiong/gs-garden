---
title: Natural Ids vs Surrogate Ids
date: 2020-08-09
---

## Natural Ids

Natural ids are unique ids that exist _naturally_ within the domain model.

- usually defined as one or more persistent attributes
- natural ids by definition are usually immutable, and are easier to determine equality

Cons of natural ids:

- although it represents the business better, it also suffers from the fact that if the business change, the natural id will have to change
- natural ids are typically larger and more complex (consist of multiple columns). this is harder to maintain and update, and less performant

## Surrogate Ids

A lot of ORMs implement generated (or surrogate) ids by default, and you need to model that in your schema. Different ORMs may handle generated ids differently, which can be an issue with _schema portability_.

- you should never have to change a surrogate key - if you have to do it something is wrong
- [https://www.techrepublic.com/blog/10-things/10-tips-for-choosing-between-a-surrogate-and-natural-primary-key/#:~:text=1%3A%20A%20primary%20key%20value,unique%20in%20and%20of%20itself.](https://www.techrepublic.com/blog/10-things/10-tips-for-choosing-between-a-surrogate-and-natural-primary-key/#:~:text=1%3A%20A%20primary%20key%20value,unique%20in%20and%20of%20itself.)

## Conclusion

This may not be a real issue, as auto-generated ids are a very common practice anyway, even though it may seem a bit "unnatural"
