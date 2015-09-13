---
layout: post
title:  "Property Mapping With Mantle"
date:   2014-12-07 16:00:00
permalink: /1
---

[Mantle](github.com/Mantle/Mantle) is a great framework for creating immutable models based on JSON. The models are serializable, you can merge them together or turn them back to dictionaries if need arises. Also you can do model migrations with relative ease.

I’ve found Mantle to be really intuitive to use, except when trying to customize property mapping on model creation. When using the `initWithDictionary:error:`, Mantle will try to do one to one mapping from the JSON dictionary to class properties. If JSON dictionary has key `name`, it’ll be mapped to property called `name` in the model. In many cases this is okay, but not always.

When working with 3rd party APIs the keys in JSON responses aren’t always perfect. They could contain spaces, dashes and other characters that aren’t allowed in property names in Objective-C. In cases like this, the one to one mapping of `initWithDictionary:error:` just doesn’t work. On the other hand, you might want to map a key from JSON to a property of different name in the model. Let’s say JSON has `id` and you’d like to map it to `identifier`. That can’t be done with one to one mapping either. So what to do?

Fortunately, you can do this using `MTLJSONAdapter` using its class method `modelOfClass:fromJSONDictionary:error`. It will create a model for the class and uses `JSONKeyPathsByPropertyKey` while mapping values from JSON to your model’s properties. I found this really awesome because it allows doing custom mapping from JSON to model’s properties and also circumvents the property name invalidness problem! I’m also a bit flabbergasted that I had to find out about this by scratching my head and reading Mantle’s sources.

For instance, if backend serves you JSON like this:
{% highlight objc %}
{
    "character name": "Danno",
    "id": 1234567890
}
{% endhighlight %}
and you'd like store it to a model such as
{% highlight objc %}
@interface HFOActor : MTLModel <MTLJSONSerializing>

@property (nonatomic, copy, readonly) NSString *name;
@property (nonatomic, readonly) NSUInteger identifier;

@end
{% endhighlight %}
In this situation, the one to one mapping is clearly going to fail. So you need to do property mapping to achieve the desired results. To do that implement `JSONKeyPathsByPropertyKey` from `MTLJSONSerializing`.

{% highlight objc %}
+ (NSDictionary *)JSONKeyPathsByPropertyKey
{
    return @{
        @"name": @"character name",
        @"identifier": @"id",
    };
}
{% endhighlight %}
Then instantiating a model using the parsed JSON dictionary can then be done by `modelOfClass:fromJSONDictionary:error`.
{% highlight objc %}
NSError *error;
HFOActor *actor = [MTLJSONAdapter
                   modelOfClass:[HFOActor class]
                   fromJSONDictionary:jsonDictionary
                   error:&error];
// handle the error here
{% endhighlight %}

To me it feels rather cumbersome to write all the boilerplate for handling the possible error, do the `[HFOModel class]` along with passing the JSON dictionary when using `MTLJSONAdapter`s `modelOfClass:fromJSONDictionary:error`. To make creating models simple, I add a factory method for the model class. The method will handle all the nasty boilerplate.

{% highlight objc %}
+ (HFOActor *)actorFromDictionary:(NSDictionary *)dictionary
{
    NSError *error = nil;
    HFOActor *actor = [MTLJSONAdapter
                       modelOfClass:[HFOActor class]
                       fromJSONDictionary:jsonDictionary
                       error:&error];

    if (error) {
        LogError(@"Failed to create actor");
        LogError(@"   jsonDictionary: %@", dictionary);
        LogError(@"   error: %@", error);
        return nil;
    }

    return actor;
}
{% endhighlight %}

Clean and simple.

While writing this I noticed that Github’s [Octokit.objc](https://github.com/octokit/octokit.objc) project has many _real world_ examples of using Mantle. I cloned and starred that repository as a reference. It’s also a great reference to using ReactiveCocoa.
