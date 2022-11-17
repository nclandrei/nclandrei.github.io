---
layout: post
title: How to use feature flags in Rails
categories:
  - Rails
---

Feature flags permit enabling and disabling certain functionalities in an application without deploying new code. They are used to control and experiment with a feature throughout its lifecycle.

Feature flags are comprised of 2 main components - the feature and the target audience. Firstly, a feature is implemented and it's ready to be flagged. A flag is created to control who can use the feature. The feature then becomes available for a percentage of the total number of users, exclusively for certain email domains, users that belong to specific companies, users in a specific environment or whatever criterion you used for the target audience.

As an example, a company wants to roll out a new implementation of their payment service to customers. The programmers realize that this is a major change to the system, therefore they use a feature flag to gradually roll out the new service. 10% of the users get it in the first week - if monitoring reports no errors, then they might roll it out to an extra 20% of the customers. Eventually, after a couple of weeks of good results, they can roll out the feature to everyone and remove the feature flag altogether.

If the codebase or the use cases for feature cases are simple enough, it makes sense to implement your own feature flag service. There are great tools available, such as [flipper](https://github.com/jnunemaker/flipper). However, in more complex scenarios, I would vote for using a fully-serviced application. [LaunchDarkly](https://launchdarkly.com) is one of the most popular and robust options available.

In order to use LaunchDarkly in Rails, the first step is to install the LaunchDarkly Ruby SDK. Let's add the following in the Gemfile:

```ruby
gem 'launchdarkly-server-sdk'
```

The next step is to add a new initializer under `config/initializers/feature_flag.rb`:

```ruby
Rails.configuration.feature_flag_client = LaunchDarkly::LDClient.new(ENV['SDK_KEY'])
```

Let's now build a concern called `Flaggable`:

```ruby
module Flaggable
  extend ActiveSupport::Concern

  included do
    # Is the feature flag enabled for the provided user?
    # Defaults to false if there's no feature flag with the given name.
    def feature_flag_enabled?(flag_name:)
      variation(
        flag_name:,
        user: feature_flag_user,
        default_value: false
      )
    end
    
    # Returns the variation for a specific flag and a specific user.
    # Values supported: boolean, string, number and JSON.
    def variation(flag_name:, default_value:)
      Rails.configuration.feature_flag_client.variation(
        flag_name, 
        feature_flag_user, 
        default_value
      )
    end

    private

    # Maps model to LaunchDarkly's feature flag user. Relies on the model including the
    # concern to provide name and email attributes.
    # Example of return value:
    #  {
    #    key: 'User - 123',
    #    email: 'test@test.com',
    #    name: 'John Doe'
    #  }
    def feature_flag_user
      {
        key: "#{self.class.name.demodulize} - #{id}",
        email: email,
        name: name
      }
    end
  end
end
```

Suppose there is a User model in the app. In order to use the `Flaggable` concern in the context of users, just include it as follows:

```ruby
class User
  include Flaggable
  ...
end
```

The `Flaggable` concern is ready to include in other models too because the `key`, which is the unique identifier for a feature flag user, is constructed from the model class name and ID. This could prove useful if you want, for example, to make a `Company` model flaggable and enable/disable feature flags for all users in certain companies.

After that, you must create a feature flag to actually start using LaunchDarkly. You can follow [this guide](https://docs.launchdarkly.com/guides/flags/creating-flags) on how to create a flag through LaunchDarkly's UI.

Assuming you created a boolean feature flag with the key set to `show-awesome-feature`, you can check whether the flag is enabled for the user as follows:

```ruby
Actor::User::User.last.feature_flag_enabled?(flag_name: 'show-awesome-feature')
```

Even though non-boolean flags are rarer, you can use them through the `variation` method:

```ruby
# elements_in_paginated_table will be a number determining how many elements will the user
# see on each table page.
elements_in_paginated_table = Actor::User::User.last.variation(
  flag_name: 'paginated-table-element-count', 
  default_value: 25
)
```
