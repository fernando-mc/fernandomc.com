+++
Tags = ["Hugo","Static Site","Brew"]
Description = "How to brew install legacy versions of the Hugo static site generator."
draft = false
publishdate = "2017-09-28T10:51:05-07:00"
date = "2017-09-28T10:51:05-07:00"
title = "Using Legecy Versions of the Hugo Static Site Generator"
menu = "main"
Categories = ["AWS"]
[image]
	feature = "/images/brew_hugo_post/hugo.png"
	credit = "@spf13"
	creditlink = "https://discourse.gohugo.io/t/hugo-logo-terms-of-use/3422/5"
+++

While upgrading [Hugo](https://gohugo.io/) (the static site generator I use to make this blog), I noticed a few errors surfacing:

```bash
WARNING: calling IsSet with unsupported type "invalid" (<nil>) will always return false.
```

After looking around for this I got a hit:


![A photo of a Github issue](/images/brew_hugo_post/small-world.png)

Amusingly, a coworker had asked this question a few weeks earlier and discovered the error was related to deprecated functionality in a new version of Hugo that my current theme still relied on.

While frustrating, I probably should have expected I would be running into errors by blindly upgrading. So the new problem was that I had to downgrade my version of Hugo, which I had just installed with brew. 

Hugo doesn't currently use semantic versioning with a major.minor.patch structure because the maintainers say it is still not ready for version 1 given frequent changes.

They're [somewhat adamant](https://github.com/gohugoio/hugo/issues/3782) on this front when asked if they'll consider changing to it.

![A sassy GitHub response](/images/brew_hugo_post/sassy.png)

So I had to figure out how to get an older release for my theme that was incompatible with Initially I tried searching for older released versions of hugo within brew with `brew search hugo`:


![Searching hugo for an older version](/images/brew_hugo_post/brew_search_hugo.png)

No luck.

Then I tried to see if there was an official way to downgrade my version. There are [release tags](https://github.com/gohugoio/hugo/releases/tag/v0.18) on GitHub for the official Hugo repo which I downloaded but wasn't sure how to install these properly as I had previously installed Hugo with brew.

So after a little digging I found out you can install with brew from older versions of homebrew-core package urls. You can track down the version you want and install it yourself by doing the following:

1. Search for the package you want in homebrew-core. In my case [Hugo](https://github.com/Homebrew/homebrew-core/search?utf8=%E2%9C%93&q=hugo&type=)

2. Then click the 'history' button

3. Scroll down to find the version you want

4. Press the angle brackets button to view the file at that point in time. `< >`

5. View the raw file

6. Copy that url and use it with `brew install`

For example, this is what I'm currently using to brew install version v0.18.1 of Hugo:

```
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/d3c4aadfd067534a723f9cf2e5c5bec444d0579d/Formula/hugo.rb
```

After that you'll need to be careful not to accidentally upgrade your version when doing brew updates. Assuming you've switched to the version you want, the easiest way to do this is to pin that version with `brew pin hugo`.

Hopefully other folks using Hugo or other outdated brews will find this useful!