+++

title = "Building Pocketlink"
description = "Things I learned building a startup catered around influencers, and quick e-commerce."
date = 2025-04-25




[extra]
banner = ""
toc = true
toc_inline = true
toc_ordered = true
featured = false

[extra.comments]
# Long thread with image
#
# host = "mastodon.social"
# user = "brownpau"
# id = "104529877688537579"
#
# Thread with multiple images per post
#
# host = "mastodon.blaede.family"
# user = "cassidy"
# id = "112774854109302186"
#
# Thread with preview cards
# host = "mastodon.blaede.family"
# user = "cassidy"
# id = "110669429936617026"
#
# Post on GoToSocial
#
# host = "alpha.polymaths.social"
# user = "orbitalmartian"
# id = "01J7ETKJ19FGBDQGS1ZWZ3KEPP"
#
# Post on Sharkey
#
# host = "is-a.wyvern.rip"
# user = "volpeon"
# id = "9qy755nsnu2c0hbc"
host = ""
user = ""
id = ""
+++

<!-- {% alert(tip=true) %}
Recommended banner dimensions are 2:1 aspect ratio and 1920x960 resolution.  
Other sizes will also work, but will be cut off at the bottom/won't be high enough.
{% end %} -->


<!-- <figure> -->

<!-- ![The Office](the-office.webp)
<figcaption>The Office where Stanley works, it has yellow floor and beige walls</figcaption>
</figure> -->


## The Problem

Bento.me was one of the most popular link-in-bio tools and was on track to being acquired, leaving a gap in the market, because let's face it, Linktree has a really bad UI and not something a content creator would be proud to put in their bio. On top of that, there was also the problem of wanting an e-commerce platform that would allow selling directly within the link-in-bio, what I call optimized for sales and conversions. I had worked on customer retention for a while when building Schedrix, and what we found was that customers lose interest if the checkout process is too long with too many steps.

## The Solution

So we built Pocketlink, a link-in-bio optimized for sales and conversions, directed at content creators and anyone who wants to sell on the internet.

Pocketlink had two main features:
1. An aesthetic link-in-bio.
2. Sell and manage anything in a few quick steps.

After a few days of market research and competitor analysis, we launched — and it kind of blew up. We got close to 800 sign-ups in the first few days. This was the first time we had gotten this much traction, so we decided to drill down on the idea and considered it as validation (which, looking back, was not correct, would recommend reading *The Mom Test*, a book on validating ideas).

Important to note: we hadn't made any money yet. Everything was completely free, and we started to focus on tech and marketing. We started using Docker, Kubernetes, and Supabase Pro as we allowed users to store images, videos, and GIFs, all for free.

We started building more and more features, things like email marketing that allowed creators to run campaigns to thousands of people without ending up in the spam folder or getting blocked, optimizing loading speed, system design for handling thousands of concurrent users, in-depth analytics for creators, and so much more.

On the marketing side, we started filming videos, reached out to our circle, pure organic growth, no ads. Instagram page: https://www.instagram.com/pocketlink.co/

This went on for a few months, grinding out code, late nights fixing bugs and pushing new features — without realizing one simple thing: we still hadn't made money and were burning through cash. Supabase Pro was $25 a month, our biggest cost at the time.

Eventually, we decided to shut it down. This is often referred to as the 90-day lifespan of a startup — new startups often have roughly 90 days to build, execute, and validate. If it doesn't make money within that window, it shuts down.

{% alert(tip=true) %}
Book Recommendation: The Mom Test
{% end %}

## Lessons and Things I Would Do Differently

1. **The only validation is money.**
2. **Find the problem first, build later.** Focus on the core feature or features.
3. **The first few days and weeks must be spent purely on validating** and understanding if this is even a problem worth solving. A really good way to do this is by asking: does the user move from a negative emotion to a positive one? Does this save time or money? And most importantly, is there anyone who opens their wallet?
4. **VCs and startups make sense if you, as founders, have past achievements** or something that sets you apart. We'd love for Y Combinator to accept us someday.
5. **Prototype and push your MVP as soon as possible** once you understand the problem well enough. It's easier than ever now, use tools like Claude Code (use Next.js for web, AI has been trained really well on it), Supabase for your database, Vercel for deployments, and GitHub for storing your repo.
6. **Start building content — that's the only moat that exists** when building tech becomes easy and quick. Only those who tell good stories and have solid distribution channels win, because otherwise it's impossible to stand out or reach an audience.

{% alert(tip=true) %}
Remember: The people who often win in this space are those who keep at it for a long time. Actions are much better than overthinking your idea.
{% end %}