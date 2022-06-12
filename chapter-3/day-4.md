# Chapter-3 : Day-4

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter3.0/day4#quests*

## Question 1

> Explain, in your own words, the 2 things resource interfaces can be used for (we went over both in today's content)

Interfaces declare what behavior an implementing resource or struct will have, enforcing specific fields and functions.

Interfaces can also be used by callers to restrict access to a specific interface.  If the caller chooses to restrict the type to a specific interface, only the interface fields and functions are accessible (even if the underlying resource or struct has additional fields or functions).

## Question 2

> Define your own contract. Make your own resource interface and a resource that implements the interface. Create 2 functions. In the 1st function, show an example of not restricting the type of the resource and accessing its content. In the 2nd function, show an example of restricting the type of the resource and NOT being able to access its content.

```cadence
pub contract MyContract {

    // interface
    pub resource interface IToken {
        pub var animal: String
        pub var name: String
        pub fun getTokenAnimal(): String
    }

    // resource implementing interface
    pub resource AnimalToken: IToken {

        pub var animal: String
        pub var name: String
        init(_animal: String, _name: String) {
            self.animal = _animal
            self.name = _name
        }

        pub fun getTokenAnimal(): String { // IToken
            return self.animal
        }

        pub fun getTokenName(): String { // Not in IToken
            return self.name
        }
    }

    // function that does not restrict to interface
    pub fun noInterface() {
        let squirrelToken: @AnimalToken <- create AnimalToken(
            _animal: "squirrel",
            _name: "SquirrelToken"
        )
        log(squirrelToken.getTokenAnimal())
        log(squirrelToken.getTokenName())
        destroy squirrelToken
    }

    // function that does restrict to interface, coded to fail
    pub fun usesInterface() {
        let squirrelToken: @AnimalToken{IToken} <- create AnimalToken(
            _animal: "squirrel",
            _name: "SquirrelToken"
        )
        log(squirrelToken.getTokenAnimal())        
        // member of restricted type is not accessible: getTokenName
        log(squirrelToken.getTokenName()) // FAILS
        destroy squirrelToken
    }
}
```

## Question 3

> How would we fix this code?

```cadence
pub contract Stuff {

    pub struct interface ITest {
      pub var greeting: String
      pub var favouriteFruit: String
    }

    // ERROR:
    // `structure Stuff.Test does not conform
    // to structure interface Stuff.ITest`
    pub struct Test: ITest {
      pub var greeting: String

      pub fun changeGreeting(newGreeting: String): String {
        self.greeting = newGreeting
        return self.greeting // returns the new greeting
      }

      init() {
        self.greeting = "Hello!"
      }
    }

    pub fun fixThis() {
      let test: Test{ITest} = Test()
      let newGreeting = test.changeGreeting(newGreeting: "Bonjour!") // ERROR HERE: `member of restricted type is not accessible: changeGreeting`
      log(newGreeting)
    }
}
```

After fix

```cadence
pub contract Stuff {

    pub struct interface ITest {
      pub var greeting: String
      pub var favouriteFruit: String
    }

    pub struct Test: ITest {
      pub var greeting: String
      // FIX: declare missing favoriteFruit field
      pub var favouriteFruit: String

      pub fun changeGreeting(newGreeting: String): String {
        self.greeting = newGreeting
        return self.greeting
      }

      init() {
        self.greeting = "Hello!"
        // FIX: init missing favoriteFruit field
        self.favouriteFruit = "strawberries"
      }
    }

    pub fun fixThis() {
      // FIX: either expose changeGreeting in ITest (not shown)
      // or remove restriction to ITest (shown below)
      let test: Test = Test()
      let newGreeting = test.changeGreeting(newGreeting: "Bonjour!")
      log(newGreeting)
    }
}
```
