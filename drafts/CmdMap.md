Write a blog post explaining Cmd.map in the context of this interaction in #beginners on Elm Slack. Maybe also explain map as a larget concept in a follow-up post.

vegard [11:02 AM]
Hello, any ideas how to avoid the `Cmd.map`? Without it Elm complains that `Cmd Msg` not equal to `Cmd DatePicker.Msg`
```init : ( Model, Cmd Msg )
init  =
    let
        dateCmd =
            Task.perform NewDate Date.now

        ( datePicker, datePickerFx ) =
            DatePicker.init
    in
    ( initialModel datePicker, Cmd.batch [ dateCmd, Cmd.map ToDatePicker datePickerFx ] )```

palmerpa [11:04 AM]
@vegard Why do you want to avoid `Cmd.map`? What’s the high-level goal you’re trying to accomplish?

vegard [11:06 AM]
Trying to figure out why it's there. The docs about is just a type signature

jessta [11:08 AM]
@vegard you've said that `init` returns a `( Model, Cmd Msg )`, but `datePickerFx` isn't a `Cmd Msg`, it's a `Cmd DatePicker.Msg`. So you need a way to convert your `Cmd DatePicker.Msg` to a `Cmd Msg` that can be returned from your `init` function (edited)

jessta [11:10 AM]
@vegard if you look at the type of `Cmd.map`, `map : (a -> msg) -> Cmd a -> Cmd msg`  you can see what it does.

palmerpa [11:15 AM]
@vegard It has to do with nested levels of messages. According to some people, you should avoid cluttering up your messages by organizing them in substructures. For example, the messages that are relevant for the date picker are of type `DatePicker.Msg`. But eventually you need to be able to use these messages in your main app. The way to do that is by creating a main `Msg` constructor that wraps messages of the subtype (in this case `ToDatePicker`). `Cmd.map` is being used here to perform that wrapping. The reason why you need to wrap them in the first place is for the types to match as @jessta said above.
Note that I said according to some people above. Modeling your app as a bunch of substructures is more or less another name for components, which is something that Elm generally tries to avoid.

palmerpa [11:20 AM]
I believe you are using the elm-community/elm-datepicker package though. In that case it is fine, as reusable packages more or less need to design things in this way.
@vegard I definitely could have done a better job explaining that. Questions?

vegard [11:24 AM]
I think I get it. So basically it wraps `something` into a new `Cmd` and we have to unwrap `something` in the update
The explanation was good :+1:

palmerpa [11:30 AM]
Yeah, more or less. A little more accurately, we have a command `datePickerFx` that produces messages of type `DatePicker.Msg` (the substructure). We need it to produce messages of type `Msg` (our main message type). The way we get that to happen is by saying that we should wrap any message produced by `datePickerFx` with `ToDatePicker` so that we will wind up with a message of type `Msg`.
And `Cmd.map ToDatePicker datePickerFx` is how we express that idea.
And then the part about unwrapping `something` in update is right except that we are unwrapping messages instead of commands.

vegard [11:36 AM]
Great! I see how it fits together now when look again at `DatePicker.init` and `DatePicker.update`.

jessta [11:39 AM]
@vegard you'll see lots of `map` functions in various Core modules. `List.map`, `Html.map`, `Maybe.map`. They all work the same.
