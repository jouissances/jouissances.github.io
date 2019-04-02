---
layout: post
title:      "Flatiron Hall: Sinatra Application for Web Development Inspiration"
date:       2019-04-02 06:28:50 +0000
permalink:  flatiron_hall_sinatra_application_for_web_development_inspiration
---


On this second portfolio project post, I’ll share my process-based approach on writing Flatiron Hall from scratch, putting the CRUD functions together, designing and adding user-friendly features, and finally, deployment to Heroku. Needless to say — I faced many roadblocks during the development phase, but nothing insurmountable, and I will share them later on. Most importantly, first things first: Go visit my baby [here](https://flatiron-hall.herokuapp.com/)! **It needs a lot of love.**

Project time is always going to be my favourite part of being in Flatiron. It allows me to be creative, expressive, and to be scrappy—*don’t know how to do it?* Read the docs. Read other people’s answers to the same problem. Revisit the code, talk to it (yes), and iterate. I learned so much more from building a project than from passing lab specs, and I’m thankful for that.

## 1. Discovery
Flatiron Hall was originally Dakota’s idea. During one of my 1:1 sessions with Dakota, we were discussing about potential ideas, and he mentioned that he’s always wanted to do a web-app that puts everyone’s projects in one place. I then enthusiastically volunteered to build it, as I didn’t have any strong pull towards any particular idea at that moment. (I had wanted to make something similar to [80,000 Hours’ Problem Profiles](https://80000hours.org/problem-profiles/), but I decided that the idea hasn’t had much concreteness yet, so I’m letting it incubate for a while more—I may revisit it for my upcoming Rails project.) Even so, I knew that I wanted to create something that other people would use, and would enjoy using, and Flatiron Hall’s idea seems extremely exciting!

## 2. Strategy
To build a Sinatra app from scratch, the Corneal app generator is available on the open-source. This gem will take care of the skeletal MVC structures (with all the scaffold directory), and everything you need to load your basic development environment, including `shotgun`. *This gem was made by a fellow Flatiron Student!*

Using the knowledge I learned from [Learn.co](http://learn.co/)’s previous labs (especially Fwitter), programming Flatiron Hall and making it fully functional isn’t at all the challenging part. The challenge would be the designing of the front-end, streamlining the user on-boarding and flow throughout the app, as well as ensuring that all user interactions with the app is meaningful and purposeful—we want to minimise user frustration as much as possible.

## 3. Design
Naturally, along that line of thought, I chose to first engage in the design aspect of the project. I didn’t focus on making any prototype—rather, I first built a design system that will guide my overall thinking process.

For this project, the design system only serves as a visualisation guide, as I only got to the wire-framing stage and didn’t proceed with the prototyping. After considering the time/effort/efficiency of prototyping vs. developing, I jumped straight into the front-end development right away, as I already had mental pictures of how the frames will interact.

In hindsight, this made the creation process quite efficient, especially because this was such a simple project with only a few views, and it was easy to visualise the user flow without having a prototype to refer to. It saved me some time that would’ve been spent prototyping. Nonetheless, I could’ve spent time building out the user flow diagram, just so that it’d make life easier when determining which path to redirect the user, or considering which `flash` message to show, and at which point of the user journey. I previously used [Draw.io](http://draw.io) to map out the user journey, however I feel more affinity towards [Milanote](http://milanote.com/) due to it being lightweight and minimalistic, and I believe that it can also be suitable for the task.

I also noticed that I made a few changes in design decisions along the development, which gave me an inkling of how developers can provide their input and perspective for designers, and vice versa, to improve the overall UX of the product. This developer-designer feedback loop is an aspect in which I will try to deepen my understanding, and this project has been a great stepping stone for me. I’m looking forward to exercising more code-switching between the two disciplines with future projects.

## 4. Architecture
Back to code. After installing `Corneal` with `gem install corneal`, you can `cd` to the appropriate directory, create your app using `corneal new <app-name>`, and watch the magic happens! I discovered this gem a little too late, so I created the scaffold and configured everything manually—however, due to VSCode’s capabilities and its integrated terminal, setting up wasn’t a very time-consuming process.

Afterwards, make sure to redefine the scaffold structure according to your needs. I ended up with the following:
```

├── config.ru
├── Gemfile
├── Gemfile.lock
├── Rakefile
├── README.md
├── app
│   ├── controllers 
│   │   ├── application_controller.rb
│   │   ├── projects_controller.rb
│   │   └── users_controller.rb
│   ├── models
│   │   ├── project.rb
│   │   └── user.rb
│   └── views
│       ├── projects
│       │   ├── create.erb
│       │   ├── edit.erb
│       │   ├── projects.erb
│       │   └── show.erb
│       ├── users
│       │   ├── create.erb
│       │   ├── login.erb
│       │   └── show.erb
│       ├── about.erb
│       ├── error.erb
│       ├── index.erb
│       └── layout.erb
├── config
│   └── environment.rb
├── db
│   └── migrate
├── lib
│   └── .gitkeep
└── public
    ├── css
    │   └── style.css
    ├── fonts
    ├── icons
    └── js
        ├── jquery.js
        └── main.js
```

I also defined the relationship between the `User` and `Project` model. In this case, it’s straightforward enough without the `has_many :through` association.


Now, our environment is all set up and it’s ready for some action!

## 5. Development
Again, this stage took the longest—no surprises there. But again, this has proved to be a successful project, and I learned a few more things:

#### Back-end:
* Do not use `type` as an ActiveRecord column names, as `type` is one of the reserved keywords for AR. I used `type` originally to indicate which type of project the user has submitted — and ran into errors. I have changed the column name to `category` instead. Dakota warned me of this earlier—but I forgot about it, and had to learn it the hard way.
* Try to use `params[:slug]` as early as possible. I originally used `params[:id]` to set up relations between the project views and controllers, and changed nearer to the completion of the app. Needless to say, at this point, there are already a few interactions set up based on `params[:id]` and I missed making a few changes, which broke the edit and delete function of the application. Alternatively, I could have done a mass-replacement of the`.id` as well, but I believe it’s better to just use `.slug` earlier.
* Do not use two similar paths in the controller, i.e. `get '/projects/:id'` and `get '/projects/:slug'`. Only one of them will work, and I believe that depends on which comes first in the controller. So if there is a request to `http://localhost:9393/projects/<project-name>`, it will give you the Sinatra “don’t know this ditty” page, as there are no projects with `params[:id] = <project-name>`.
* Used and formatted `created_at` for viewing date of submission of projects, as well as added a few more columns along the way. It’s probably not realistic to have migrated all the columns I wanted right at the start of the project—I realised there were other columns which I wanted to add, such as the `blog_url` and `timestamps`.

#### Front-end:
* Using if-else in the `.erb` files to display certain message, depending on conditions (e.g. `logged_in?`, `user == current_user`).
* Implementing `sinatra-flash` messages to interact with users. Did the user successfully edit their project? Did they successfully log out? The gem also allows full reign over the styling of the alerts, which I find really neat—different colours can be used to indicate the nature of different alerts.
* Better understanding of `yield`. I previously only vaguely understand this concept—thinking that `yield` is meant to be written in the different views, when it is only meant for the layout view.
* HTML class names are case-sensitive. I had problems with the filter feature due to this. The way the filter works is by attaching a value to each filter link (say, I click CLI, then the value attached to that link is `cli`, and so on). Then a JS script tells it to hide all projects, and only show all the project that has the HTML class `cli`. Pretty simple in concept (learned about this in [SuperHi](http://superhi.com)). I dynamically implemented the HTML class names to every project just by adding `<%= project.category %>` to the project div— such that it becomes `<div class=”project <%= project.category.downcase %>”>`. The `.downcase` was to ensure that the HTML class name is now `cli` instead of `CLI`. I had that inkling the first time around and everything works perfectly. But during deployment, I deleted the `.downcase` and everything fell apart. Please don’t ask why I deleted it.
* Customise radio button look. I had the help of [Sam Keddy](https://medium.com/@skeddles)’s public [Codepen creation](https://codepen.io/samkeddy/pen/PbROLK) to achieve this. It’s in the create and edit project view of the application.
* Keeping aspect ratio of iframe contents using CSS padding. I still don’t quite remember which percentage means which aspect ratio—but we have the Internet for that.

## 6. Deployment (or Publishing)
Deployment was made incredibly painless due to the help of Heroku doing all the heavy-lifting and Dakota’s step-wise walkthrough. Postgres configuration was surprisingly easy. An issue I faced, however, was the Gemfile specification for the `pg` gem—the version has to be specified, such that it looks like so: `gem 'pg', ‘~> 0.20’`. Simply adding `gem 'pg'` will not work, and the release will break. Otherwise, all is good.

Once again, check out the project here: https://flatiron-hall.herokuapp.com/.


