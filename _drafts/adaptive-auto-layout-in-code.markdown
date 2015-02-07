---
layout: post
title:  "Adaptive Auto Layout in Code"
categories: blog ios
---

[Matthew Sanders][matthew] posted an excellent article in September 2014 on [Adaptive Layouts for iPhone 6][matthew-article]. I worked through it meticulously, following along with the examples in Xcode 6 using Interface Builder to create views and auto layout constraints visually.

{% include figure.html caption="Image by Matthew Sanders" asset="/assets/Instagram-rotate-size-class.gif" %}

The last half of the article focussed on building an adaptive layout for a hypothetical Instragram interface. I replicated the final results using Interface Builder however I was not impressed by the tediousness of installing, adjusting, and inspecting auto layout constraints visually.

The purpose of this article is to illustrate how that adapative Instagram interface can be succicntly recreated in code. I'll be using [Masonry][masonry] a light-weight layout framework which wraps AutoLayout with a nicer syntax.

### Simple Squares

To illustrate the succint layout DSL provided by [Masonry][masonry] we can recereate the adaptive coloured squares examples from first half of Matthew's article.

{% include figure.html caption="Image by Matthew Sanders" asset="/assets/Constraint-5.gif" %}

The code snippet below creates the Auto Layout constraints for the coloured squares in the `UIView` method [updateConstraints][update-constraints]. The chainable expressive syntax of Masonry shines through. A working example is availble from my fork of Masonry on [github][masonry-squares-fork].

{% highlight objective-c %}
- (void)updateConstraints
{
    UIView *superView = self;
    UIEdgeInsets padding = UIEdgeInsetsMake(20, 20, 0, 20);

    [self.blueSquareView mas_makeConstraints:^(MASConstraintMaker *make) {

        // Make the view a square
        make.height.equalTo(self.blueSquareView.mas_width);

        // Pin the view to the superview
        make.top.and.left.equalTo(superView).with.insets(padding);

        // Make the blue square width equal to the pink square
        make.width.equalTo(self.pinkSquareView);

        // Pin the blue square right edge to the pink square left edge
        make.right.equalTo(self.pinkSquareView.mas_left).with.offset(-20);
    }];

    [self.pinkSquareView mas_makeConstraints:^(MASConstraintMaker *make) {

        make.top.and.right.equalTo(superView).with.insets(padding);
        make.height.equalTo(self.pinkSquareView.mas_width);
    }];

    [super updateConstraints];
}
{% endhighlight %}

### Install Size Class Constraints

The following function when called will create and install the generic constraints for our top level views. By generic we mean constraints which apply for both regular and compact [size classes][adaptivity-layout]. In interface builder and Matthew's article this corrresponds to the _any-width any-height_ setting.

{% highlight objective-c %}
- (void)installGenericConstraints
{
    UIView *superView = self.view;

    [self.headerView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.height.equalTo(@60);
        make.left.right.and.top.equalTo(superView);
    }];

    [self.pictureView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.height.equalTo(self.pictureView.mas_width);
        make.left.equalTo(superView);
    }];

    [self.authorView mas_makeConstraints:^(MASConstraintMaker *make) {
        addObject:make.height.equalTo(@50);
        make.top.equalTo(self.headerView.mas_bottom);
    }];
}
{% endhighlight %}

The following function creates layout constraints which apply only to the iPhone portrait orientation. The Masonry constraint maker `mas_makeConstraints:` returns an array of the constraints created. We use that return value to store the portrait constraints in a view controller property. Enabling us to switch them off and on again as the device changes orientation.

{% highlight objective-c %}
- (void)installPhonePortraitConstraints
{
    NSMutableArray *constraints = [[NSMutableArray alloc] init];

    [self.authorView mas_makeConstraints:^(MASConstraintMaker *make) {
        [constraints addObject:make.left.and.right.equalTo(self.view)];
    }];

    [self.pictureView mas_makeConstraints:^(MASConstraintMaker *make) {
        [constraints addObject:make.top.equalTo(self.authorView.mas_bottom)];
        [constraints addObject:make.right.equalTo(self.view)];
    }];

    [self.likesView mas_makeConstraints:^(MASConstraintMaker *make) {
        [constraints addObject:make.top.equalTo(self.pictureView.mas_bottom).with.offset(5)];
        [constraints addObject:make.left.and.right.equalTo(self.view)];
    }];

    self.phonePortraitConstraints = [constraints copy];
}
{% endhighlight %}

Similarly ehe following function creates layout constraints which apply only to the iPhone landscape orientation. Once again we stored the constraimts in a property so we can swicth them on and off as required.

{% highlight objective-c %}
- (void)installPhoneLandscapeConstraints
{
    NSMutableArray *constraints = [[NSMutableArray alloc] init];

    [self.authorView mas_makeConstraints:^(MASConstraintMaker *make) {
        [constraints addObject:make.left.equalTo(self.pictureView.mas_right)];
        [constraints addObject:make.width.lessThanOrEqualTo(self.pictureView)];
    }];

    [self.pictureView mas_makeConstraints:^(MASConstraintMaker *make) {
        [constraints addObject:make.top.equalTo(self.headerView.mas_bottom)];
        [constraints addObject:make.bottom.equalTo(self.view)];
    }];

    [self.likesView mas_makeConstraints:^(MASConstraintMaker *make) {
        [constraints addObject:make.top.equalTo(self.authorView.mas_bottom)];
        [constraints addObject:make.left.equalTo(self.pictureView.mas_right)];
        [constraints addObject:make.width.lessThanOrEqualTo(self.pictureView)];
    }];

    self.phoneLandscapeConstraints = [constraints copy];
}
{% endhighlight %}


[matthew]: http://mathewsanders.com/
[matthew-article]: http://mathewsanders.com/designing-adaptive-layouts-for-iphone-6-plus/
[masonry]: https://github.com/Masonry/Masonry
[update-constraints]: https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/instm/UIView/updateConstraints
[masonry-squares-fork]: https://github.com/kouky/Masonry/blob/squares-example/Examples/Masonry%20iOS%20Examples/MASExampleSquaresView.m
[adaptivity-layout]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/LayoutandAppearance.html

