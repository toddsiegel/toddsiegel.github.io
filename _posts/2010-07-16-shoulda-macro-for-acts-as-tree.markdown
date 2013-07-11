---
layout: post
title: "Shoulda Macro for `acts_as_tree`"
date: 2010-07-16 10:40
categories: [rails, testing]
---
I love writing tests and I love Shoulda.  Here is an acts_as_tree macro I threw together.

{% highlight ruby %}
class Test::Unit::TestCase

  def self.should_act_as_tree(opts = {})
    klass = self.name.gsub(/Test$/, '').constantize

    foreign_key = get_options!([{:foreign_key => 'parent_id'}.merge(opts)], :foreign_key)

    context "To support acts_as_tree" do
      should have_db_column(foreign_key).of_type(:integer)
    end

    should "include ActsAsTree methods" do
      assert klass.include?(ActiveRecord::Acts::Tree::InstanceMethods)
      assert klass.methods.include? "root"
      assert klass.methods.include? "roots"
    end
  end
end
{% endhighlight %}