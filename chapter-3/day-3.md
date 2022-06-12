# Chapter-3 : Day-3

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter3.0/day3#quests*

## Question 1

> Define your own contract that stores a dictionary of resources. Add a function to get a reference to one of the resources in the dictionary.

```cadence
pub contract MyProject {

    // declare the resource
    pub resource CoolNFT {
        pub let someTrait: String
        init(_traitValue: String) {
            self.someTrait = _traitValue
        }
    }

    // contract state
    pub var collectionDictionary: @{String: CoolNFT}
    init() {
        self.collectionDictionary <- {
            "key1": <- create CoolNFT(_traitValue: "rainbow"),
            "key2": <- create CoolNFT(_traitValue: "alien"),
            "key3": <- create CoolNFT(_traitValue: "robot")
        }
    }

    // dictionary functions
    pub fun getReference(key: String): &CoolNFT {
        return &self.collectionDictionary[key] as &CoolNFT
    }
}
```

## Question 2

> Create a script that reads information from that resource using the reference from the function you defined in part 1.

```cadence
import MyProject from 0x01

pub fun main() {
    let key: String = "key2"
    log(MyProject.getReference(key: key))
}
```

## Question 3

> Explain, in your own words, why references can be useful in Cadence.

References allow you to read data without moving it.  Moving it cannot be done in a script (which cannot modify the blockchain) and moving it temporarily requires taking care to move it back to the right place (even if there is an error).
