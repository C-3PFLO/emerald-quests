# Chapter-3 : Day-5

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter3.0/day5#quests*

## Question 1

> For today's quest, you will be looking at a contract and a script. You will be looking at 4 variables (a, b, c, d) and 3 functions (publicFunc, contractFunc, privateFunc) defined in SomeContract. In each AREA (1, 2, 3, and 4), I want you to do the following: for each variable (a, b, c, and d), tell me in which areas they can be read (read scope) and which areas they can be modified (write scope). For each function (publicFunc, contractFunc, and privateFunc), simply tell me where they can be called.

Variables

| Declaration              | Read Scope | Write Scope |
| ------------------------ | ---------- | ----------- |
| `pub(set) var a`         | 1,2,3,4    | 1,2,3,4     |
| `pub var b`              | 1,2,3,4    | 1           |
| `access(contract) var c` | 1,2,3      | 1           |
| `access(self) var d`     | 1          | 1           |

Functions

| Declaration              | Access Scope |
| ------------------------ | ------------ |
| `pub fun publicFunc`     | 1,2,3,4      |
| `access(contract) fun`   | 1,2,3        |
| `access(self) fun`       | 1            |
