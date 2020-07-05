---
title: "Quick Tip: Invoking Liquid Tags from Custom Filters"
---

I was recently working on a [Jekyll](https://jekyllrb.com/) website and figured out a neat trick involving custom filters. I was creating a filter to extend the functionality of the [Jekyll `link` tag](https://jekyllrb.com/docs/liquid/tags/#links) to specially handle certain site-specific URLs and delegate to the `link` tag in all other cases.

The tricky part of writing this filter was delegating to the `link` tag from Ruby, as Liquid tags normally must be put in your markup. However, using the code below I was able to do it. (Just replace `<TAG>` with your tag of choice, in my case `Link`, and replace `<INPUT>` with the input to the tag.)

```
Jekyll::Tags::<TAG>.send(
    :new,
    Jekyll::Tags::<TAG>.tag_name,
    <INPUT>,
    Liquid::ParseContext.new
).render(@context)
```

Using this trick, I was able to successfully write the [filter I needed](https://github.com/pzp1997/pennocp.org/blob/17469d51a14f831a63358e166c8bf092c8ebdf3f/src/_plugins/custom_filters.rb#L51-L56).

While Shopify has pretty good documentation for creating [custom filters](https://github.com/Shopify/liquid/wiki/Liquid-for-Programmers#create-your-own-filters) and [custom tags](https://github.com/Shopify/liquid/wiki/Liquid-for-Programmers#create-your-own-tags), this trick wasn't mentioned so I thought I'd share it here.
