+++
title = "What I Learned Building a Platform as a Junior Dev 👨‍💻"
date = 2025-03-19T19:26:00+01:00
lastmod = 2025-03-19
tags = ["hohle.art"]
categories = ["hohle.art"]
+++

I was not satisfied with the available options to host my art online. I wanted to host it on Artsy, but they require you to go through an already established and reputable gallery. I also figured there must be more artists like me that are looking for a cool yet professional alternative to the current available platforms.

That’s why I decided to build a new online art platform and name it hohle.art (after the caves that are of important archaeological finds dating from the upper paleolithic).

This was a great opportunity to build what I was looking for, and at the same time learn the technologies and techniques composing a modern web application. Something that could help me jump-start my art career and also either land me a job as a developer or secure projects as a contractor.

Before this project I had some experience in full-stack web development from  earlier courses, self-teaching, and an earlier employment as a junior developer, but nothing close to building a production application by myself.

Along the way I had to teach myself about systems design, e-commerce data modelling, programming languages, frameworks, API design, web standards, version control, testing, infrastructure, security and open source collaboration.

No guts, no glory!

I started out by doing extensive research about what technologies to use and how to build a scalable and robust modern web application. I say modern, because to learn while doing I choose to decouple the architecture despite being a solo project.

Django was the obvious choice for me. Its ORM, maturity, built-in security features, and — of course — the admin panels make it a powerful framework. It gives me more control over the data layer, without relying on third-party services or less battle-tested solutions. Plus, having prior Django experience helped me hit the ground running.

Then I had to choose the API layer and I was pretty sure that I didn’t want to go for the usual choice of Django Rest Framework. I had heard something about GraphQL, but that it was complex and possibly had security issues. After doing some research I found that it wasn’t that bad.

I chose to pair Django with Strawberry GraphQL because it has an active community compared to graphene.

JavaScript for the frontend, and React as a natural follow-up. I wanted the clean, maintainable codebase that comes with its component-based design, along with the ease of building interactive UIs. Because I wanted decoupled architecture I didn’t choose something like htmx – like a lot of Django folks would have chosen.

And why not Vue or Svelte? Boring answer is React just felt right and it does not have anything proving it being a bad choice in my opinion. I had some prior knowledge and it might make me biased to not choose Vue and having to learn another thing from scratch.

The biggest concern I had about the frontend was managing state.

Anyway, I did some research and at the time Next was the most popular choice, but I wasn’t convinced, instead I just tried to use React Router (this was before v7) as a client router. But I quickly realized that I would need something like redux or mobx to handle all the data being fetched from the backend, so I gave Next another look, and almost gave up on the idea of decoupling, because it felt too complex.

Enter Remix!

At first, because of my novice take on React heavy frontends, I didn’t really know what it was all about. But after a couple of hours of watching the presentations, reading through the docs, and fiddling around with the code, I realized this is exactly what I was looking for. The way it’s built on top of web standards is awesome, it gives me insight in how the web works under the hood, which is a yuge bonus.

I was presented with the term BFF – Backend For Frontend – something I could swear I heard myself asking for.

And for the record, I am not hating on Next.

Moving on.

With a BFF, I no longer have to worry about state management. I can fetch data in parallel every time, avoiding waterfalls and props drilling—such a smooth experience! Since my codebase wasn't very large, migrating from Remix to React Router v7 was simple, and that's what's currently running the frontend.

Any good platform requires user accounts, and with user accounts comes the need for authentication. Thankfully, Django has us covered with its built-in features. Since JWT is often preferred for native applications, I’m already leaning toward using a WebView app, which means I can comfortably rely on session-based authentication.

To use Django's session-based authentication, I simply pass a secure cookie to React Router, which then forwards it to the browser. The browser sends this cookie back with each request to React Router, which then forwards it to Django. This cycle repeats with each request.

Easy peasy – maybe not at first, but now I guess I could do it in my sleep.

Passing cookies back and forth from Django is possible using Graffle (formerly graphql-requests). I initially tried using urql, but it doesn’t support this out of the box, and I didn’t feel like trying to hack a solution.

Once the authentication features—like signup, login, logout, password change, and password reset—were up and running, I moved on to designing the data layer.

This was the trickiest part, especially because of how I wanted artworks and variations to be structured together. And just to be clear, if I had known exactly how I wanted the architecture and stack to be set up from the start, I probably would have begun with the data layer.

To steal like the great artist I am, I started by drawing (read: stealing) a lot of inspiration from Saleor, and looking back, that probably wasn’t the best approach.

Instead of writing the models from scratch and making progress, I ended up spending too much time diving into Saleor’s inner workings, especially their attribute setup—ironically, while they were refactoring it. After more time than I’d care to admit, I finally got it to work, shedding most of the initial copy-pasta along the way.

But I guess the pain was worth it, because now I have a dynamic model structure that allows for artworks and artwork variations, with attributes that can be assigned to either the artwork itself or its variations.

Re-cap...

So far, I’ve got a stable backend running on Django and an almost equally stable BFF with React Router v7, which communicates with GraphQL through Strawberry GraphQL and graphql-requests/graffle. User authentication is secure and painless (so far), using session-based cookies. I’ve also set up dynamic data models and have the tools to create a highly interactive UI with React. Not bad at all!

To make things more interesting, I stumbled into the world of open-source communities while building the forms to collect user inputs. Since I already had my dynamic data layer in place, I needed some dynamic forms to add interactivity and flexibility. React Hook Form was the obvious choice, and naturally, Remix Hook Form followed, since we’re using React Router ;)

While trying to upload images with Remix Hook Form, I noticed that I was receiving “[object Object]” from the parsed data on the server. After reading through the documentation, I discovered that it stringifies all values by default before sending them with the request. This is done to avoid confusion between numbers and strings when the data is received.

So, I reached out to the Discord channel to ask if I was doing something wrong or if I needed to use another solution on top of this one. The response I got suggested using a different package for handling file uploads, but that package was focused on streaming files, which wasn’t what I needed.

So, I took a closer look at the code to figure out what was going on. I discovered that, while all values except files were serialized on the client side, on the server side, all values—files included—were being serialized again before being passed to the deserializer.

I forked the project, removed the second serialization step, and found that it worked as I had hoped. I then shared my findings in the chat and asked if I could submit a PR with the changes. I was encouraged to do so, with the promise that it would be merged if I provided tests. After a quick re-touch of the test, I submitted the PR.

The only thing left now was shipping this thing—using GitLab CI/CD.

To start off, I used AWS to spin up a Debian box and installed Caddy. Then, I realized I needed an extra layer of protection in front of it. Since I was already using Cloudflare to reverse proxy traffic to my blog, I figured I could do the same for the platform.

You can read my guide on how I did it here.

I already had some tests for both the backend and frontend, so writing a pipeline to run the tests, build the images, and then deploy the application was pretty straightforward.

I found some guides on how to use SSH and bash to deploy with zero downtime. The process basically involved logging into the server, pulling images from GitLab's repository, spinning up two new containers, using Caddy’s reload function to redirect traffic to them, and then removing the two old containers.

Now, I know it might sound like I just slapped it together over the weekend, but to get the pre-alpha ready, I actually spent a little over six months.

What did I learn in the process?

Hard work pays off xD
