# Project 1/10: Phinxer - Affordable and simple Web Analytics

Last year I built a web analytics tool for myself. I didn't want to pay $50/mo and I didn't want to give the data to Google, so I just made my own.

It was kinda slow and looked awful:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/phinxer-old.png" alt="Phinxer'd old UI" width="70%"> 

So since I'm doing a [10 products in 4 weeks challenge](https://gnardini.com/blog/im-starting-a-4-week-challenge) I decided to turn it into a real business by making it faster with a more adequate database (ClickHouse), a nicer UI, an easy way to add to any website and some user management features.

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/phinxer.png" alt="Phinxer'd new UI" width="70%"> 

The biggest player in the space is Google Analytics, which is free, so I can't compete on price with them, but I store no personal data and do not track anybody across sites or devices unlike them.

There are a bunch of privacy-centric alternatives, but they are all kind of pricey. They cost $60-$140 for 1M events while Pinxer is just $10 for the same, and credits don't expire every month, so you could pay $10 once and have analytics for a while depending on usage.

Website: https://phinxer.com
It's also open source! Built with Express, React, Vite, Postgres, ClickHouse: https://github.com/gnardini/analytics

## The Challenge

I spent the first 6 days of the challenge building the product and I just launched to Hacker News and Reddit and scheduled the Product Hunt launch, so this already counts as project 1/10.

I'm obviously behind, my target for the first week (only 1 day left) was two projects, so I don't think I'll make it in time for this wee. However, I built a lot of funcionalities like the build system to make sure deploying to production is easy, authentication, billing, email sending, analytics, user management, billing that all will be useful in future projects. I'm hoping that the following ones will be easier thanks to this work.

The challenge was always going to be challenging so I'm not too worried yet. This was also a more challenging project, some of the future ones will be simpler I think. I hope I'm not underestimating their difficulty too much!

Talk yo you soon!

Gonza
