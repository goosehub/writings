# How to consume an API

Programs are not the self-contained, self-reliant, self-sufficient things they once were. In todays world, it's not considered unusual for a program to call a dozen different APIs, doing it's thing with the data, and then providing the result, perhaps in it's own API. It's not just the users of the web that's interconnected, but now the websites themselves.

Even the self contained programs of the past are being refactored by the younger generation into several smaller code bases that share data by talking, not by memory. And it's a Very Good Thing. Instead of figuring out the never ending question of how to architect a large code base, developers are avoiding the issue all together by keeping their code base as small as possible. Yes, APIs are consuming the world, but this isn't about that. This is about how we consume APIs.

In this article, we'll discuss how to consume an API in a way that won't result in you being summoned by your project owners in the middle of the night twice a week. This article will not cover the how tos of OAuth, tools, or other implementation details. I hope this article will be useful not just for consuming APIs, but also for consuming spreadsheets, files over FTP, or a gmail that contains ZIPs that contain txt files with pipe delimited data.

## Zero trust

> "Things will go wrong in any given situation, if you give them a chance."
> -- Murphy's law

Murphy's law is often misquoted as "Anything that can go wrong, will.". The original quote, "Things will go wrong in any given situation, if you give them a chance." is much less dire, and suggests we can do something about it.

Whether you're using what you'd expect to be an extremely reliable and consistent API, or will be consuming an API you've personally built, you need to work with zero trust of what you'll be receiving. APIs are all about depending on someone elses data, from someone else code, from someone elses server, and someone elses network. When this many things are out of your control, you need to have a healthy respect for Murphy's Law.

* A name with a new line in the middle? Yep.
* Emoji in a street address? Sure.
* `&nbsp;`, `U+0009`, and mojibake? Oh yeah.
* Does this API consider Guam a state or a country? Why not both?
* That currency type field that should always be USD coming in as CAD? You betcha.
* Invisible control characters copied from a PDF that cause you hours of debugging? Consider it inevitable.

These are just a _small_ sample of real situations I've dealt with. Every piece of data should be treated with a high level of skepticism. _When_ an unexpected response is received, you'll need to be prepared to take the appropriate action. `&nbsp`; might need to be converted to space, control characters might need to be stripped out completely, unexpected currency types might need to trigger an alert, and a dirty if statement (or an elegant look up table) might be needed for when Guam is sent in as a country.

An excellent resource for planning for and testing against certain sorts of insanity is [The Big List Of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings). This list focuses on UTF strangeness and malicious injections. You might want to make a list of other unexpected values you might expect.

## The Set Up

The very first step when using an API is to look for an official (or unofficial) SDK for the API you'll be using. SDKs do more than take care of HTTP boiler plate. They provide a simplier interface that requires less knowledge of the quirkier parts of the API, and often provide great error handling specific to the errors the API respond with. If there are no SDKs, there are still general purpose API libraries such as [Guzzle](http://docs.guzzlephp.org/en/stable/) that can make your life a little easier.

Most APIs provide a public key, private key, and sometimes other bits of other information that need to be plugged into a request. The specific lingo used is often different, but the concepts are usually the same. I recommend against hard coding this into your application. A configuration file (or even better, Environment Variables) will make updating the keys easier and can help keep the keys out of the code repo for open source projects.

Most APIs have throttling limits. Some will be simple, such as 10,000 a day. Others will use bucket like concepts, such as a quota of 10 and a restore rate of 1 a minute. I recommend using any API as little as you can get away with. This is not only to be kind to the people providing the API, but also to give you a cushion for replaying your requests to debug problems. Even so, you should also design your code to gracefully recover for when it does hit against the throttling limit.

I have found that some APIs have higher rates of failure at the top of the hour. I assume this is because people tend to set up very simple CRONs, such as `0 * * * *`, `0,30 * * * *`, and `0/15 * * * *`, all of which align at the 0th minute. The top of the hour is rush hour, ern, minute, for any API. Because of this, I recommend avoding it by setting your CRONs to run at more off peak times. Using odd numbers such as in `11 * * * *` work excelently, at least as long as everybody doesn't take this advice.

## Monitoring

I highly recommend you log every HTTP request you send and every HTTP response you receive. In addition to being essential for debugging unexpected responses, this will be important for when the API is broken, understanding why it is broken, and proving it to the developers of the API. When an API developer receies a complaint that their API isn't working, they begin with the assumption that you are doing something wrong (because you probably are). Don't show or mention your code to them. Keep the discussion focused on HTTP. Provide the HTTP request sent, the HTTP response received, and explain why the API is not behaving as documented.

Catching issues and logging them is the easy part. Deciding how to be actively and effectively be alerted of which issues is more difficult. Understanding which errors your software will gracefully recover from on it's own, and which require immediate attention is difficult to know before hand. That's going to be specific to the API and your implementation. When you do have an idea of severity and a way to filter them, you'll need a alert system. Email is definitely the most popular option, and for good reason. It's simple and when set up correctly works great. Some may prefer a text message system for things that require more immediate attention, or even a centralized dashboard that is actively monitored for larger teams. I do advise that you only report on errors that require manual action from the recipitent. If the people monitoring the alerts get use to disregarding them, it will come to bite you when you need them most.

But if we really want to know if the system is working, we can do better than trying to understand and parse the generated errors. I recommend building a script that watches your system from the outside. Test what "working" means for your system, whatever thay may be. Are you expecting a database table to receive new rows. Keep an eye on it and make sure it does. Are you sending requests to change the status of an order. Do an API call a few minutes afterwards and check that the API has actually processed your request.

In addition to the usual suspects of unit testing, intergration testing, and mock testing (of which I highly recommend you make use of [The Big List Of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings) mentioned above), I recommend you consider using contract testing. Where the previously mentioned tests test your code, a contract test tests the API itself. This is useful not only for checking if their API is outright broken, but also listening for more subtle changes, such as new fields made available.

I also recommend making use of a service like [Down Detector](https://downdetector.com/). I've found spikes in issues reported for a service correlate extremely closely with issues I have with the API, and helps give me peace of mind that I'm not alone.

Perhaps the single most important peice of advice, if it applies, is to find an official (or unofficial) forum dedicated to the discussion of the API. Even if the community is only a handful of people, it will be incredibly useful so you can know if anybody else is having issues with an API as well, or common workarounds the known bugs or quirks. It is also a good idea to keep an eye out for announcements for changes, depreciations, or new additions to the API.

## In Conclusion

Building something until it works and forgetting about it is rarely an option when depending on an API. You'll need a robust system that works on zero trust, listens for and reacts to issues, and active monitoring and diligence. It might be a lot of work, but just remember, it's easier than creating a successful social network, building a video library of petabytes, or mapping the world.

## Picture Idea

For the picture, maybe a messy looking hare eating an "API" spaghetti, elbows on table, slurping a long noodle, with two stuffy looking hare's looking on disapprovingly

## Author Info

> Author:"Goose" Hare
> Species:H.A.R.E. (as "Hairless And Ranting Engineer")
> Job Title:Indecisive Web Developer
> Hobbies:Moving brackets to new lines, then back again

"Goose" Hare is a Web Developer than likes things to be a certain way. He just hasn't figured out what that certain way is yet, but he'll know when he sees it. Dispite his continual search for perfection, he is incredibly pragmatic, especially when he's short on time.

He's always been interested in sharing his opinions on the topics he's passionate about, but worries he'll be outraged by his own musing in 6 months time.