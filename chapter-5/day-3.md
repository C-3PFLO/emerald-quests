# Chapter-5 : Day-3

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter5.0/day3#quests*

## Question 1

> What does "force casting" with as! do? Why is it useful in our Collection?

Force casting will "downcast" a type from a more generic type to a more specific one.  This is useful if the more specific type has additional fields or functions that need to be leveraged (ex: the `CryptoPoops.NSF` fields `name`, `favouriteFood` and `luckyNumber`).

## Question 2

> What does `auth` do? When do we use it?

The `auth` operator returns an authorized reference, allowing a reference to be downcast to a more specific type.  Like force casting a resource, this can be used if you want to allow borrowing a reference of a more explicit type, exposing additional fields and methods.

*NOTE: Flow documents the use of `auth` as an anti-pattern since it could expose functionality that was not intended to be exposed: https://docs.onflow.org/cadence/anti-patterns/#problem-1*.

## Question 3

Contract with a new `CryptoPoopsCollectionPublic` resource interface including `borrowAuthNFT`, implemented within `Collection`.

```cadence
import NonFungibleToken from 0x02

pub contract CryptoPoops: NonFungibleToken {
    pub var totalSupply: UInt64

    pub event ContractInitialized()
    pub event Withdraw(id: UInt64, from: Address?)
    pub event Deposit(id: UInt64, to: Address?)

    pub resource NFT: NonFungibleToken.INFT {
        pub let id: UInt64

        pub let name: String
        pub let favouriteFood: String
        pub let luckyNumber: Int

        init(_name: String, _favouriteFood: String, _luckyNumber: Int) {
            self.id = self.uuid
            self.name = _name
            self.favouriteFood = _favouriteFood
            self.luckyNumber = _luckyNumber
        }
    }

    // Add a resource interface that exposes borrowAuthNFT
    pub resource interface CryptoPoopsCollectionPublic {
        pub fun getIDs(): [UInt64]
        pub fun borrowAuthNFT(id: UInt64): &NFT
    }

    // Append the resource interface
    pub resource Collection: NonFungibleToken.Provider,
                             NonFungibleToken.Receiver,
                             NonFungibleToken.CollectionPublic,
                             CryptoPoopsCollectionPublic {

        pub var ownedNFTs: @{UInt64: NonFungibleToken.NFT}

        pub fun withdraw(withdrawID: UInt64): @NonFungibleToken.NFT {
            let nft <- self.ownedNFTs.remove(key: withdrawID)
            ?? panic("This NFT does not exist in this Collection.")
            emit Withdraw(id: nft.id, from: self.owner?.address)
            return <- nft
        }

        pub fun deposit(token: @NonFungibleToken.NFT) {
            let nft <- token as! @NFT
            emit Deposit(id: nft.id, to: self.owner?.address)
            self.ownedNFTs[nft.id] <-! nft
        }

        pub fun getIDs(): [UInt64] {
            return self.ownedNFTs.keys
        }

        pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT {
            return (&self.ownedNFTs[id] as &NonFungibleToken.NFT?)!
        }

        pub fun borrowAuthNFT(id: UInt64): &NFT {
            let ref = (&self.ownedNFTs[id] as auth &NonFungibleToken.NFT?)!
            return ref as! &NFT
        }

        init() {
            self.ownedNFTs <- {}
        }

        destroy() {
            destroy self.ownedNFTs
        }
    }

    pub fun createEmptyCollection(): @NonFungibleToken.Collection {
        return <- create Collection()
    }

    pub resource Minter {
        pub fun createNFT(name: String, favouriteFood: String, luckyNumber: Int): @NFT {
            return <- create NFT(_name: name, _favouriteFood: favouriteFood, _luckyNumber: luckyNumber)
        }
        pub fun createMinter(): @Minter {
            return <- create Minter()
        }
    }

    init() {
        self.totalSupply = 0
        emit ContractInitialized()
        self.account.save(<- create Minter(), to: /storage/Minter)
    }
}
```

Transaction to initialize the collection (run by `0x03`)

```cadence
import CryptoPoops from 0x01
import NonFungibleToken from 0x02

transaction() {
    prepare(signer: AuthAccount) {
        signer.save(<- CryptoPoops.createEmptyCollection(), to: /storage/MyCollection)
        // need NonFungibleToken.CollectionPublic for deposit
        // and CryptoPoops.CryptoPoopsCollectionPublic for borrowAuthNFT
        signer.link<&CryptoPoops.Collection{NonFungibleToken.CollectionPublic,
                                            CryptoPoops.CryptoPoopsCollectionPublic}>
            (/public/MyCollection, target: /storage/MyCollection)
    }
}
```

Transaction to mint the NFT (run by `0x01`, recipient `0x03`)

```cadence
import CryptoPoops from 0x01
import NonFungibleToken from 0x02

transaction(recipient: Address, name: String, favouriteFood: String, luckyNumber: Int) {

    prepare(signer: AuthAccount) {
        let minter = signer.borrow<&CryptoPoops.Minter>(from: /storage/Minter)
            ?? panic("This signer is not the one who deployed the contract.")
        let nft <- minter.createNFT(
            name: name,
            favouriteFood: favouriteFood,
            luckyNumber: luckyNumber
        )

        let recipientsCollection = getAccount(recipient)
            .getCapability(/public/MyCollection)
            .borrow<&CryptoPoops.Collection{NonFungibleToken.CollectionPublic}>()
            ?? panic("The recipient does not have a Collection.")
        recipientsCollection.deposit(token: <- nft)
    }
}
```

Script to read a specific NFT by id (run for address `0x03`)

```cadence
import CryptoPoops from 0x01

pub fun main(address: Address, id: UInt64) {
    let publicCollection = getAccount(address).getCapability(/public/MyCollection)
        // borrow as CryptoPoopsCollectionPublic to use borrowAuthNFT
        .borrow<&CryptoPoops.Collection{CryptoPoops.CryptoPoopsCollectionPublic}>()
        ?? panic("The address does not have a Collection.")
    log(publicCollection.borrowAuthNFT(id: id))
}
```

Script to all of the NFTs in the collection (for address `0x03`)

```cadence
import CryptoPoops from 0x01

pub fun main(address: Address) {
    let publicCollection = getAccount(address).getCapability(/public/MyCollection)
        // borrow as CryptoPoopsCollectionPublic to use borrowAuthNFT
        .borrow<&CryptoPoops.Collection{CryptoPoops.CryptoPoopsCollectionPublic}>()
        ?? panic("The address does not have a Collection.")
    // get list of ids and iterate
    let nftIDs = publicCollection.getIDs()
    for id in nftIDs {
        // borrow auth so CryptoPoops.NFT properties are accessible
        log(publicCollection.borrowAuthNFT(id: id))
    }
}
```
