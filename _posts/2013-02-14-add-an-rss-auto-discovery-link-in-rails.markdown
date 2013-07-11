---
layout: post
title: "Add An RSS Auto-Discovery Link in Rails"
date: 2008-04-17 13:35
categories: [rails, rss]
---
This is a follow-up to another RSS post I have. Once you’ve created an RSS feed for your site you should create an auto-discovery link for it so that browsers and RSS readers can find it.
Don't know what I’m talking about? Go to pretty much any blog with a feed (in a somewhat recent browser) and you’ll see a little RSS icon in the address bar or toolbar if the browser auto-detected the feed.
Here's an example from Firefox 2:

![RSS Icon in Firefox](/images/rss-icon.jpg "RSS Icon in Firefox")

Here's IE7 (when the little star thingy appears, it's found a feed):

![RSS Icon in IE](/images/ierss.jpg "RSS Icon in IE")

Browsers and readers know the feed is there because in the `<head>` of the page there is a `<link>` tag telling it the feed is there.
It looks like this:
 `<link rel="alternate" type="application/rss+xml" title="My Site's RSS Feed" href="http://mysite.com/feed/" />` 

Of course, you could just hard-code the tag into your Rails layout, but that’s not a good idea. A better idea is to use the Rails’ auto_discovery_link_tag view helper to do it. The helper method docs can be found in the ActionView::Helpers::AssetTagHelper module.

What you end up with is something that looks like:

`<%= auto_discovery_link_tag(:rss, {:controller => "controller_name", :action => "feed", :title => "My Site's RSS Feed"}) %>`

See the docs for the full details.

There are other helpful methods on that module too. There’s a method for creating stylesheet links and methods for creating paths to javascript files and images.