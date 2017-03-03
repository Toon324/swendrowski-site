Published: 06-06-16
Title: A retrospective from 2 years of building Sites
PromoImageUrl: http://mdbootstrap.com/images/slides/slide%20(16).jpg
Category: general
Intro: Thoughts on what I have learned over 2 years, and why programming is great.
---

Just over two years ago, I built swendrowski.us for the first time. Although I had dabbled in HTML before, this was a project that I wanted to actually show people with pride, so I spent a few weeks learning the ropes to make something.

I had been working with Java as my language-of-choice since I was 14, so the technical aspects of a site were easy to learn, but the design aspects eluded me. All I had ever made in my life were command line applications, 2D game engines that used rectangles to represent things, and all sorts of eyesore monstrosities using Java Swing GUI.

![https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Gui-widgets.png/300px-Gui-widgets.png](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Gui-widgets.png/300px-Gui-widgets.png)
*I think we should make this UI popular again, it's "retro"*

I read some blogs, watched some videos, and played with some prototypes on my machine. I was amazed by how much you could make with just a little HTML and a framework. 

> **Wow,** I thought to myself. **This Bootstrap stuff is pretty cool.**

My editor of choice was [Codio](), and after a little work I figured out how to set up the editor to publish straight to my hosted server. In just a few days I had went from cobbling together HTML and manually uploading them to a process where I could edit anywhere and easily update my site. I felt like I was living in the future!

The hard work payed off and helped me land an internship at a Web Development Agency where I very quickly learned that I had only taken a small step into the world of Web Development.

<img src="http://puu.sh/usI4e/059a6804a1.png" style="width: 600px;" />
*The original site*

- - -
I've learned a lot since then. Here are the 5 things I would share with myself 2 years ago, given the chance.

1. **Unit tests are good, in moderation.** When I first started, Unit Tests seemed like a huge waste of time - why would I write code to prove that my working code was, in fact, working? I now know that Unit Tests help to ensure your code stands the test of time, but due to time constraints, they should be carefully designed to test a lot of things. 100% coverage is simply impractical on many projects.
2. **Write your code as if everything is going to try and break it.** Even API's from companies like Google and Facebook can break, so *null check, null check, and null check some more*.
3. **Clean up and Document now, not later.** It is easy to fall into the idea of "this code is ugly or pooerly documented, but I'll clean it up later". If you don't do it before you push up your code, chances are it won't happen. At the least, put a `TODO:` comment for clean up or documentation, so other developers know it needs to be dome.
4. **Write methods that are small and reusable.** The more experience I get, the smaller my methods seem to get. Having a collection of small methods that each do one thing well is far more maintainable than a giant method that does the same thing all at once.
5. **Don't be afraid to talk to people and ask questions.** It is easy to get lost in the code and just keep trying new things, especially when we are stuck on something, As a group, we seem to have trouble admitting when we are stuck instead of asking for help. Ask a team mate for their thoughts, or pose a question online, or just go find a friend who you can explain your problem to. If what you're doing seems incredibly hard, there's probably a different way, so find it!

- - -
Now it is two years later and I have replaced my old site with this hand-built blog. Compared to the first site, the description of how this site is works sounds like a wizard spouting made-up magical words:
>"The site uses a Fluently Configured engine written in C# that is curently setup to consume Markdown documents and dynamically insert them in Razor partial files, where Metadata is then used to organize and display them styled using Google's Material Design. Content is easy to update, just push up a commit on an orphaned branch and Appveyor will run the Generator code and push the updated site to Github Pages for hosting!"

*(Don't worry, if you want to expand your spellbook, I will be writing up an in-depth post on how it all works later!)*

To the casual observer, I might seem to now be a master of my craft, one who has absorbed the knowledge of my mentors and is ready to join their ranks. Yet I know I have still only just scratched the surface of what is out there, and what is possible. That is one of the the things I love most about programming - there is so much to learn, and you can always grow! It is not enough for me to be content with just being able to use these magic words, I want to delve deeper and help to write new ones. Programming represents a powerful tool, but it also enables you with the power to understand it and build your own tools, your own Bootstrap, your own Appveyor, or whatever else you dream of at night.

It seems popular to claim that programming is a dying art - we have already built what needs to be built, desgined what needs designing, and thought about all the problems that need thinking about; all that is left is for machines to take over and churn out code like a factory churns out cars. To this notion I politely disagree - the art of learning and growing is one we will never replace.


- - -
*This is a blog written by a guy who loves to talk about Programming. Thanks for reading, and I hope you return after I write some technical posts!*