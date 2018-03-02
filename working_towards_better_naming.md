# Working Towards Better Naming

[https://css-tricks.com/working-towards-better-naming/](https://css-tricks.com/working-towards-better-naming/)

There is a quote that I bet every one of you has heard many times. You know the one. The one that reminds you how hard naming is.

Let’s talk names.

We talk often about how hard naming is, but it’s less common see people talk about how to get better at it. Even naming philosophies lend structure to naming, but don’t pick names for you. Names are more than just some hard thing we get needlessly caught up in. They're essential to good code. Good names can make a code base easy to pick up and work with. Bad names are hard to understand, or worse yet, prone to error.

## Naming Examples
Let’s look at some examples in JavaScript.

```
function processData(data) {
  var result = JSON.parse(data);
  return result;
}
```

Reading only the function name, parameter name, and returned variable, we see that processData gets data and returns a result. These names have added nearly zero information to the reader. Code like this is easy to write when you just want to get it working or you're trying to stay flexible to changes, and that's okay. It's always a good idea to go over your code with fresh eyes to fix things, and names should be on your quality checklist.

Here's a more descriptive way we could have written the last example:

```
function parseJson(string) {
  var jsonObject = JSON.parse(string);
  return jsonObject;
}
```

Technology is one of the most abbreviation and acronym heavy fields there are, and they are key to great names. FTP is easier to read and understand than "File Transfer Protocol." In some cases though, the reader can be left out.

Here's an example where the abbreviations are convenient for the developer writing the code, but not so handy for anyone else who needs to read or contribute to it:

```
function cts(con, ack) {
  if (con && ack) {
    return true;
  }
  else {
    return false;
  }
}
```

Often I'll read code with an acronym and have to switch to my web browser to do a search, only to find results for car models. A perfect example of that is cts, which returns a lot of Cadillac results. ack does show up on a search, but I'd rather not leave it my text editor. con can be misunderstood many ways. Is it a connection? A count? A container? Maybe it's a scam. These things may be obvious if you are familiar with the code base, but it adds a learning curve to those joining the project. A few extra seconds can save several minutes for readers over years.

Here's the previous example written without abbreviations:

```
function clearToSend(connectionExists, acknowledgementStatus) {
  if (connectionExists && acknowledgementStatus) {
    return true;
  }
  else {
    return false;
  }
}
```

Let's turn to some HTML examples because HTML is perhaps the most name heavy language of them all.

```
<section class="new-promotion-parent">
  <img class="logo" src="small-square-logo-monochrome.png"/>
  <div class="get-your-coupon">
    <p>Get Your Coupon</p>
  </div>
</section>
```

We can imagine that the word "new" was used here likely because a designer was told to update the promotion-parent element, but to also keep support for the existing class, maybe for old HTML floating about. The term "new" is an accurate description for the first few months, but over time it becomes more and more ironic. A better idea might be to add a separate class that describes what is new. For example, if a new flat design is being implemented, then a class name of flat-design might work. For an added bonus, you can let the CSS from the existing promotion-parent class cascade if you'd like to reuse some of the styles.

Similarly, logo seems like a sensible class name at first. Inevitably, however, a second logo will come along somewhere, which gets the name alt-logo. The logos keep coming, and so do the increasingly bad names. Most assets have several variations, such as different shapes, sizes, color schemes and more. That said, small-square-logo-monochrome wouldn't be a great class name either, because the image itself should be able to be swapped without the class name becoming obsolete. A better idea might be a name that describes the role of the asset rather than the type or appearance.

Here, the language of the div has been used to name the div get-your-coupon. The content of an HTML document is meant to continually evolve; names are not. A coupon code today might be an email signup in the future, while keeping the same styles and functionality. HTML is one place where names are often too specific instead of too vague.

Here's that same HTML code taking the suggestions into consideration:

```
<section class="flat-design promotion-parent">
  <img class="promotion-branding-image" src="small-square-logo-monochrome.png"/>
  <div class="primary-promotion-text">
    <p>Get Your Coupon</p>
  </div>
</section>
```

We can even look at the names in a database for better naming. Tables often get used countless times across an application in several very different contexts.

Here's a simplified database table:

```
CREATE TABLE `book` (
  `id` int(12) NOT NULL,
  `title` varchar(50) NOT NULL,
  `author` varchar(50) NOT NULL,
  `type` bit(1) NOT NULL,
  `sort` int(12) NOT NULL,
  `order` varchar(25) NOT NULL,
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

What does type mean in the context of books? Does it mean fiction or non-fiction? Does it mean paperback or hardcover? Maybe it means physical or digital?

The sort column is equally confusing. Is it representing ASC or DESC? Does it represent which column is being used to sort? Maybe it decides if sorting is active? Or maybe it decides show the books in some other alternate order?

And then there's order. Aside from being equally ambiguous, order is a reserved word in MySQL and many other languages. You can work around this using backticks (\`) for MySQL, but it's probably a better idea to avoid using reserved words altogether.

Here's how the table could be written in a more descriptive way:

```
CREATE TABLE `book` (
  `id` int(12) NOT NULL,
  `title` varchar(50) NOT NULL,
  `author` varchar(50) NOT NULL,
  `cover type` bit(1) NOT NULL,
  `sort_order` int(12) NOT NULL,
  `purchase_order_number` varchar(25) NOT NULL,
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## Naming Conventions

Let's talk naming conventions.

```
if (oldmanshaven) {
  return true;
}
```

Did you read that as Old Mans Haven or Old Man Shaven? Either way, it forces you to slow down and think which adds up and might one day lead to a misunderstanding. PascalCase, camelCase, snake_case, kebab-case are all excellent choices. Use them, and just as important, be consistent.

Here's that same code in snake_case, which is less likely to be misread:

```
if (old_man_shaven) {
  return true;
}
```

One more example:

```
if (!isNaN(number)) {
  alert('not a number')
}
else if (!number > 50) {
  alert('number too large')
}
else if (!number < 10) {
  alert('number too small')
}
else {
  // code here
}
```

I looked at some of my first lines of code for inspiration writing this post. I didn't see many bad names because I wrote code in a way that didn't use names. I used very few functions, rarely used assignments, and would abuse variables by making them do a dozen different things. Having plenty of names in your code is often a sign that you're abstracting things properly.

Here's one example from my code:

```
function validateNumber(number) {
  var maximumValue = 50;
  var minimumValue = 10;
  if (isNaN(number)) {
    alert('not a number')
    return false;
  }
  if (number > maximumValue) {
    alert('number too large')
    return false;
  }
  if (number < minimumValue) {
    alert('number too small')
    return false;
  }
}

if (validateNumber(number)) {
  // code here
}
```

## Caveats

Naming things is an art, not a science. There's some things outside the name to consider when evaluating if a name is good or bad.

## 

Context can give generic terms much more meaning. For example, "item" is a vague name but, in the context of a getCustomerSalesOrder() function, we can tell it likely refers to a product that was purchased. A function that is short, focused, and takes context into account can reduce the need for verbose names. I still prefer a good name. Context can disappear easily over time as functions get longer or re-factored. Names are more permanent markers of meaning.

## Comments

Code comments are important to readable code, but they can't do it all by themselves. Comments should pick up where names leave off, giving details you can't cram into a name, noting the reason for a particular way of doing things, or ranting about a broken library.

```
/* This refers to a product that was purchased and relates to the customer-sales-order class. */
.product-item {
  display: block;
  text-transform: uppercase;
  width: 100vw;
}
```

## Reading Length

Longer names create more to read and wider lines. It can especially be problematic when accessing deep into an array, such as:

```
$order_details['customer']['ship_to_address']['default']['street_address']['line_1']
```

That said, even that straw man example I just gave, while verbose, is crystal clear and gives me no reason to stop reading to think or look over the rest of the function to understand the context.

## Names are everywhere in Code

Most of the characters of a code file probably aren't brackets or syntax, but names. It might be variable names, function names, HTML tags or attributes, database tables or columns, or any of the countless things that we give names to in any given project.

I still struggle with naming things. I often find myself sitting frozen at my text editor, trying to name some minor variable. I've learned that when that happens, it's likely because I'm dealing with something difficult to conceptualize, which makes it all the more important to find the right name for it.
