---
layout: post
title: How to use feature flags in Rails
categories:
  - Rails
---

Feature flags permit enabling and disabling certain functionalities in an application without deploying new code. They are used to control and experiment with a feature throughout its lifecycle.

Feature flags are comprised of 2 main components - the feature and the target audience. The feature might become available for a percentage of the total number of users, exclusively for certain email domains, users that belong to specific companies, users in a specific environment, such as staging, and so on.

As an example, a company wants to roll out a new implementation of their payment service to customers. The programmers realize that this is a major change to the system, therefore they use a feature flag to gradually roll out the new implementation. 10% of the users get it in the first week - if monitoring reports no errors, then they might roll out the new payment service to an extra 20% of the customers. Eventually, after a couple of weeks of good results, they can roll out the feature to everyone and remove the feature flag altogether.

If the codebase or the use cases for feature cases are simple enough, it makes sense to implement your own feature flag service. There are great tools available, such as [flipper](https://github.com/jnunemaker/flipper). However, in more complex scenarios, I would vote for using a battle-tested and fully-serviced application. [LaunchDarkly](https://launchdarkly.com) is one of the most popular and robust options available.

In order to use LaunchDarkly in Rails, the first step is to install the LaunchDarkly Ruby SDK. Let's add the following in the Gemfile:

```ruby
gem 'launchdarkly-server-sdk'
```

The next step is to add a new initializer under `config/initializers/feature_flag.rb`:

```ruby
Rails.configuration.feature_flag_client = LaunchDarkly::LDClient.new(ENV['SDK_KEY'])
```

Let's create now a new concern called `Flaggable`:

```ruby
module Flaggable
  extend ActiveSupport::Concern

  included do
    # LaunchDarkly mapped feature flag user. Relies on classes
    # including this concern to provide name and email attributes.
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
    #
    # Feature flag variations determine what a user sees when they encounter a feature flag.
    # Every flag has at least two variations, one for when the flag is off, and one for when it's on.
    # However, they can represent different other values than boolean,
    # such as numbers: for example, number of rows to show in a paginated table.
    #
    # Current values supported: boolean, string, number and JSON.
    def variation(flag_name:, user:, default_value:)
      feature_flag_client.variation(flag_name, user, default_value)
    end
end
```

Suppose there is a User model in the app. In order to use the `Flaggable` concern, just include it as follows:

```ruby
class User
  include Flaggable
  ...
end
```
