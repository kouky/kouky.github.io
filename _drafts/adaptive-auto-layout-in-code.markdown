---
layout: post
title:  "Adaptive Auto Layout in Code"
categories: blog ios
---

[Matthew Sanders][matthew] posted an excellent article in September 2014 on [Adaptive Layouts for iPhone 6][matthew-article]. I worked through it meticulously, following along with the examples in Xcode 6 using Interface Builder to create views and auto layout constraints visually.

The last half of the article focussed on building an adaptive layout for a hypothetical Instragram interface. I managed to replicate his final results using Interface Builder. The purpose of this article is to recreate that adapatve interface in code.

{% include figure.html caption="Image by Matthew Sanders" asset="/assets/Instagram-rotate-size-class.gif" %}

In 2014 I started using [Masonry][masonry], a light-weight layout framework which wraps AutoLayout with a nicer syntax. The layout DSL Masonry provides is succinct, easy to recall and it has been a boon for my productivity when working with auto layout.

[matthew]: http://mathewsanders.com/
[matthew-article]: http://mathewsanders.com/designing-adaptive-layouts-for-iphone-6-plus/
[masonry]: https://github.com/Masonry/Masonry

