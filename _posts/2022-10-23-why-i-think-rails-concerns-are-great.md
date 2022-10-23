---
layout: post
title: Why I think Rails concerns are great
categories:
  - Rails
---

Let's start with a refresher on what concerns are. This is a quote on concerns from DHH I totally resonate with:

> It’s a writing style. Like using subheads to explain subservient ideas within a broader context. You could probably extract all those subheads out, and turn them into little essays of their own. But that’s often just not the right level of extraction.

A concern starts with extending `ActiveSupport::Concern` and it would look like this:

```ruby
  module Deletable
    extend ActiveSupport::Concern
    
    included do
      scope :deleted, -> { where(status: DELETED) }
    end
    
    def deleted?
      status == DELETED
    end
  end
```

Then, you get 3 things out of the box:

* the included block (there's also a [prepended block](https://api.rubyonrails.org/v7.0.4/classes/ActiveSupport/Concern.html#method-i-prepended) option available) is evaluated in the context of the class, so that in our case, the `deleted` scope becomes available to the model including the concern 
* a `class_methods` block which can make class methods available to the model including the concern
* any other methods or constants that are defined outside the included/prepended blocks, such as our `deleted?` method, become available to the model including the concern, as with regular mixins

Here's how you could use it in a model:

```ruby
  class Post < ApplicationRecord
    include Deletable
    
    scope :inactive, -> { deleted.or(where(status: ARCHIVED) }
    
    def active?
      !deleted?
    end
  end
```

But why would you use concerns after all? Why not jam in those scopes and methods directly into the model? Well, I see two main benefits to using concerns:

* concerns can encapsulate logic that can be used across multiple models; in the example above, you can reuse the `Deletable` concern in many other models across the codebase while adhering to the DRY principle
* concerns can help cut bigger or smaller models into multiple facets, with each facet dealing explicitly with a part of the logic

I've read countless blog posts and threads on Reddit or Hacker News stating that concerns are rubbish. Most people note that cutting a model into multiple concerns just hides away the complexity. Then, on the outside, the model seems clean and tidy, while it is still humongous behind the curtains. 

But then, you might ask, what is the alternative? There are 2 main ways to avoid using concerns: either by using POROs (plain old Ruby objects) or service objects. 

A PORO implementation would look like this:

```ruby
  class DeletablePost
    def initialize(post)
      @post = post
    end
    
    def deleted?
      @post.status == DELETED
    end
  end
```

A service object implementation would look like this:

```ruby
  class DeletionService
    def post_deleted?(post)
      post.status == DELETED
    end
  end
```

These code snippets look perfectly OK, but let's look at how you would use them:

```ruby
  post = Post.find(1)
  post.deleted? # using concerns
  DeletablePost.new(post).deleted? # using POROs
  DeletionService.post_deleted?(post) # using service objects
```

Which one do you find the most readable? I believe that POROs and service objects can make for very weird syntax, and especially when the logic can be encapsulated by a concern.

This is not to say that POROs or service objects are bad. They are not, and they can be used in a variety of useful ways. However, they don't solve the problems people on the internet state in regards to concerns: doesn't a PORO or service object actually put the many facets the model has into multiple *drawers*? Don't they hide away the complexity? Yes, on the surface it might not seem so because *they are standalone classes*, but the deletion logic that is still inherently part of the post logic now resides in a different class - it is still hidden somewhere, while being inherently tied to the Post logic.

Moreover, what happens if you want to make the `Post` model `Archivable`, accompanied by a variety of methods, constants and scopes? You will create yet another service object or PORO that will hold the logic. This way, you start polluting your codebase with many small objects and each will deal with the different facets of posts.

And this brings me to my final argument for using concerns. Concerns are a way of cutting out some part of the model's essence while still staying *deeply connected* to the model - by looking at the model and seeing even a long list of concerns (hopefully with proper naming), you can clearly see what that post is composed of - the model itself will hold its essence, while the concern components will hold important parts of the model, just not part of its essence.

I am a strong believer in programmer happiness and that a programmer is synonymous with a code **writer**. Then, what would a great programmer look like? Well, in my opinion, a **great** programmer is one that **writes well** - he or she is expressive and puts out ideas in a clear and concise way. 

As Matz created Ruby for programmer happiness, we should ourselves continue to strive to make ourselves happier. The most important step to do that is to write expressive, clear and concise code. And this is in essence what concerns provide - a writing style that makes for expressive and readable code.
