# Chapter-2 : Day-3

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter2.0/day3#quests*

## Question 1

> In a script, initialize an array (that has length == 3) of your favourite people, represented as Strings, and log it.

Script

```cadence
pub fun main() {
    let myFavoritePeople: [String] = [
        "R2D2",
        "Luke",
        "Obi-Wan"
    ]
    log(myFavoritePeople)
}
```

# Question 2

> In a script, initialize a dictionary that maps the Strings Facebook, Instagram, Twitter, YouTube, Reddit, and LinkedIn to a UInt64 that represents the order in which you use them from most to least. For example, YouTube --> 1, Reddit --> 2, etc. If you've never used one before, map it to 0!

Script

```cadence
pub fun main() {
    let sites: {String: UInt64} = {
        "Facebook": 5,
        "Instagram": 4,
        "Twitter": 1,
        "YouTube": 2,
        "Reddit": 3,
        "LinkedIn": 6
    }
    log(sites)
}
```

# Question 3

> Explain what the force unwrap operator ! does, with an example different from the one I showed you (you can just change the type).

The unwrap `!` operator removes an optional type if the variable is not `nil`, else it panics.  For example:

```cadence
pub fun main() {
    // ? operator specifies myInt as optional
    var myInt: Int? = 1
    // ! operator unwraps the optional
    var myUnwrappedInt: Int = myInt!
    // logs 1 since myInt is defined as 1
    log(myUnwrappedInt)

    // setting to nil instead of 1
    myInt = nil
    // this line panics since unwrap of nil causes wide-spread panic
    myUnwrappedInt = myInt!
}
```

# Question 4

Initial code

```cadence
pub fun main(): String {
    let thing: {Address: String} = {0x01: "One", 0x02: "Two", 0x03: "Three"}
    return thing[0x03]
}
```

> What the error message means

The returned type does not match the declared return type

> Why we're getting this error

Error because `thing[0x03]` returns an optional `String?` but `main()` declares that it returns a `String`.

> How to fix it

Either `return thing[0x03]!` if main should return `String`, or change main to declare that it returns an optional with `pub fun main(): String?`.
