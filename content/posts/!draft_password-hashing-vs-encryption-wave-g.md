+++
draft = true
Description = ""
Tags = [
  "Security",
  "Wave G",
  "Go Wave G",
  "Passwords",
  "Password Hashing",
]
Categories = [
  "AWS",
]
title = "Wave G Password Practices Fail"
menu = "main"
publishdate = ""
date = ""

+++

0. GET SOME IMAGES in (Feedly, RSS, etc use them and need em in there at first publish)
1. Outline post
2. Write Post
3. Upload and add tags and categories
4. Check publish date and date
5. Add all the other front matter
6. Check summary

Over the last few months I've tried to reach someone at Wave G about concerns I have regarding their password storage practices. Namely, that they store them in a form that makes them vulnerable to being exposed in the case of a data breach.

After calling, emailing and speaking with Wave G folks multiple times and repeatedly making myself available to describe my concerns to anyone on their development or security teams I recieved an email essentially telling me they're not concerned with the issue. I think that's a problem and here's why.

**What's the Problem?**

When I signed up for a Wave G account I was looking forward to a smaller more regional provider that was an alternative to Comcast. Calling them to get service installed was easy enough and was relatively simple.

The issues started when I went to create an online account through the Wave G customer portal. The first thing that was a red flag was that they decided to disable pasting passwords in the password fields during the account creation process. Other folks have [written about this](ADD LINK TO TROY HUNT POST ON THIS) before and why it's such a bad idea. Essentially it prevents people from using password managers to securely create and store passwords that are unique from any other service they use. Disabling paste on sign up forms means that users are more likely to use the same password as another service they already use (because it is easier to remember). 

Now this alone is probably enough  to make me complain to Wave G on Twitter. But I went ahead and completed the sign up process by stubbornly hand-typing in my 25-character randomized password that I stored in a password manager.

I thought that this would be the end of it. I would basically setup an auto-pay for my account and never think about internet again unless the service went out or I had to move.

Unfortunately, Wave G had more fun surprises in store for me. After creating my account I recieved a "protected PDF" via email. The email instructed me to use the same password I had provided during signup to access the PDF.

Now this initially might not seem like a big deal to some folks. 

"So what? They sent you a more secure statement by adding PDF protection. Great for them!"

Unfortunately, the fact that they were able to add password protection to the PDF with that same passord indicates to me they have some poor security practices when it comes to passwords. But let me explain a little more.

Let's start by adding some context. Passwords are some extreemely sensitive information. If you know someone's password you can frequently access some of the most intimate details of their life. Things ranging from personal email correspondence to finance and banking information. 

Now when a responsible developer asks you to set a password on their website they need to take some precautions. Because passwords are so sensitive the developers ideally don't want to store your passwords at all! At a minimum they take the password you provide and "hash" it - or put it through a one-way procedure that consistently produces the same output. As an example, the password "Ilikejuice" might go through a hashing algorithm and turn into something like "651r2euioijasdbaf1265asw1273tasdf". (REPLACE WITH ACTUAL HASH)

These hashing algorithms use some clever math and programming to make this process non-reversible but consitently reproducible. In other words:

- I cant feasibly take "651r2euioijasdbaf1265asw1273tasdf" and turn it back into "Ilikejuice" 
- I can garuntee that "Ilikejuice" will always have a hash of "651r2euioijasdbaf1265asw1273tasdf"  

This allows the developer to do two things:

1. Never store your password anywhere in the actual form you entered it "Ilikejuice"
2. Store and use the result of the hash to verify you've entered the correct password

Now there's other precautions like [adding a 'salt'](LINK ABOUT SALTS) or using [many iterations](LINK ABOUT MULTIPLE ROUNDS OF HASH) of the hashing algorithm and also using a [strong hashing algorithm](LINK ABOUT BCRYPT). But hashing is one of the most basic precautions. And Wave G, an *internet service provider*, doesn't use this basic security step.

Ok. Ok. I'll slow down here. You might be asking one of two questions now.

1. What the heck does this guy know compared to an ISP?
2. Or maybe I convinced on the best practices and you're asking how I _know_ they aren't doing this.

The best help I can give you on the first part is to point you to a [lot](TWITTER?) of [other people](TROY HUNT) and [government agencies](NIST LINK) who will tell you the same thing.

And how do I know they're not taking these precautions? Well, two reasons. 

The first reason is that they used my password to add PDF protection. The only way they could have done this is by actually _having my password_ stored in their systems.

Second, [they told me](https://faq.wavehome.com/hc/en-us/articles/115002786213-How-is-my-password-safe-in-your-system-if-I-use-the-same-password-for-logging-into-my-Online-Account-Manager-and-to-open-my-Statement-when-it-is-emailed-to-me-). Hidden in the "we blah blah blah security" is the fact that they store my password not _hashed_ (the industry standard practice) but _encrypted_. Now this might seem like a silly thing - "what's the big deal Fernando? 'Encrypted' Sounds pretty safe to me!". And sure, encryption is a great security tool to use - in the right scenarios it helps protect a variety of data. Unfortunately when you hear 'encryption' in the 'enrypted passwords' context you have to realize that it also means 'you can see what this says if you have the right key'. Now for lots of kinds of data like social security numbers, financial records and health information you can't hash it because the data itself needs to be viewed and used in a way that is usable by people.

Passwords are different. You don't need passwords to ever be stored in a system in a way they could ever be turned back into the password itself. And that's a good thing. Everytime there's a password data breach it's a big deal because it means that all those people who didn't use a unique enough password are at risk of having their _other_ accounts breached. 

It's furstrating to spend so much time trying to help Wave G understand my concerns and address the issue and see no real movement towards a fix. My most recent interaction with Wave G before they linked me to the article about how they handled passwords was speaking with a Wave G repreesentative on the phone who knew what password hashing was and why it was important. He said he would to bring it to the attention of relevant people internally. But unfortunately someone higher up must have said "nah, the security of our customers isn't a priority." 
