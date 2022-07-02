# Chapter-5 : Day-1

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter5.0/day1#quests*

## Question 1

> Describe what an event is, and why it might be useful to a client.

Events are objects emitted during execution of a transaction or contract.  They are public and can include any fields, making them useful to notify observers.  For example, whenever an NFT is minted the contract can emit an event that includes key meta-data of the minted NFT and observers (flowscan, Graffle, Twitter and Discord bots, etc.) can efficiently observe and react to the event (ex: tweet that a new NFT was minted).

## Question 2

> Deploy a contract with an event in it, and emit the event somewhere else in the contract indicating that it happened.

```cadence
pub contract EventTest {
    pub event MyEvent(message: String)
    init() {
        emit MyEvent(message: "contract deployed")
    }
}
```

## Question 3

> Using the contract in step 2), add some pre conditions and post conditions to your contract to get used to writing them out.

```cadence
pub contract EventTest {

    pub event MyEvent(message: String)

    pub fun addPositiveInts(a: Int, b: Int): Int {
        pre {
            a > 0 : "must be positive"
            b > 0 : "must be positive"
        }
        post {
            result == a + b : "result incorrect"
        }
        return a + b;
    }

    init() {
        emit MyEvent(message: "contract deployed")
    }
}
```

## Question 4

> For each of the functions below (numberOne, numberTwo, numberThree), follow the instructions.

```cadence
// Tell me whether or not this function will log the name.
// name: 'Jacob'
pub fun numberOne(name: String) {
  pre {
    name.length == 5: "This name is not cool enough."
  }
  log(name)
}
```

Since `Jacob` has length 5, the `pre` condition is met and the function will log the name.

```cadence
// Tell me whether or not this function will return a value.
// name: 'Jacob'
pub fun numberTwo(name: String): String {
  pre {
    name.length >= 0: "You must input a valid name."
  }
  post {
    result == "Jacob Tucker"
  }
  return name.concat(" Tucker")
}
```

The `pre` condition is met since `Jacob` is length 5, greater or equal than 0.  The `post` condition is also met since the result is `Jacob Tucker`, after concatenating ` Tucker` to the input `Jacob`.

```cadence
pub resource TestResource {
  pub var number: Int

  // Tell me whether or not this function will log the updated number.
  // Also, tell me the value of `self.number` after it's run.
  pub fun numberThree(): Int {
    post {
      before(self.number) == result + 1
    }
    self.number = self.number + 1
    return self.number
  }

  init() {
    self.number = 0
  }
}
```

The `post` condition is not met, since the function increments `self.number` to be 1 greater than it was but the `post` condition checks that the `result` be 1 less than `before` (ie: `result == before(self.number) - 1`).

Since the `post` condition is not met the transaction is aborted and `self.number` remains at the initial state of 0.
