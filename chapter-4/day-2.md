# Chapter-4 : Day-2

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter4.0/day2#quests*

## Question 1

> What does .link() do?

`.link()` creates a pointer from either `/public/` or `/private/` to `/storage/`.  This is useful to allow other accounts to interact with data in `/storage/`.

## Question 2

> In your own words (no code), explain how we can use resource interfaces to only expose certain things to the /public/ path.

Resource interfaces restrict access to a resource.

When a resource is saved to `/storage/`, no other accounts can read it.  In many cases, systems are designed to allow read-only access to all users but restrict modify, move and destroy access to the account owner.

A resource can be linked to `/public/` to grant read access to all accounts.  The link provides a reference to the resource, so no other accounts can move or destroy the resource.  However, the reference includes access to all fields and functions.

If a resource has fields or functions that should not be publicly available (ex: functions that modify the resource), a resource interface can be used to restrict access.  Since a resource interface limits the fields and functions a caller can interact with, linking a resource as a resource interface limits which fields or functions will be exposed.

## Question 3

> Deploy a contract that contains a resource that implements a resource interface. Then, do the following:
> - In a transaction, save the resource to storage and link it to the public with the restrictive interface.
> - Run a script that tries to access a non-exposed field in the resource interface, and see the error pop up.
> - Run the script and access something you CAN read from. Return it from the script.

Contract

```cadence
pub contract DroidContract {

    // public constants for paths
    pub let DroidStoragePath: StoragePath
    pub let DroidPublicPath: PublicPath
    init() {
        self.DroidStoragePath = /storage/DroidPath
        self.DroidPublicPath = /public/DroidPath
    }

    // interace to restrict access
    pub resource interface IDroid {
        pub var name: String
    }

    // resource with additional fields and functions
    pub resource Droid: IDroid {
        pub var name: String
        pub var objective: String
        init(_name: String) {
            self.name = _name
            self.objective = ""
        }
        pub fun getObjective(): String {
            return self.objective
        }
        pub fun setObjective(objective: String) {
            self.objective = objective
        }
    }

    // public functions

    pub fun mint(name: String): @Droid {
        return <- create Droid(_name: name)
    }
}
```

Transaction

```cadence
import DroidContract from 0x01

transaction {

    prepare(acct: AuthAccount) {
        // mint resource and modify as needed
        let droid <- DroidContract.mint(name: "C-3PFLO")
        droid.setObjective(objective: "Human-Flovatar Relations")

        // save and create link, restricted by interface
        acct.save(<- droid, to: DroidContract.DroidStoragePath)
        acct.link<&DroidContract.Droid{DroidContract.IDroid}>(
            DroidContract.DroidPublicPath,
            target: DroidContract.DroidStoragePath
        )
    }

    execute {}
}
```

Script

```cadence
import DroidContract from 0x01

pub fun main(address: Address): String {
    // borrow reference from public
    let droidPublicCapability: Capability<&DroidContract.Droid{DroidContract.IDroid}> =
        getAccount(address).getCapability<&DroidContract.Droid{DroidContract.IDroid}>(DroidContract.DroidPublicPath)
    let droid: &DroidContract.Droid{DroidContract.IDroid} = droidPublicCapability.borrow() ?? panic("not the droid you're looking for")

    // ERROR: member of restricted type is not accessible: getObjective
    // log(droid.getObjective())

    return droid.name
}
```
