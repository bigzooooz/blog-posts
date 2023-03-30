---
title: "Multiple Stored XSS"
seoTitle: "Multiple Stored XSS"
seoDescription: "This is a short writeup on how I found 4 Stored XSS in a short time."
datePublished: Thu Mar 30 2023 13:22:33 GMT+0000 (Coordinated Universal Time)
cuid: clfv5b7gu000909k1b4hp2e3a
slug: multiple-stored-xss
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1680182490519/dc2feeff-b91b-4fa0-b031-654a2def65b8.jpeg
tags: hacking, xss, bugbounty, bughunt

---

# بسم الله الرحمن الرحيم

This is a short writeup on how I found 4 Stored XSS in a short time.

I'll keep track of the bugs found by incrementing the **BUG COUNTER** as we go through the writeup

I started by selecting a program that I have an account on and use from time to time.

The website is designated for gamers teaming-up and gaming tournaments

my plan was simple, test every input field and url parameter for XSS and low-hanging fruits.

  
**BUG COUNTER = 0**

  
starting with the search feature, used a simple payload to check what to get `"><iframe%20src=x>` and that gave me a CloudFront WAF

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680114054718/3aaef2fc-c8d0-4449-8376-7070a2164354.png align="center")

and since I'm hunting for low-hanging fruits i decided to move on.

looking at the sidebar, there's a "Talk" page which is a DM section that allows me to interact with other gamers.. sweet, I guess I can test multiple payloads through the chat and see results without the need to go back and forth and reloading pages

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680114273483/0b9b43b6-0db1-4bb6-b906-d215c3696677.png align="center")

in a live chat, I tested multiple payloads such as `img` and `svg` and `script` nothing worked and seemed to be filtered until I tried `<iframe src="someRandomPathToGeta404"></iframe>`

and I see it brought up an iframe with a 404 inside the chat

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680115167023/f37852b3-b897-493c-a12e-8dbbafedf3b7.png align="center")

let's trigger an alert now!  
yet again, filters are in place and `onerror`, `onmouseover`, `onload` ..etc are filtered :/

but we know that `src` is not .. so, I ended up with this `<IFRAME SRC="javascript:window['al'+'ert']('XSS')"></IFRAME>` and that worked like a charm!!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680115521396/dc4737a5-74fe-427a-99f1-d4067c457080.png align="center")

now we have a working PoC and an unfiltered payload, time to go wild! :)

**BUG COUNTER = 1**

**SEVERITY = <mark>HIGH</mark>**

went to the homepage where it's basically a feed wall for users to post their thoughts

pasted the same payload, and we got another hit! and needs no user interaction, no chat and other nonsense. it triggers on view!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680115719650/95b76eb3-9b76-48aa-b725-d6887c217dc6.png align="center")

**BUG COUNTER = 2**

**SEVERITY = <mark>HIGH</mark>**

went to the comment section of a post, pasted the payload and boom that's another hit again!

**BUG COUNTER = 3**

**SEVERITY = TBD**

moved on and went to a tournament page and there was a comment section for participants of the tournament.

pasted the same payload and BOOM 4th XSS in a few hours.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680182005589/5d6aee26-c30d-4f5e-b7bd-202e436c1b98.png align="center")

**BUG COUNTER = 4**

**SEVERITY = TBD**

filed all reports, the severity will be updated when all reports are fully triaged

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680181960987/3ab6c846-e383-4695-83a8-8a06c7feaa39.png align="center")

conclusion: you got a working payload? go wild and test it everywhere!