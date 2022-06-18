# Chapter-4 : Day-1

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter4.0/day1#quests*

## Question 1

> Explain what lives inside of an account.

Accounts can contain contract code (with deployed contracts) and account-specific storage (with `/storage/`, `/private/` and `/public/` sections).

## Question 2

> What is the difference between the /storage/, /public/, and /private/ paths?

There are three sub-paths, each with varying access levels.

- `/storage/` contains all data and is only accessible by the account owner

The account owner can sign transactions that expose access to other accounts, either in
- `/public/` which is accessible to all accounts
- or `/private/` which is accessible to an explicit list of accounts

## Question 3

> What does .save() do? What does .load() do? What does .borrow() do?

- `save()` persists a resource to the `AuthAccount` (located within `/storage/`)
- `load()` retrieves a resource from the `AuthAccount` (from `/storage/`, returned as an optional)
- `borrow()` retrieves a resource reference from the `AuthAccount` (from `/storage/`)

## Question 4

> Explain why we couldn't save something to our account storage inside of a script.

Scripts are read only and `save()` is a write operation.

## Question 5

> Explain why I couldn't save something to your account.

The `save()` function is only valid for the `AuthAccount`, ie: the account for which the transaction has been signed.

"You" could not save to my account unless you had my private key to sign a transaction on my behalf.

## Question 6

> Define a contract that returns a resource that has at least 1 field in it. Then, write 2 transactions:
> - A transaction that first saves the resource to account storage, then loads it out of account storage, logs a field inside the resource, and destroys it.
> - A transaction that first saves the resource to account storage, then borrows a reference to it, and logs a field inside the resource.

Contract

```cadence
pub contract DroidContract {

    pub resource Droid {
        pub var name: String
        init(_name: String) {
            self.name = _name
        }
    }

    pub fun mint(name: String): @Droid {
        return <- create Droid(_name: name)
    }
}
```

Transaction-1

```cadence
import DroidContract from 0x01

transaction {

    prepare(acct: AuthAccount) {
        let storagePath = /storage/DroidPath
        acct.save(<- DroidContract.mint(name: "C-3PFLO"), to: storagePath)
        // retrieve with load
        let droid <- acct.load<@DroidContract.Droid>(from: storagePath) ?? panic ("not the droid you're looking for")
        log(droid.name)
        destroy droid
    }

    execute {}
}
```

Transaction-2

```cadence
import DroidContract from 0x01

transaction {

    prepare(acct: AuthAccount) {
        let storagePath = /storage/DroidPath
        acct.save(<- DroidContract.mint(name: "C-3PFLO"), to: storagePath)
        // retrieve as reference via borrow
        let droid = acct.borrow<&DroidContract.Droid>(from: storagePath) ?? panic ("not the droid you're looking for")
        log(droid.name)
    }

    execute {}
}
```
