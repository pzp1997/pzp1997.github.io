# `flip (flip id) == ($)`

One of the least intuitive identities that I have encountered in Haskell is `flip (flip id) == ($)`. It turns out that with some careful thought, we can derive this identity in only a couple steps. The derivation also reveals some nice insights about the relationship between `id` and `($)`.

We will begin with the type signatures of `id` and `flip`.

```
id :: a -> a
flip :: (a -> b -> c) -> b -> a -> c
```

In other words, `id` returns whatever you give it untouched, and `flip` takes a function of two arguments and swaps the order that you need to pass the arguments.

So what is the type of `flip id` then? The question sounds nonsensical. `id` is a function of one argument and `flip` operates on a function of two arguments. The trick here is to realize that the generic `a` in the signature for `id` can be replaced with any type, even the type of another function.

Replacing each `a` with `(a -> b)` gives us `id :: (a -> b) -> (a -> b)`. Furthermore, due to currying we can remove the second set of parentheses, which gives us `id :: (a -> b) -> a -> b`.

Just like that we have "made" `id` into a function of two arguments. Applying `flip` to `id` is now as simple as swapping the `(a -> b)` and the `a` in the type, giving us `flip id :: a -> (a -> b) -> b`.

Now that we know the type of `flip id` we can easily find the type of `flip (flip id)` by swapping back the `a` and `(a -> b)` to get `flip (flip id) :: (a -> b) -> a -> b`. Looks familiar? The function application operator also has this type!

So what is going on here? What does this identity mean? Some readers might be familiar with the identity `flip (flip x) == x`. If not, it is fairly intuitive-- flipping two things and then flipping them back leaves those things in their original state. Thus, `flip (flip id) == id` and furthermore, `id == ($)`. Wait, what?

Notice that a couple paragraphs ago, after expanding and currying its type, we said that `id :: (a -> b) -> a -> b`.




module Main exposing (main)

import Browser
import Html exposing (Html)



main =
    Browser.document
        { init = always ( 1, Cmd.none )
        , view = always { title = "Hello", body = Html.text "hello" }
        , update = identity
        , subscriptions = always Sub.none
        }


update : Msg -> Model -> ( Model, Cmd Msg )
identity : (a -> b) -> a -> b

flip identity

flip : (x -> y -> z) -> y -> x -> z

x := (a -> b)
y := a
z := b

a -> (a -> b) -> b

x := a
y := (a -> b)
z := b

(a -> b) -> a -> b
