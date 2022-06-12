# Chapter-3 : Day-1

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter3.0/day1#quests*

## Question 1

> In words, list 3 reasons why structs are different from resources.

Structs can be treated like traditional objects.  Resources cannot be copied (only moved with an explicit `<-` operator), cannot be lost (only explicitly destroyed with the `destroy` operator) and cannot be created without an explicit `create` operator.

## Question 2

> Describe a situation where a resource might be better to use than a struct.

Resources are better suited for digital assets.  Cadence guardrails for resources prevent against bugs that could accidentally lose, transfer or unintentionally create those assets.

## Question 3

> What is the keyword to make a new resource?

Resources are created with `create`.

## Question 4

> Can a resource be created in a script or transaction (assuming there isn't a public function to create one)?

No.  Resources can only be created within a contract, although contracts can expose public functions to create resources.

# Question 5

> What is the type of the resource below?  `pub resource Jacob {}`

It is a `Jacob` resource.

# Question 6

> Let's play the "I Spy" game from when we were kids. I Spy 4 things wrong with this code. Please fix them.

```cadence
pub contract Test {

    pub resource Jacob {
        pub let modest: String
        init() {
            self.modest = "eeeeeek :)"
        }
    }

    pub fun createJacob(): @Jacob { // add @
        let myJacob <- create Jacob() // replace = with <-, add create
        return <- myJacob // add <-
    }
}
```
