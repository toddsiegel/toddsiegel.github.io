---
layout: post
title: "Notes On Upgrading To Rails 2.3"
date: 2009-06-09 12:56
comments: false
categories: rails
---
I am upgrading from Rails 2.2.2 to 2.3.2 on a project today.  Here's what I've found so far.

1. RAILS\_ROOT/app/controllers/application.rb has been renamed to application\_controller.rb.  Upon upgrading if you see `/usr/lib/ruby/gems/1.8/gems/activesupport-2.3.2/lib/active\_support/dependencies.rb:443:in 'load\_missing\_constant\':NameError: uninitialized constant ApplicationController`, that's the change you need to make.
2. `Test::Unit::TestCase` is now `ActiveSupport::TestCase`. The error you’ll see is undefined method \`use\_transactional\_fixtures=.
3. Using `fixture_file_upload` stops working unless you `include ActionController::TestProcess`.

More as I find them…