+++
menu = "main"
date = "2016-01-18T00:50:09-05:00"
title = "Good, Bad or Ugly? – Theoretical Security in Password Creation Systems"
Description = ""
Tags = [
  "Passwords",
  "Diceware"
]
Categories = [
  "Security"
]

+++
Recently I’ve paid more attention to recommendations for memorized keys. Should I always use a number in my password? Is it really more secure to add that special character at the end every time? What are some real best practices when you’re telling people to create strong, memorable passwords? First, I’ll make some assumptions to help think about this.

1. I’m only talking about the passwords you absolutely must memorize – Otherwise we would all be better off using a [password](http://keepass.info/) [manager](http://keepass.org/) of some sort.

2. The Attacker Is Only Attacking My Password – A secure system is only as strong as the [weakest link](weakest link). For this analysis I’ll assume the attacker is trying to attack my password without using a [$5 wrench](https://xkcd.com/538/) on my tibia. I’ll also make the (naive) assumption that my system is completely secure: No 0-days hiding in the closet to grab root on my computer, no malware pre-installed on my system, and no shoulder-surfers near my keyboard. My only danger is a brute-force or smart-brute-guessing of some kind.

3. Attackers Know My ‘System’ – When cryptographers publish strong algorithms the details are [completely](http://www.moserware.com/2009/09/stick-figure-guide-to-advanced.html) [transparent](http://csrc.nist.gov/publications/fips/fips197/fips-197.pdf). They want to be sure that even with intimate knowledge of the details no one will be breaking  the encryption. In order to do something similar (kinda), I’ll assume that the attackers who want my password know the details about the system that I use to create it. For a bad example – if my advocated ‘system’ is to tell people to think of their favorite book and then add punctuation and their birth year to the end I might get something like this: TheTwoTowers%1990. I’ll be assuming attackers know this ‘formula’ and will design specifically to attack it.

4. The Attacker Makes [One Trillion Guesses Per Second](https://www.reddit.com/r/Thenewsroom/comments/2lvjeh/assume_theyre_capable_of_three_trillion_guesses/) – This is an estimate for offline cracking and assumes that the attacker is using many well configured machines and that the hashing algorithm used on our password is terrible. This also means that each year, rounded down, the attacker can make about thirty quintillion guesses (30 * 10^18). Let’s say our acceptable risk of guessing the password is a ten percent chance over the course of one hundred years. This means our total pool of possibilities should be a thousand times what our attacker can guess in that time. So we need a total possibility pool of thirty sextillion possibilities (30 * 10^21) for reasonable security.

I’ve collected a few methods that claim to be secure and tried to compare some of them in terms relative to their overall possibilities and the number we’ve concluded is okay enough for us. In reality we might want to remain secure for our lifetimes to a higher degree of certainty than 90% .

___
**Example 1 – Books, Punctuation, and Birth Year**

To get started lets use my above example, mostly because it is easy to break it down into pieces:

- The Book Title
- A Special Character
- The Birth Year

The book title is relatively easy to put a number on in terms of combinations. First, let’s be realistic and say that people are not equally likely to use any of the 129M published books. There is a smaller subset of books that anyone might have heard of, let alone choose. I doubt I even know the names of a thousand books off the top of my head. But, just to be very comprehensive of reading preferences, languages, popularity, and other factors let’s make the incredibly generous assumption that for a given person we can narrow down the set of books they might know the titles of to 10M.

Lets also multiply that number by spacing, misspelling, and capitalization choices of the book or other word mangling oddities that someone might use. Let’s still be generous and say that there are 10,000 of these.

This gives us 100bn possibilities for the book section.

For the single punctuation mark or special character lets include some wonky and non-standard keyboard characters and say an even 100.

Given the amount of information out there, a birth year is trivial to find. But even without that information you can realistically guess someone’s age within ten years quite easily.

So. We’ve got the following for numbers of possibilities:

- The Book Titles and their Manglings – 100bn
- The Special Character – 100
- The Birth Year – 10

Total possible combinations – **100 Trillion**.

So we can see from this that we’ve capped out around a minute and forty seconds of time required to absolutely be able to crack our password.

**Conclusion – BAD**

___

**Example 2 – The ‘Schneier’ Method**

In 2008 Bruce Schneier [suggested](https://www.schneier.com/essays/archives/2008/11/passwords_are_not_br.html) the following for creating strong passwords:

“My advice is to take a sentence and turn it into a password. Something like “This little piggy went to market” might become “tlpWENT2m”. That nine-character password won’t be in anyone’s dictionary. Of course, don’t use this one, because I’ve written about it. Choose your own sentence – something personal.”

He also [followed up](https://www.schneier.com/blog/archives/2014/03/choosing_secure_1.html) on these recommendations in 2014 saying that this method was preferable to Diceware, a password generation method [featured](https://xkcd.com/936/) by XKCD.

This method is particularly interesting in that it is difficult to quantify the level of security it provides. Because of this I’ll analyze a few of the possible ways it could be used independently but still be forced to use a variety of assumptions.

_Test A: Iconic Phrases_

In this instance I’m talking about phrases like “A penny saved is a penny earned” and “Life is a box of chocolates, Forrest. You never know what you’re gonna get.” Drawing from my earlier estimation that there are about 10M books someone might know I would add in three million potential songs, movies, speeches, and other media that might be used. This gets us 13M potential sources overall.

But wait, are these ‘Iconic’? No, probably not. Let’s say on average there is one iconic phrase for every one thousand sources. This leaves us with 13,000 iconic phrase possibilities.

Let’s also say that for each of these phrases there are 100,000 unique ways to mangle each phrase. We’re increasing this from our previous estimation because phrases are, on average, more complicated than book titles.

This leaves us with a total possibility pool of 1.3bn.

This gives us the total time to calculate of less than a second.

**Conclusion – BAD**

_Test B: All Easily Memorable Phrases_

Another possible interpretation of this password creation system is to pick a less common but still easily memorable phrase from some publication or document. This could be a book selection, a quote or a variety of other phrases that are somewhat less iconic than the above. I will use the same 13M potential source numbers as the above because these will still be appropriate for commonly used sources. This time however, I assume each source has about 1000 easily memorable phrases.

Within 13M potential phrases sources let’s estimate that there are an average of 1,000 easily memorable phrases that within the source. Let’s maintain our assumption that we can do 100,000 unique manglings of these phrases.

13M * 1000 * 100000 = 1.3 * 10 ^ 15

Better, but this still only buys us 1300 seconds (21 minutes) at 1 trillion guesses per second.

**Conclusion – BAD**

_Test C: All Written Down English Phrases_

For this example we will be very permissive with our numbers and mistakenly assume that all books have been translated into English. This gives us 129M potential books which I will round-up to 130M. I’ll top that off with an overestimate of the number of other, non-redundant phrase sources at 20M. We’ll then also say that each of these sources is at least a extra-full length novel of some 500,000 words (keep in mind we’re counting things like redundant 2 minute songs in as the same length as full novels so this is fairly generous). This gets us to 150M 500,000-word sources.

We’ll also say that our sources have a lot to say, but they have concise phrasing and therefore a shorter average length of 10 words per phrase (below the real average of 15-20 words).

With this we can calculate the total number of written english phrases:

( 150M sources * 500000 ) / 10 words = 7.5 * 10^12

Or 7.5 Trillion phrases

Multiply this by the 100,000 unique manglings and we get 7.5 * 10 ^ 17.

This amount of phrases and manglings gives us 750000 seconds (about 8.68 days) until our password is 100% guessed.

**Conclusion – BAD**

A (Massive) Side Note
These calculations will vary wildly depending how generous you are with the unique mangling types. Every order of 10 that you assume mangling differences is a substantial increase in difficulty. That said, what do we need to assume to get this to reasonable security (defined above by 30 * 10^21)?

This happens when:
30 * 10^21 = total phrases * total manglings

Since total phrases = 7.5 trillion

total manglings will need to be = (30 * 10^21) / 7.5 trillion

so total manglings = 4,000,000,000

So we would need to increase our total number of manglings per phrase to 4 billion. Now it might be possible mangle each phrase 4 billion ways, but that seems like a little bit of a stretch. Even if you’re doings things like adding in punctuation and capitalization differences.

Overall Conclusion – UGLY and probably BAD but no way to know for sure given the ambiguity of the method.

Let’s try something easier.

___

**Example 3 – Diceware**
Diceware is a password creation system that forms a password using dice to select words at random from a lengthy word list. A longer and more detailed explanation can be found [here](https://theintercept.com/2015/03/26/passphrases-can-memorize-attackers-cant-guess/) but here’s the basics:

When I say ‘random’ I mean really random. We choose these words from the list using dice or an equally random input and certainly not ‘random’ words you think of like ‘phone’, ‘table’, ‘window’, ‘chair’ which all happened to be right next to you while reading this.

A sample Diceware wordlist contains 7776 words. So, for every truly random selection from this list the number of possibilities (n) will equal 7776^n. If you include 5 words chosen at random from your list to form something like “bolt vat frisky fob land” you will already have about 28 quintillion (28 * 10^18) possibilities easily matching our best-case scenarios for the Schneier method.

“But… whaaaat? How was that so easy?”

Well, 7776 ^ 5 is about 28 * 10^18. In fact, if we add one extra word (7776 ^ 6) we get about 2.2 * 10^23 possibilities. At this rate it would take around 6976 years to exhaust all the possibilities. Or just 697 to get a 10% chance of guessing our password.

That’s great! This lets us easily achieve a theoretical security we’re comfortable with according to our above requirements.

**Conclusion – GOOD**

_Results:_

I like easy, crisp math so I’m a little biased towards Diceware from the outset because I can easily verify my calculations about its security. The ‘Schneier’ Method might have a reasonable amount of security too, but given the difficulties verifying its strength and the proven strength of Diceware, why take the risk?

Do you have a password ‘scheme’ you think holds up better than Diceware? Well then feel free to describe it to me in [140 characters or less](https://www.twitter.com/fmc_sea) and I’ll be happy to take a crack at calculating its theoretical security! I’ll only try if you aren’t asking me to remember anything that looks like this: “HurwB-V3XS`,=:{pR.i1cLlMIg”
