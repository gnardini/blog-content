# Dealing with indexing bots

On my website ARAM Zone I index around 700k League of Legends games per day. This includes both games from the latest patch to show up to date stats and player matches used to show users their match history.

Last night I noticed unusual activity. There were a large number of player games being indexed that did not match the active number of active users. At first I figured it was one user requesting hundreds of player profiles, but it all came from different UIDs (a unique identifier I store in a cookie). 

Maybe they were just deleting cookies or doing so from an incognito tab? But then my analytics would show a much higher number of sessions! In fact, the users making these requests were not showing up in my analytics at all! 

Aha, it’s a bot! I start logging the user agent and immediately see Bytedance, Bing and a few others spamming the user profile endpoint, which triggers indexing for the player games if they are not already stored. This was slowing down indexing of other kind of games and not adding much value, so it was a problem!

My first instinct was to just redirect to the home page if a bot is the one querying the player page but even though they’re not my SEO focus I’d rather those pages be indexed by search engines. So what I finally did is instead of automatically kicking off game indexing for bots, return an empty page if there are no games already stored.

Everything’s good now! Crawlers are still crawling but not taking up my game indexing quota anymore :)

One concern is that I have a self-maintained list of bot crawlers which most likely is not exhaustive. Wonder what other people do to keep this list up to date 🤔