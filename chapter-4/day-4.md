# Chapter-4 : Day-4

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter4.0/day4#quests*

## Question 1

> Take our NFT contract so far and add comments to every single resource or function explaining what it's doing in your own words.

```cadence
// the CryptoPoops contract
pub contract CryptoPoops {
  // stores a public variable used to count the total supply of CryptoPoops.NFT
  pub var totalSupply: UInt64

  // the NFT resource
  pub resource NFT {
    // declares the list of NFT fields and their types
    pub let id: UInt64
    pub let name: String
    pub let favouriteFood: String
    pub let luckyNumber: Int
    // constructor to initialize NFT fields
    init(_name: String, _favouriteFood: String, _luckyNumber: Int) {
      self.id = self.uuid // sets NFT.id to the unique resource id (generated by Cadence)
      self.name = _name
      self.favouriteFood = _favouriteFood
      self.luckyNumber = _luckyNumber
    }
  }

  // a "CollectionPublic" resource interface used to limit which collection fields and functions
  // will be made accessible in public
  pub resource interface CollectionPublic {
    pub fun deposit(token: @NFT)
    pub fun getIDs(): [UInt64]
    pub fun borrowNFT(id: UInt64): &NFT
  }

  // "Collection" resource that implements the CollectionPublic interface
  pub resource Collection: CollectionPublic {
    // a dictionary used to hold NFT resources, indexed by their ID
    pub var ownedNFTs: @{UInt64: NFT}
    // method to deposit NFTs into the ownedNFTs dictionary at the location of the NFT.id
    pub fun deposit(token: @NFT) {
      self.ownedNFTs[token.id] <-! token
    }
    // method to withdraw an NFT from ownedNFTs dictionary from a specific index
    // returns the NFT resource to the caller
    pub fun withdraw(withdrawID: UInt64): @NFT {
      // removes the NFT from the dictionary and panics if it is not found
      let nft <- self.ownedNFTs.remove(key: withdrawID)
              ?? panic("This NFT does not exist in this Collection.")
      // returns the NFT to the caller
      return <- nft
    }
    // get the list of keys in the ownedNFTs dictionary (ie: the list of NFT ids)
    pub fun getIDs(): [UInt64] {
      return self.ownedNFTs.keys
    }
    // instead of withdrawing, borrow a reference to NFT
    pub fun borrowNFT(id: UInt64): &NFT {
      // gets the NFT from ownedNFTs dictionary at the id location
      // unwraps the optional which will panic if there is no NFT at that location
      return (&self.ownedNFTs[id] as &NFT?)!
    }
    // initializes the ownedNFTs dictionary (empty)
    init() {
      self.ownedNFTs <- {}
    }
    // destroys the ownedNFTs dictionary, required for any nested resouces (dictionary of NFTs nested within Collection)
    destroy() {
      destroy self.ownedNFTs
    }
  }
  // public function for anyone to create a new collection, returned so it can be saved
  pub fun createEmptyCollection(): @Collection {
    return <- create Collection()
  }

  // A helper resource used to control admin functions
  pub resource Minter {
    // a function to create NFT resources
    // create can only be called within the contract that declares CryptoPoops.NFT, and
    // this wrapper createNFT can only be called from Minter.createNFT, ie: only accounts
    // that have access to minter can mint a CryptoPoops.NFT
    pub fun createNFT(name: String, favouriteFood: String, luckyNumber: Int): @NFT {
      return <- create NFT(_name: name, _favouriteFood: favouriteFood, _luckyNumber: luckyNumber)
    }
    // creates the Minter resource, used below to initialize the creator only in the account
    // deploying this contract
    pub fun createMinter(): @Minter {
      return <- create Minter()
    }

  }
  // initialize the contract
  init() {
    // total supply initially zero
    self.totalSupply = 0
    // create the Minter and save it to storage, where it is only accessible to the
    // account deploying this contract, ie: no one else can call createNFT
    self.account.save(<- create Minter(), to: /storage/Minter)
    // this account could run a transaction to CryptoPoops.Minter.createMinter() and
    // send that minter to another account (ex: if multiple accounts need "admin" access)
    // but this would require something like a MinterCollection on the recipient side
    // so this account holder has access to deposit the Minter in the recipients account
  }
}
```