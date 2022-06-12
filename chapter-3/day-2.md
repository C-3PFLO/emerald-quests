# Chapter-3 : Day-2

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter3.0/day2#quests*

## Question 1

> Write your own smart contract that contains two state variables: an array of resources, and a dictionary of resources. Add functions to remove and add to each of them. They must be different from the examples above.

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
    pub var collectionArray: @[CoolNFT]
    pub var collectionDictionary: @{String: CoolNFT}
    init() {
        self.collectionArray <- []
        self.collectionDictionary <- {}
    }

    // array functions
    pub fun addToArray(nft: @CoolNFT) {
        self.collectionArray.append(<- nft)
    }
    pub fun removeFromArray(index: Int): @CoolNFT {
        return <- self.collectionArray.remove(at: index)
    }

    // dictionary functions
    pub fun addToDictionary(nft: @CoolNFT) {
        self.collectionDictionary[nft.someTrait] <-! nft
    }
    pub fun removeFromDictionary(trait: String): @CoolNFT? {
        return <- self.collectionDictionary.remove(key: trait)
    }
}
```
