# How to consume an API

Programs are not the self-contained, self-reliant, self-sufficient things they once were. In today's world, it's not considered unusual for a program to call a dozen different APIs, do it's thing with the data, and then provide the result, perhaps with it's own API. It's not just the users of the web that's connected, but now the websites themselves.

Even the self contained programs of the past are being refactored by the younger generation into several smaller code bases that share data by talking, not by memory. Instead of figuring out the age old question of how to architect a large yet maintainable and extendable code base, developers are avoiding the issue all together by keeping their code base as small as possible. Yes, APIs are consuming the world, but this isn't about that. This is about how we consume APIs.

In this article, we'll discuss how to consume an API in the most robust way possible, so you won't be summoned by your boss in the middle of the night, or return to your personal project website to find that it's been down for months. This article will not cover the how-to of OAuth, or other implementation details. Although the focus here is on APIs, I think much of this article will also be useful for consuming spreadsheets, files over FTP, or a gmail that contains ZIPs that contain TXT files with pipe delimited data.

## The Set Up

The very first step when using an API is to look for an official (or unofficial) SDK for the API you'll be using. SDKs do more than take care of HTTP boilerplate. They provide a simpler interface that requires less knowledge of the quirkier parts of the API, and often provide great error handling specific to the API. If there are no SDKs, there are still general purpose API libraries such as [Guzzle](http://docs.guzzlephp.org/en/stable/) that can make the job easier.

Most APIs provide a public key, private key, and sometimes other bits of other information that need to be plugged into a request. I recommend against hard coding this into your application. A configuration file or environment variables will make updating the keys easier and can help keep the keys out of the code repo for open source projects.

Most APIs have throttling limits. Some will be simple, such as 10,000 a day. Others will use bucket like concepts, such as a quota of 10 and a restore rate of 1 a minute. Whatever the throttle limits, use the API as little as you can get away with. This is not only to be kind to the people providing the API, but also to give you a cushion for replaying your requests to debug problems. Even if you don't expect your API to ever hit the throttling limits, design your code to gracefully recover for when it find a way to hit the throttling limits anyways.

I have found that some APIs have higher rates of failure (mostly 503s) at the top of the hour. I assume this is because people tend to set up very simple CRONs, such as `0 * * * *`, `0,30 * * * *`, and `0/15 * * * *`, all of which align at the 0th minute. This minute is rush hour (or rather rush minute) for most APIs. Because of this, I recommend avoiding it by setting your CRONs to run at more off peak times. Using odd numbers such as in `11 * * * *` work excellently, at least as long as everybody doesn't take this advice.

## Zero trust

    "Things will go wrong in any given situation, if you give them a chance."
    -- Murphy's law

Murphy's law is usually misquoted as "Anything that can go wrong, will.". The original quote, "Things will go wrong in any given situation, if you give them a chance." is much less dire, and suggests we can do something about it.

Whether you're using what you'd expect to be an extremely reliable and consistent API, or will be consuming an API you've personally built, you need to work with zero trust of what you'll be receiving. APIs are all about depending on someone else's data, from someone else's code, from someone else's server, and someone else's network. When this many things are out of your control, you need to have a healthy respect for Murphy's Law.

* A name with a new line in the middle? Yep.
* Emoji in a street address? Sure.
* `&nbsp;`, `U+0009`, and mojibake? Oh yeah.
* Does this API consider Guam a state or a country? Why not both?
* That currency type field that should always be USD coming in as CAD? Of course.
* Invisible control characters copied from a PDF that cause you hours of debugging? You betcha.

These are just a _small_ sample of real situations I've dealt with. Every piece of data should be treated with a high level of skepticism. When an unexpected response is received, you'll need to be prepared to take the appropriate action. `&nbsp`; might need to be converted to space, control characters might need to be stripped out completely, unexpected currency types might need to trigger an alert, and a dirty if statement (or an elegant look up table) might be needed for when Guam is sent in as a country.

An excellent resource for planning for and testing against certain sorts of insanity is [The Big List Of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings). This list focuses on UTF strangeness and malicious injections. You might want to make a list of other unexpected values you might expect.

## Monitoring

I highly recommend you log every HTTP request you send and every HTTP response you receive. In addition to being essential for debugging unexpected responses, this will be important for when the API is broken, understanding why it is broken, and proving it to the developers of the API. When an API developer receives a complaint that their API isn't working, they begin with the assumption that you are doing something wrong (because you probably are). Don't show or mention your code to them. Keep the discussion focused on HTTP. Provide the HTTP request sent, the HTTP response received, and explain why the API is not behaving as documented.

    When an API developer receives a complaint that their API isn't working, they begin with the assumption that you are doing something wrong (because you probably are)

Catching issues and logging them is the easy part. Deciding how to be actively and effectively be alerted of which issues is more difficult. Understanding which errors your software will gracefully recover from on it's own, and which require immediate attention is difficult to know beforehand. That's going to be specific to the API and your implementation. When you do have an idea of severity and a way to filter them, you'll need a alert system. Email is definitely the most popular option, and for good reason. It's simple and when set up correctly works great. Some may prefer a text message system for things that require more immediate attention, or even a centralized dashboard that is actively monitored for larger teams. I do advise that you try to only report on errors that require manual action from the recipient. Our goal is fight against the forces of apathy and complacency.

But if we really want to know if the system is working, we can do better than trying to understand and parse the generated errors. I recommend building a script that watches your system from the outside. Test what "working" means for your system, whatever they may be. Are you expecting a database to receive new data. Take its pulse and make sure it is. Are you sending requests to the API change the status of an order. Follow up on it with an API call a few minutes afterwards to check that the status actually changed.

In addition to the usual suspects of unit testing, integration testing, and mock testing (of which I highly recommend you make use of [The Big List Of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings) mentioned above), I recommend you consider using contract testing. Where the previously mentioned tests test your code, a contract test tests the API itself. This is useful not only for checking if their API is outright broken, but also listening for more subtle changes, such as new fields made available.

I also recommend making use of a service like [Down Detector](https://downdetector.com/). I've found spikes in issues reported for a service correlate extremely closely with issues I have with the API, and helps give me peace of mind that I'm not alone.

Perhaps the single most important piece of advice, is to find an official (or unofficial) forum dedicated to the discussion of the API. Even if the community is only a handful of people, it will be incredibly useful so you can know if anybody else is having issues with an API as well, or common workarounds to the known bugs and quirks. Finally, periodically check up for announcements on changes, depreciations, or new additions to the API.

## In Conclusion

Building something until it works and forgetting about it is rarely an option when depending on an API. You'll need a robust system that works on zero trust, the ability to listen for and react to issues, and active monitoring and diligence. It might be work, but I promise you it's less work than acquiring a billion users, building a video library of petabytes, or mapping the earth.

## Picture Idea

For the picture, maybe a messy looking hare eating an "API" spaghetti, elbows on table, slurping a long noodle, with two stuffy looking hare's looking on disapprovingly

## Author Info

    Author:"Goose" Hare
    Species:H.A.R.E. (as "Hairless And Ranting Engineer")
    Job Title:Indecisive Web Developer
    Hobbies:Moving brackets to new lines, then back again

'Goose' Hare is a Web Developer than likes things to be a certain way. He just hasn't figured out what that certain way is yet, but he'll know when he sees it. Despite his continual search for perfection, he is incredibly pragmatic, especially when he's short on time. While most of his opinions change frequently, he'd like to share some of his more enduring observations.
