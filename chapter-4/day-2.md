# Chapter-4 : Day-2

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter4.0/day2#quests*

## Question 1

> What does .link() do?

`.link()` creates a pointer from either `/public/` or `/private/` to `/storage/`.  This is useful to allow other accounts to interact with data in `/storage/`.

## Question 2

> In your own words (no code), explain how we can use resource interfaces to only expose certain things to the /public/ path.

Resource interfaces restrict access to a resource.

When a resource is saved to `/storage/`, no other accounts can read it.  In many cases, systems are designed to allow read-only access to all users but restrict modify, move and destroy access to the account owner.

A resource can be linked to `/public/` to grant read access to all accounts.  When linking a reference to the resource, no other accounts can move or destroy the resource (since they only have a reference).  However, the reference includes access to all fields and functions.

If a resource has fields or functions that should not be publicly available (ex: functions that modify the resource), a resource interface can be used to restrict access.  Since a resource interface limits the fields and functions a caller can interact with, linking a resource as a resource interface limits which fields or functions will be exposed.

## Question 3

> Deploy a contract that contains a resource that implements a resource interface. Then, do the following:
> - In a transaction, save the resource to storage and link it to the public with the restrictive interface.
> - Run a script that tries to access a non-exposed field in the resource interface, and see the error pop up.
> - Run the script and access something you CAN read from. Return it from the script.

Contract

```cadence

```
