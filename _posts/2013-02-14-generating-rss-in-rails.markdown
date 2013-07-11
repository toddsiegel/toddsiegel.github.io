---
layout: post
title: "Generating RSS in Rails"
date: 2008-02-29
categories: [rails, rss]
---
Generating RSS in Rails 2 is pretty easy. In the simplest case, all that is required is an extra view file to create the RSS output.

Consider a simple (RESTful) blog example:

{% highlight ruby %}
map.home '', :controller => 'posts', :action => 'index'
map.resources :posts
{% endhighlight %}

{% highlight ruby %}
def index
	@posts = Post.find(:all)
end
{% endhighlight %}

When I generated the posts controller, Rails created an `index.html.erb` file for the index action.  The file name tells Rails what to do 'index' is the action, 'html' and 'erb' are the format and builder to use, respectively. To generate RSS we want to use the XML Builder, so the file name to use is `index.rss.builder`. And the file contents should look something like this:

{% highlight ruby %}
xml.instruct! :xml, :version => "1.0"
xml.rss :version => "2.0" do
  xml.channel do
    xml.title "My Blog"
    xml.description "My Fantastic Blog"
    xml.link posts_url

    for post in @posts
      xml.item do
        xml.title post.title
        xml.description post.content
        xml.pubDate post.created_at.to_s(:rfc822)
        xml.link post_url(post)
      end
    end
  end
end
{% endhighlight %}

Now that is really easy, but I’ve experienced two issues with this. The first issue is with layouts. If the controller uses a layout for all actions (using layout => "mylayout") it will cause Rails to look for a layout file named `layouts/posts.rss.erb`. This can be bypassed by excluding the layout for the rss format, like so:

{% highlight ruby %}
def index
  @posts = Post.find(:all)

  respond_to do |format|
    format.html
    format.rss  { render :layout => false }
  end
end
{% endhighlight %}

The other issue I experienced was with the will_paginate plug-in where my index method looks like this:

{% highlight ruby %}
def index
  @posts = Post.paginate(:per_page => 10, :page => params[:page])
end
{% endhighlight %}

The problem is that only 10 posts will show up in the RSS feed, which is not what I want.
There are at least two good ways I came up with to deal with this issue. The my preferred way is create a new feed method that is excluded from the layout and returns all posts.

{% highlight ruby %}
def feed
  @posts = Post.find(:all)
end
{% endhighlight %}

with a new route:

{% highlight ruby %}
map.home '', :controller => 'posts', :action => 'index'
map.resources :posts
map.connect 'posts/feed.:format', :controller => 'posts', :action => 'feed'
{% endhighlight %}

The second way examines the request format and grabs the posts via paginate for HTML and find for RSS

{% highlight ruby %}
def index
  if request.format.html?
    @posts = Post.paginate(:per_page => 10, :page => params[:page])
  else
    @posts = Post.find(:all)
  end

  respond_to do |format|
    format.html
    format.rss  { render :layout => fals }
  end
end
{% endhighlight %}

I like the first way better. Seems a little cleaner. I like small methods.
