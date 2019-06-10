---
layout: post
title:      "hario: Fitness Resource Website (React + Rails API)"
date:       2019-06-10 16:29:02 +0000
permalink:  hario_fitness_resource_website_react_rails_api
---


Last portfolio project. It’s been a long, fruitful ride. In this post, I will outline the steps needed to design, build, and deploy [hario](https://hario.netlify.com/). This project feels like the epitome of “if you want to learn how to build it, you have to build it”. I bumped into more obstacles than ever, but ultimately got through them.

If you have more suggestions for fitness tools, comment below and I’ll build them!

## 1. Discovery
This time, I decided to do something different — rather than building a typical web-app, I made a sort of hybrid. Some parts of it seems to be static (e.g. home and about page), but some parts of it has the functionality of a web-app (e.g. fitness tool forms).

This project will be a basic one, built with the following structures:

* **Stateless components**: Header, Home, Tools, Services, About
* **Container components**: ToolContainer, ToolsContainer
* **Routes**: Home, About, Tools, Services, Results, Community

To make it more interesting, I will make it such that the data are served by the Rails API back-end. That means that in the React JSX code, there are no manually typed data — instead of typing `Harrison Oldridge` into the render method, I used `{this.props.trainer.name}` instead (`this.props` instead of `this.state` because I used redux and `mapStateToProps`). The trainer info would have been seeded onto the Rails database, and is accessible via the API GET request. The same applies for `services`, `logs`, and `tools` — with the exception of logs not having any seed data. `logs` is for the user’s input.

This means that there will be quite a few API calls. However, the maximum calls that exists on a page is 2, which is comparatively lesser compared to some websites out there.

## 2. Strategy
I have never built any React projects on Rails before. Fortunately, there are [several](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/) [comprehensive](https://blog.heroku.com/a-rock-solid-modern-web-stack) [resources](https://medium.com/@bruno_boehm/reactjs-ruby-on-rails-api-heroku-app-2645c93f0814) for React front-end + Rails API apps that I could use as guides. Some of them even guide you through setting up ActiveAdmin for user authentication — but I will keep it simple and not implement this feature for now.

A strategy that I used was to implement the form logic (e.g. macronutrient calculation) in the Rails model rather than to implement it in the reducer. Rails will be calculating the form results, and the reducer will just be in charge of fetching data from Rails.

## 3. Design
![](https://cdn-images-1.medium.com/max/720/1*Rvw07cL5xD-b2UP3r8y_7A.png)

I worked out a basic mockup for the website then improvised as I go along. While the live version looks different than the mockup, I think the live version looks simpler and more minimalistic. I used Sketch to design the mockup, which made the whole process really efficient and quick.

## 4. Architecture
After setting up the Rails API backbone using `rails new hario --api --database=postgresql`, we are now free to set up our ActiveRecord relationships and MVC structures. Setting it up with PostgresQL will allow us to easily deploy our app to Heroku later on. So far, everything was pretty straightforward. The most fun I’ve had was rendering each object as JSON and making it available for later API requests.

Now that the back-end is ready, we’ll now start building our React front-end. This is achievable by running `cd app && create-react-app client`, which creates a `client` folder in `hario/app`. Following this, we can see our front-end in the browser by running `cd client && npm start`.

Since we will be running our front-end on `localhost:3000` and our back-end on `localhost:3001`, we foresee our React app attempting to load resources from the back-end. This would be performing [**Cross-Origin Resource Sharing (CORS)**](https://medium.com/@baphemot/understanding-cors-18ad6b478e2b), and by default, browsers prevents these types of requests from scripts for security reasons. There are several workarounds for this — the one that I chose was probably the simplest.

For development purposes, I simply installed and enabled a [Chrome Extension](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi?hl=en) that allows us to request from other sources using AJAX. During production, we will use Netlify for the front-end and Heroku for the back-end, and they seem to have taken care of CORS without the need for the browser extension. I will need to look up on this — any insights are welcome.

For the first iteration of the project, I decided not to use Redux first. I decided to use no middleware to get everything working first — then refactor, or convert, using Redux.

## 5. Development
I thought I would have to watch countless [Learn](https://instruction.learn.co/student/video_lectures#/445) videos to get everything working, as I found the videos really helpful during the Rails+JS project section.

There were so many learning points during development, some of them being:

* how to send GET request using `fetch()`
* how to send POST request using `fetch()`
* how to convert React methods to using Redux middleware and make stateful components stateless
* how to use `mapStateToProps`
* how to use `rootReducer`
* how to fix JS bugs using `debugger` and `console.log()`
* how to dynamically and conditionally render components

## 6. Deployment
Deployment was quite painful. My attempts at deployment generally are. There were two parts to deployment: front-end and back-end. There are different ways to deploy a React + Rails API app, and this will be only one of them.

First, I deployed the Rails back-end to Heroku. Prior to deploying, I created a Procfile, that specifies the commands that are executed by the app on startup. Mine looks like this:

```
web: bundle exec rails s
release: bin/rake db:migrate
```

I’ve installed the Heroku CLI and completed the login step previously, so this step should be as easy as:

```
heroku create
git push -u origin master
git push heroku master
heroku run rake db:seed
```

I needed the `db:seed` command as I need the seeded data to be served to the front-end.

One persistent challenge that I faced was the `You must use Bundler 2 or greater with this lockfile` error message from Heroku during the `git push heroku master` step. After reading through [this thread](https://github.com/bundler/bundler/issues/6784), the solution that worked for me was to update my Ruby version to 2.6.1, and to install a specific `bundle` version (1.17.3) to be used in `bundle`. This somehow solved the error message, and the deployment was finally successful. I also made sure that the `/api/trainers` works.

After the back-end is deployed, we can now focus on deploying the front-end. For this project, I’ve chosen to use Netlify as it has been something I’ve wanted to learn. It was surprisingly simple! I setup my `public/_redirects` app based on [this instruction](http://snsavage.com%20-%20how%20to%20deploy%20a%20react%20app%20with%20api%20request%20proxying/) and also [this post](https://medium.freecodecamp.org/how-to-deploy-a-react-application-to-netlify-363b8a98a985), and both of them were very helpful. After the final deployment to Netlify, I checked the link using my mobile device and discovered that I was only getting a white screen, whereas on Chrome it was showing the app perfectly, with the API information shown correctly. I then used my desktop and checked it again using Opera, and discovered that it wasn’t working, either. Looking at the console, I saw that it was throwing [this error](https://stackoverflow.com/questions/53514758/redux-typeerror-cannot-read-property-apply-of-undefined) and after looking through some solutions, I decided to delete the Redux DevTools code in `index.js`, which got everything working.

A note is to remember running `npm run build` before trying to deploy to Netlify, and to remember pushing to the git repository before deploying to Heroku, which I think is good practice.



