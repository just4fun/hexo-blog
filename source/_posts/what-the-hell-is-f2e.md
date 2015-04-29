title: What the hell is F2E
date: 2015-04-29 17:30:19
tags:
- f2e

---

As a F2E (Front End Engineer), there is a big headache that how should I explain to others what is F2E or what F2E do.

<!-- more -->

------

In general, many people consider F2E the similar role like UI/UX, they also think F2E should belong to the Design Department, not the Develop Department. In their opinions, the biggest task for F2E is transform `.psd` file which produced by UI/UX to `.html` file, and our weapons are only html and css. A little javascript at most.

For instance, below is conversation between I and some guys in other departments when I decided to resign from my previous company.

*-- Where will you go?
-- Somewhere I can do the interesting things about front end.
-- So you're the F2E here!
-- But what I did is just a small part of front end.
-- So...what F2E should do?
-- Ummm...*

At that time, I always don't know how to explain. Of course I won't tell them such as MVC/MVVM or modularization.

------

Even for some F2Es, they also think the main task is build web pages with html5 and css3. As for javascript, it is just used for form validation, ajax, or building sliders.

Today, there are still many companys only use jQuery in their projects. They use JSP or ASP.NET to render page, then use jQuery to do some UI changes. All the javascript code are in the global namespace, even in tempaltes. In addition, if they want to upgrade jQuery, they should download it from jQuery official site, and then put it into repo.

------

So, what the hell is F2E?

When decide to build a web application(context of use aside), from F2E's perspective, we could do as below.

- use RequireJS/Browserify/Webpack for modularization.
- use npm/bower for version control of libraries.
- use Grunt/Gulp for building automating tasks.
- select appropriate framework/libraries
- abstract reusable components
- write integration/unit tests
- optimize performance
- ensure compatibility
- etc.

We can move most logics from backend to front end based on these advanced tools and modern browsers, and backend can only focus on APIs.

------

Nowadays, you can write any application with javascript, not only web application. You can write backend with Node, build desktop application with node-webkit, even build native app with react-native.

Are you ready to write javascript?