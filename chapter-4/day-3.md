# Chapter-4 : Day-3

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter4.0/day3#quests*

## Question 1

> Why did we add a Collection to this contract? List the two main reasons.

Collections allow you to group multiple resources, typically with functions to deposit and withdraw resources from the collection.  The account holder can save the collection to their storage, which then

1. Organizes all of the resources into a single storage path.
1. Allow other accounts to interact with storage, including write if the collection deposit function is made accessible (typically via a resource interface that does not make withdraw accessible).

## Question 2

> What do you have to do if you have resources "nested" inside of another resource? ("Nested resources")

Cadence enforces that resources nested within resources have a `destroy` function implemented.

## Question 3

> Brainstorm some extra things we may want to add to this contract. Think about what might be problematic with this contract and how we could fix it.
>
> Idea #1: Do we really want everyone to be able to mint an NFT? 🤔.
>
> Idea #2: If we want to read information about our NFTs inside our Collection, right now we have to take it out of the Collection to do so. Is this good?

Some potential issues and room for improvement:

- Most NFT projects have limits on who can mint the NFTs, ex: only an admin can.  Permissions on `CryptoPoops.createNFT` could be set to `access(account)` so only contracts in an admin account can call create [^flovatar].
- Most NFT projects also have limits on the total supply that can be minted for an NFT, ex 9999.  This could be done by panicking in the `createNFT` function if the next mint would exceed a total mint threshold.
- Instead of withdrawing a resource from the collection to read it, we should have a collection function to borrow a reference to the resource instead (possible restricted by a resource interface if the resource has properties or methods that should not be accessible).
- NFTs should implement the `MetadataViews` standard for exposing public views.
- The storage path to the collection could be a public variable, possibly following a standard name like `CryptoPoops.CollectionStoragePath` so callers don't need to know the actual path.

[^flovatar]: Flovatar does this with an Admin resource minted into the admin account.  The Admin resource is saved to `storage` and not linked, so no other users can access it.  The resource has `pub` functions that wrap the `access(account)` functions, providing the admin account holder access to those create functions. https://flowscan.org/contract/A.921ea449dffec68a.Flovatar/overview
