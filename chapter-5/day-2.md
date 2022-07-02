# Chapter-5 : Day-2

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter5.0/day2#quests*

## Question 1

> Explain why standards can be beneficial to the Flow ecosystem.

Standards allow developers to reason about what data and behaviors an item might have.  For example, apps like .find would like to rely on standards when finding all NFTs in an account and rendering their basic meta-data and images.

## Question 2

> What is YOUR favourite food?

My *favorite* food depends on the day, but right now I could go for a sausage, egg and cheese on an everything bagel.

## Question 3

> Please fix this code (Hint: There are two things wrong):

*NOTE: The original code is valid without any of these fixes, but probably not aligned with the development intent of declaring `ITest` and `ITest.IStuff`.  Once those interfaces are used, additional fixes like Fix-3 then become required.*

The (fixed) contract interface:

```cadence
pub contract interface ITest {
  pub var number: Int

  pub fun updateNumber(newNumber: Int) {
    pre {
      newNumber >= 0: "We don't like negative numbers for some reason. We're mean."
    }
    post {
      self.number == newNumber: "Didn't update the number to be the new number."
    }
  }

  pub resource interface IStuff {
    pub var favouriteActivity: String
  }

  // Fix-0: assuming the intent is to enforce Stuff implement the IStuff interface
  // declared in ITest, the resource interface should be declared here
  pub resource Stuff: IStuff {
    pub var favouriteActivity: String
  }
}
```

The (fixed) implementing contract:

```cadence
// Fix-1: The ITest contract interface needs to be imported
import ITest from 0x01

// Fix-2: The Test contract needs to declare that it implements the ITest interface
// (assuming that is the intent for this quest)
pub contract Test: ITest {
  pub var number: Int

  pub fun updateNumber(newNumber: Int) {
    // Fix-3: self.number = 5 will fail, since ITest.updateNumber post requires
    // self.number to equal newNumber after execution
    self.number = newNumber
  }

  // Fix-4: this is redundant since ITest.IStuff has the same interface
  // pub resource interface IStuff {
  //  pub var favouriteActivity: String
  // }

  // Fix-5: assuming the intent in ITest was to require Stuff: IStuff with
  // the ITest.IStuff interface (Fix-0), this should use the ITest.IStuff interface
  // as well
  pub resource Stuff: ITest.IStuff {
    pub var favouriteActivity: String

    init() {
      self.favouriteActivity = "Playing League of Legends."
    }
  }

  init() {
    self.number = 0
  }
}
```
