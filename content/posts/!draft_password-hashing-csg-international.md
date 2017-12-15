+++
Description = "A look at a company that stores 56 million user passwords - unhashed."
Tags = [
  "Security",
  "CSGI",
  "Passwords",
  "Password Hashing"
]
Categories = [
  "Security"
]
title = "56 Million Unhashed Passwords"
menu = "main"
publishdate = "2017-12-22T00:50:09-05:00"
date = "2017-12-22T00:50:09-05:00"
[image]
    feature = "/images/password_hashing_csgi/56Million.png"
    credit = "Image Credit | CSGI"

+++

Over the last few months I've tried to reach someone at my ISP about concerns I have regarding password storage practices. Namely, that they store them in a form that makes them vulnerable to being exposed in plain text in the case of a data breach. After some initial research I discovered that the issue is actually a with a vendor called CSG International and that the passwords of 56 million user accounts are currently stored improperly. 

<!--more-->

![Marketing information from CSG International claiming 56 million user accounts managed](/images/password_hashing_csgi/56Million.png)

**What's the Problem?**

When I signed up for an account with my local ISP I was looking forward to a smaller more regional provider that was an alternative to Comcast. But my concerns with them quickly started when I went to create an online account through their customer portal. The first red flag was that they disabled pasting in password fields during the account creation process. Other folks have [written about this](https://www.troyhunt.com/the-cobra-effect-that-is-disabling/) before and why it's such a bad idea. Essentially, it prevents people from using password managers to securely create and store passwords that are unique from any other service they use. Disabling paste on sign up forms means that users are more likely to use the same password as another service they already use because it is easier to remember.

Now this alone is enough to make me [complain to them on Twitter](https://twitter.com/fmc_sea/status/894696342349422592). But I went ahead and completed the signup process by stubbornly hand-typing in my 25-character randomized password that I stored in a password manager. Fortunately, they only prevent pasting in the sign-up process and I could use my pasted password while signing in. 

I thought that this would be the end of it. I would basically set up an auto-pay for my account and never think about internet again unless the service went out or I had to move.

However, after creating my account I received a "protected PDF" via email. The email instructed me to use the same password I had provided during signup to access the PDF. Now this initially might not seem like a big deal to some folks.

"So what? They sent you a more secure statement by adding PDF protection. Great for them!"

Unfortunately, the fact that they were able to add password protection to the PDF with that same password indicates to me they have some poor security practices when it comes to passwords. Confused? Let me explain. 

When a responsible developer asks you to set a password on their website they need to take some precautions. This is because passwords are exceptionally sensitive information - they allow you to access some of the most intimate details of people's lives. 

Because of this, developers don't even want to store your passwords at all! A competent developer will, at a minimum, take the password you provide and "hash" it - or put it through a one-way procedure that consistently produces the same output. As an example, the password "Ilikejuice" might go through a hashing algorithm and turn into something like `$2a$10$NUmYqTAyqpIY/SptQ9Y8yuFhs2fqAPVH1F9qDqu3tUghQz.hvuvtq`.

These hashing algorithms use some clever math and programming to make this process non-reversible but consistently reproducible. In other words:

- I can’t feasibly take `$2a$10$NUmYqTAyqpIY/SptQ9Y8yuFhs2fqAPVH1F9qDqu3tUghQz.hvuvtq` and turn it back into "Ilikejuice" 
- I can guarantee that "Ilikejuice" will always have a hash of "$2a$10$NUmYqTAyqpIY/SptQ9Y8yuFhs2fqAPVH1F9qDqu3tUghQz.hvuvtq"  

This allows the developer to do two things:

1. Never store your actual password anywhere
2. Store and use the result of the hash to verify you've entered the correct password

Now this sounds kinda complicated. But guess what, here's one way you could do this with a single line of code:

![Hash example](/images/password_hashing_csgi/hash.png)

Now there's other precautions like [adding a 'salt'](https://en.wikipedia.org/wiki/Salt_(cryptography)) or using [many iterations](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet) of the hashing algorithm and also using a [strong hashing algorithm](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet#Leverage_an_adaptive_one-way_function). But hashing is one of the most basic precautions. Unfortunately the vendor that my *internet service provider* selected doesn't use this basic security step.

Ok. Ok. I'll slow down here. You might be asking one of two questions now.

1. What the heck does this guy know compared to an ISP?
2. Or maybe I convinced you on the best practices and you're asking how I _know_ they aren't doing this.

The best help I can give you on the first part is to point you to [experts](https://www.troyhunt.com/our-password-hashing-has-no-clothes/) and [government agencies](https://pages.nist.gov/800-63-3/sp800-63b.html) who will tell you the same thing.

As for how I know they're not taking these precautions? Well, two reasons: 

The first reason is that they used my password to add PDF protection. The only way they could have done this is by actually _having my password_ stored in their systems.

Second, [they told me](https://faq.wavehome.com/hc/en-us/articles/115002786213-How-is-my-password-safe-in-your-system-if-I-use-the-same-password-for-logging-into-my-Online-Account-Manager-and-to-open-my-Statement-when-it-is-emailed-to-me-).

They're actually pretty up-front with the fact that they store my password not _hashed_ (the industry standard practice) but _encrypted_. 

**"What's the big deal Fernando? 'Encrypted' Sounds pretty safe to me!"**

Sure, encryption is a great security tool to use - in the right scenarios it helps protect a variety of data. Unfortunately, the 'encrypt' in the 'encrypted  passwords' means 'you can see what this says if you have the right key'. Now for data that needs to be viewed in its original form like social security numbers, financial records and health information, encryption is great. This is because you can't hash it - the data itself needs to be viewed and used in a way that is usable and `$2a$10$NUmYqTAyqpIY/SptQ9Y8yuFhs2fqAPVH1F9qDqu3tUghQz.hvuvtq` looks as much like your social security number as it does mine.

Passwords are different. *Passwords should only ever be stored as a hash*. Every time that there is a password data breach it is a big deal. It means that all the people who didn't use a unique enough password are at risk of having their _other_ accounts breached. 

**So how many people's passwords are stored incorrectly?**

Initially, I thought this was just a mistake made by my ISP. But after some basic research it looks like it is actually the poor practice of a vendor - CSG International. 

My ISP told me that they were working with a vendor they had asked to fix this and a WHOIS search on the base domain of my billing portal: convergentcare.com has nameservers using the CSGSYSTEMS.COM domain which redirects to csgi.com. Also, all the subdomains of convergentcare.com that I reviewed (and there are [many](https://www.google.com/search?q=convergentcare.com) of these) have code like this in them:

![Code showing copyright to CSGI](/images/password_hashing_csgi/csg-copyright.png)

So I think it is safe to say that CSGI is the culprit here. From my discussions with my ISP it sounds like CSGI is aware of the issue and making steps towards resolving it. But until they do the combination of preventing users from easly setting unique passwords and storing these passwords improerly is unsettling. 

I hope that CSGI will prioritize these changes to their software as they are serious issues that have the potential to impact their 56 million users. 

I reached out to multiple people with CSGI for comment last week and will update when hear back from anyone there.