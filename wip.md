# Eating an API

APIs are consuming the world. Big data made acceesible to all. Marketplaces providing access to billions. Even on our personal projects are being split up into multiple smaller, more managable projects that share information by talking, not by memory. It's hard to imagine a world where you program has to be independently sufficient. This isn't about how APIs consume the world. This is about how we consume APIs.
This article will not cover the how tos of OAuth, tools, or other implementation details. Those details will be specific to the particular API you are dealing with. This article aims to be something that will apply to any API you need to consume.

## No trust

> "Things will go wrong in any given situation, if you give them a chance."
> -- Murphy's law

Whether you're using what you'd expect to be an extremely reliable and consistent API, or will be consuming an API from something you've personally built, you need to work with zero trust of what you'll be receiving.
Definitely respect Murphy's law when depending on others.

Person with no name? Yep.
Emoji in a street address? Sure.
`&nbsp;`, `U+0009` and those question mark squares. Oh yeah.
Is Guam a state or a country? Why not both?
That currency type field being CAD instead of USD? You betcha.
Invisible control characters copied from a PDF that cause your entire application to explode? Inevitable.

There are too many examples of craziness to list here. The key take away is to not make any assumptions. Even the most basic things such as getting a formatted response at all.
An excellent resource for understanding the different kinds of crazy is the big list of naughty strings.
https://github.com/minimaxir/big-list-of-naughty-strings
Each kind of crazy reuires it's own way of handling. Sometimes you'll strip out the crazy, sometimes you'll correct to what it's suppose to be, and sometimes you need to fail.

## Throttling

Some APIs will use simple throttling concepts such as 10,000 a day. Other will use more complex throttling concepts such as buckets. An example of a throttling bucket would be when you are allowed 10 requests, with a refill rate of 1 a minute. That means you can use 10 at once, and then have to wait 10 minutes, or use 1 a minute. I always recommend running well below the throttling limit is possible. You may need to reply requests to do debugging and thorttling cushion is required for that. You should also design your app so if it does hit a thorttling limit, it recovers gracefully.
I have found that some of the more popular APIs have higher rates of failure at the top of the hour. I assume this is because almost everybody builds their crontab to hit at the top of the hour. Whether it's `0 * * * *`, `0,30 * * * *`, `0/15 * * * *`, or `0 0 * * *`. Top of the hour is a peak minute for any API. Because of this, I recommend avoding rush minute by setting your cron to run at more off peak times, such as `11 * * * *`.
Error alerting
Catching issues and logging them is the easy part. Deciding how to be actively and effectively alerted of which issues are more difficult. I've had some APIs chug along without even a single minor error for months. I've had other APIs receive every HTTP status code there is in the same time span. Understanding which errors your software will gracefully recover from on it's own, and which require immediate attention is difficult to know before hand. That's going to be specific to the API and your implementation. When you do have an idea of severity and a way to filter them, you'll need a alert system. Email is definitely the most popular option, and for good reason, as it's simple and when set up correctly, works great. Some may prefer a text message system for things that require more immediate attention, or even a centralized dashboard that is actively monitored for larger teams. I do advise that you only report on errors that require manual action from the recipitent. If you most of the alerts you receive don't require you to do anything, it becomes easy to become complacient.

If we really want to know if the system is working, we can do better than trying to understand and parse the generated errors. I recommend building a script that watches your system from the outside. Test what working means for your system, whatever thay may be. Are you expecting a database table to continue growing. Keep an eye on it and make sure it does. Are you sending requests to change the status of an order. Do an API call a few minutes afterwards and check that the system processed your request.

## Request Logging

I recommend you log every HTTP request you send and every HTTP response you receive. This will be important for when the API is broken and you need to prove it to the API developers. An API developer receiving a complaint that the API isn't working begins with the assumption that the user is doing something wrong (because they probably are). Don't show or so much as mention your code to them. Keep the discussion focused on HTTP. Provide the HTTP request sent, the HTTP response received, and explain why the API is not behaving as documented.

## Testing
Testing is important to ensure you are generating the same requests, and still correctly handling responses correctly. A happy path mock test for the happy path can be useful to ensure you're not breaking anything while working. More important, I think, is sad path testing. I recommend writing a test to trigger throttling limits, writing a test to simulate a different fail responses, a test to simulate no response at all. I also recommend using the big list of naughty strings and passing each one through each field and seeing if your application is handling it in the expected way.

However, it takes two to tango. You should not only test your code, but also test the API itself. Contract Testing is a test where you send a particular request, and check that the response you receive the expected response.
This is useful not only for checking if their API is outright broken, but also listening for more subtle changes, such as new fields made available.

## Documentation
Well written API documentation is a rare thing. Sometimes the API leaves signifigant information out. Sometimes it provides information overload. Sometimes it is out of date. Honestly, I take all the above if the API provides clear concrete examples of how to use the API. Documentation for the SDK is often more useful than the documentation for the API itself.
Backwards compatibility for APIs are not respected as much as it should be. Even if it were, keeping up with new features has allowed me to signifigantly refactor my code in the past.
It can be useful to find an official or unofficial forum dedicated to discussion of a particular API. Even if the community is only a handful of people, it will be incredibly useful so you can know if anybody else is having issues with an API as well.
I also recommend using Down Detector. I've found spikes in issues reporting correlate extremely well with errors I receive in my app, and helps give me peace of mind that I'm not alone.

> Author:"foobar" Hare
> Species:H.A.R.E. (as "Hairless And Ranting Engineer")
> Job Title:foobar
> Hobbies:foobar

"foobar" Hare...
