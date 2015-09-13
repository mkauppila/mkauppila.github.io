---
layout: post
title:  "Tag - Every Mess has Humble Beginnings"
date:   2015-02-15 16:00:00
permalink: /2
---
Every view on iOS has an integer property named as `tag`. The property is used for identifying views within the view hierarchy. The actual identification based on the `tag` is done calling the `viewWithTag:` method of view’s parent view. Well, tags can’t be any simpler that but why to use them at all? After all, you could just keep a reference to a view and access it that way without a need to touch the tags.

Using a tag for a single view is rather simple and straightforward but, for instance, tagging all image views that have loaded proper image becomes cumbersome. It’s not possible to assign same tag (let’s say `kHasLoadedImage`) to all of those views, instead each view needs to have an unique tag in order to be accessible using `viewWithTag` (let’s say `kHasLoadedImage + 1`, `kHasLoadedImage + 2`, `kHasLoadedImage + 3`, …, `kHasLoadedImage + n`). In order to access the image views, you’d have to iterate through all the tags starting from `kHasLoadedImage`, access the view using `viewWithTag` and after that you can do something with the actual view.

The problem is only multiplied if you need to tag several different view groups. Then by accident, your tag sets might start overlapping which makes `viewWithTag` a little bit flakier. This is prone to happen if you generate the tag dynamically by adding values to the base tag. If the tag sets have collisions, you can’t be sure that you’ll get a correct view and that can be a problem which is potentially really annoying to debug. Identifying and accessing views by `tag` property can quickly become a horrible, unmaintainable mess.

Let’s consider the same example using an associated objects. We’ll add a property to the class using associated objects called `hasLoadedImage`. With this approach, it’s trivial to write clean and simple code to collect all the image views matching the criteria:

{% highlight objc %}
NSArray *viewsWithImage = [imageViews mk_filter:^(UIView *view) {
        return view.hasLoadedImage;
}];
{% endhighlight %}

The associated object based approach also scales better, in case you need to filter the views using several conditional statements. Also it makes the intent of the code more easily understanable.

In the past, I’ve ended up using `tag` once in awhile and I’ve regretted it later. I’ve never really found a way to use them properly. Subclassing, composing a new view class or adding properties to existing views using associated objects have better semantic structure and makes the code cleaner. In my opinion, the latter two are good choices depending on the context. Subclassing is also good choice but it has its own problems.

I believe that using the `tag` is an anti-pattern. It doesn’t offer proper semantics that would be easily understandable by code’s reader and using and handling a group of similar views with tagging is just cumbersome and error-prone mess.
