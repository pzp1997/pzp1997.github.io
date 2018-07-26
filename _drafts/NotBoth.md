Can talk about InclusiveOr type too, which is isomorphic to `Either (Either a b) (a , b)` by the identity (a ^ b) ^ (a & b) === a | b.


I was watching the part of @rtfeldman’s Elm Europe talk where he explains that the Loading state should allow for either a doc and or a problem but not both (https://youtu.be/MlyGg-DIqnU?t=55m48s). I found this problem familiar having dealt with it myself in the past, or in other words it seems to me that this is a fairly common way of relating two piece of data. I started wondering why we don’t provide a generalized type for this out-of-the-box. We already have `Either` (an exclusive or relationship) and `(,)`(an and relationship), so why not add a NAND relationship too?
```type NotBoth a b
    = Neither
    | Left a
    | Right b```
Now there is something to be said for using a custom union type here. Richard’s solution in his talk was quite elegant. But interested in people’s thoughts anyway. Also curious if this idea has manifested itself in other languages or if it has been discussed elsewhere.
