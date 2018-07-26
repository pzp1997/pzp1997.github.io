rtfeldman [1:50 PM]
I haven't shared this publicly (or with anyone else yet), but curious what you think of it! https://gist.github.com/rtfeldman/c066763e7b9295a7f646b32f32c390de

palmerpa [1:52 PM]
Thanks for sharing! I'm at work now, so I'll try to read it and respond to you tonight. And of course I won't share it with anyone else.

rtfeldman [2:09 PM]
sounds good!

palmerpa [11:24 PM]
Very well written post. I completely agree with you about the three problems with equality. I agree with you that your proposal feels like a very Elm like solution to the equality problems.

I’m split over whether it would improve or detract from readability to define a type alias `Eq a = a -> a -> Bool`. On one hand, it is another piece of information that beginners would need to learn. On the other hand, once you know what it means it makes the signatures easier to visually grep (at least imo). I’m going to use that notation below for ease.

One issue that I have with the broader proposal for explicit arguments is whether or not it scales well when considering other constraints. What happens when I need to be able to check a generic value for equality _and_ convert it to a string _and_ …? Sure, you can continue to prepend more explicit args to the function but I can definitely see that getting out of hand. It will be difficult to remember the correct order for the explicit arguments especially when some library authors decide that `foo` should be `Eq a -> ToString a -> ...` and others decide that `bar` should be `ToString a -> Eq a -> ...`. And perhaps the community will decide on some convention, such as `Eq` before `Hashable` before `ToString`, and then through our attempts to reduce implicit knowledge we will end up subtly introducing more of it.

Now I’d like to respond to the faults with the implicit argument model that you brought up. I’m envisioning something along the lines of Haskell typeclasses but with the restriction that you must declare a type and the instances that it supports “simultaneously.” Something like (ignoring minor syntax details):
```type Foo
  = Bar
  | Baz
  where
    (==) x y = ...
    toString x = ...```
1. You can tell whether or not the type that you are working with is equatable by looking at the docs for that type. The same way that you can tell that `T.eq` is defined for some type `T` or that a function accepts an `Eq a` and therefore requires its arguments to be equatable.
2. Perhaps I’m assuming too much but I think that something along the lines of the first error message you gave as an example is pretty readable. I need to try to think about this from the perspective of someone who is coming from JavaScript or some other language where the expectation is that `(==)` “works” for all types.
3. I don’t think the rules are much more complicated than those for the explicit proposal. If the type defines `(==)` then you can use `(==)` with it.
4. I think you are referring to the current state of things here. I don’t think my proposal suffers from this critique since you need to explicitly implement `(==)` for your type.
5. Probably true that comparing records is a mistake. Whether or not the language should make it harder to do so is a whole different debate than the one I’d like to have. But in short we probably agree on this one.
6. It’s true that my proposal requires language-level support. You win here.

For me whether or not it is a pain to “explicitly write out an equality function for a custom union type,” is neither here nor there. I personally wouldn’t mind having to do that, but I can see people complaining about verbosity (probably the same people who don’t like having to implement their own JSON decoders). I don’t think it would be unreasonable to provide a mechanism for automatically deriving an equality function for your type either based on the current rules or some improved variant of them. (edited)
I was in the middle of writing and I pressed enter by accident. Going to continue editing it, so please don’t read yet :slightly_smiling_face:
Ah I see, Slack has different rules about `<Enter>` vs `<Shift>+<Enter>` depending upon whether you have an open code block.

palmerpa [11:49 PM]
Okay, done. I hope I didn’t come off as too abrasive in my response. I think that your proposal is in many ways better than the status quo and definitely more in line with recent Elm language decisions than my half-baked one :wink:. Ultimately, we agree on the important thing though-- that equality is problematic and should be addressed in some way.

rtfeldman [12:27 AM]
awesome!
regarding `toString`, I'm not convinced `Show` needs to exist
I don't think `Show` should ever be used for user-facing strings, only for debugging (other than converting numbers, which doesn't need to scale)
in 0.19, `Debug.toString` prints the internal structure of the type, which I think is sufficient for debugging purposes and doesn't need to be customized beyond that
I think explicit also works fantastically for `Ord` and `Hashable`, but I haven't written that part up yet :smile:
incidentally I took a stab at converting `elm-spa-example` to a hypothetical version of `core` that followed this proposal; here's the diff https://github.com/rtfeldman/elm-spa-example/compare/pre-alt-core...alt-core

palmerpa [12:34 AM]
Fair enough regarding Show. I actually really like the way 0.19 handles it. Distinguishing between development and production environments is one of the more interesting features in the release in my mind.

rtfeldman [12:34 AM]
agreed!
your proposal reminds me of something I sketched out awhile ago: https://gist.github.com/rtfeldman/76d14fffc3c4c443341ddcdeca5d9a2c

palmerpa [12:34 AM]
Looking at your diff now.

palmerpa [12:44 AM]
The diff looks good. I kind of like your old sketch.
It seems like it allows for user defined interfaces though, which I’m not sure I would even include for simplicity’s sake. I’m fine with letting Evan or whoever else choose some common set of interfaces to support. I think allowing arbitrary instance declarations is enough by itself. (edited)

rtfeldman [12:47 AM]
just for equality, comparisons, and hashing?

palmerpa [12:48 AM]
Personally I would like to see appendable and mappable as well, but I think it is worth having a debate about what should be included.
I’d have to go through elm/core thoroughly and look at a complete list of Haskell typeclasses in Prelude before presenting a thorough list. But those are the one’s that I can think of off the top of my head.

rtfeldman [12:50 AM]
interesting, why mappable and not traversable?

palmerpa [12:50 AM]
Sure I think Traversable is also worthwhile
That’s one of the one’s I forgot
I don’t think I would include AndThenAble though to avoid complexity

rtfeldman [12:51 AM]
:smile:
speaking of `andThen` - I separately had a syntax idea...see if you can guess what this would desugar to without any explanation:

https://gist.github.com/rtfeldman/09205999ee87e399d227ef1b0cc5b2d1

palmerpa [12:55 AM]
Heh not bad. I’m not really a fan of `do` notation in Haskell so it doesn’t appeal to me that much, but it’s definitely an improvement over that.

rtfeldman [12:56 AM]
do you chain `IO` with `>>=` directly?

palmerpa [12:57 AM]
sometimes :blush:
It definitely has its use cases but outside of IO and maybe a few other cases I think it is mostly confusing
The code that I write using `do` notation almost always winds up being more “imperative” than it should be.

rtfeldman [1:00 AM]
like with `State`?

palmerpa [1:00 AM]
Yeah

rtfeldman [1:03 AM]
interesting...so I guess you'd use a fold otherwise?

palmerpa [1:04 AM]
fold other than… ?

rtfeldman [1:05 AM]
`do` with something like `State`
in a more imperative style

palmerpa [1:07 AM]
Ah no I have no problem with using `State`itself, it’s just that I would use bind or Kliesli composition or some other operator instead of do. I find that I often find neater ways of composing when doing that.
For example, `cat = getArgs >>= mapM_ (readFile >=> putStr)`
lol looking back at the snippet, I’m realizing that I’m climbing the stairs of the ivory tower
I don’t think that Elm code should look like that necessarily. That’s just how I prefer to write my Haskell.

rtfeldman [1:10 AM]
haha that's fair

palmerpa [1:12 AM]
Anyway I have to go sleep now. Super interesting conversation. Would love to continue it at some point, particularly the stuff about explicit vs implicit parameters.

rtfeldman [1:14 AM]
sounds good!
'night!

palmerpa [9:36 AM]
I took 10 minutes this morning and came up with this list:
- Equatable
- Comparable
- Appendable (I could even see this being more similar to a semigroup than a monoid)
- Mappable
- Traversable (more similar to Haskell’s Foldable than Haskell’s Traversable in terms of functionality though)
- Numeric (I feel like this one has the greatest opportunity for abuse, but given that custom operators will soon be gone I think this could be a decent solution to the math operators use case that Evan brings up in his post https://gist.github.com/evancz/769bba8abb9ddc3bf81d69fa80cc76b1)

palmerpa [9:42 AM]
Three of them we already have just in a very limited way (comparable, appendable, and number). One of them we both agree should probably be done differently (equality). Most types in Elm already provide for some kind of mapping functionality so I don’t think it is too crazy to formalize its importance. I haven’t seen too many types that have natural Traversable instances in Elm but I do think it is a useful one to have because of how many functions can be implemented in terms of it. Plus I think it’s one of those things that will be used more heavily once it exists.

palmerpa [9:49 AM]
I also just remembered that `comparable`, `appendable`, and `number` also require special language support (and in a pretty strange way). So it is not unprecedented to deal with these problems at the language-level even in Elm.

rtfeldman [12:13 PM]
I agree on Numeric - would be cool for e.g. matrix math libraries, but people would super duper abuse it now that infix operators are disallowed
like people might define a `Set` alternative where `+` unions them, `-` diffs them...
I'm really curious about Mappable though - how would you personally see yourself using that one in Elm?

palmerpa [12:18 PM]
I wonder how tricky it would be to enforce that the provided instances satisfy some set of laws to curb abuse. Given that Haskell doesn't do that but probably wishes they did, I'm going to venture to say "pretty tricky."
I have to think about Mappable a little bit. I'll get back to you on that one.

rtfeldman [12:20 PM]
Cool!
