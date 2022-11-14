---
layout: post
title: How to use feature flags in Rails
categories:
  - Rails
---

# How to use feature flags in Rails

Feature flags permit enabling and disabling certain functionalities in an application without deploying new code. They are used to control and experiment a feature throughout its lifecycle by reducing risk.

As an example, a company wants to roll out a new implementation of their payment service to customers. The programmers realize that this is a major change to the system, therefore they use a feature flag to gradually roll out the new implementation. 10% of the users get it in the first week - if monitoring reports no errors, then they might roll out the new payment service to an extra 20% of the customers. Eventually, after a couple of weeks of good results, they can roll out the feature to everyone and remove the feature flag altogether.

I'm not a fan of reinventing the wheel - if you can use an already existing, battle-tested 