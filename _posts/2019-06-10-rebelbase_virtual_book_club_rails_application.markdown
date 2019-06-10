---
layout: post
title:      "RebelBase: Virtual Book Club Rails Application"
date:       2019-06-10 12:08:58 -0400
permalink:  rebelbase_virtual_book_club_rails_application
---


It’s portfolio project time! Whew, this one took quite awhile to build. As usual, I’ll share my approach to building [rebelbase](https://rebel-base.herokuapp.com/) from scratch — from designing the user flow up to deployment. I enjoyed every moment of the process–especially during the final refactoring steps–and I learned so much more from building this project.

Go see it, play with it, and send a suggestion my way!

## 1. Discovery
I love books. Maybe a little too much. During the ideation phase, I was coincidentally looking for an online book club with a few requirements:

* It allows members to suggest the next reads easily
* It allows members to browse all existing clubs
* It allows members to follow / unfollow any club
* Its UI/UX is intuitive and easy on the eyes


I had not seen any strong contender to date, which is the main reason why I decided to build rebelbase, but if you know any, do let me know! I’d love to check it out.

## 2. Strategy
Rails is amazing. To build a Rails app from scratch, all it literally takes is `rails new rebelbase`, and this single-line command creates the entire Rails directory structure required for our project. Coupled with the `rails generate resource` command, this sets up our models, controllers, migrations, and adds the `resources :model` to our config/routes.rb file.

An important strategy I’d like to keep in mind for future Rails project is to gather all the potential gems that would be used in the course of the project. This would give me a clearer foresight, and would allow me to configure the gems with my project early in the development stage, rather than after having written lengthy codes, which might increase the likelihood of errors.

## 3. Design
After my previous project ([Flatiron Hall)](http://flatiron-hall.herokuapp.com), I learned the value in focusing on the user flow diagram when designing. This is essential as this project is larger in scope than Flatiron Hall, and I did not want to waste time in determining where the user is at any point in time, and where to redirect the user to after a method is run.

##4. Architecture
Defining the relationships between model is no easy task, either. But after this project, I have a better idea about has_many :through, as well as about setting up references in the database.

One thing I wish I had done is to set up my Rails app with a PostgreSQL database from the start, rather than after development and before deployment to Heroku. I’d also check my Ruby version and make sure that it fulfils Heroku’s minimum acceptable version.

## 4. Architecture
Defining the relationships between model is no easy task, either. But after this project, I have a better idea about `has_many :through`, as well as about setting up references in the database.

One thing I wish I had done is to set up my Rails app with a PostgreSQL database from the start, rather than after development and before deployment to Heroku. I’d also check my Ruby version and make sure that it fulfils Heroku’s minimum acceptable version.

## 5. Development
The longest stage. But also the most enjoyable. I learned to use many gems, such as:

* [devise](https://github.com/plataformatec/simple_form) (for secure user authentication)
* [omniauth](https://github.com/omniauth/omniauth) (for OAuth)
* [omniauth-facebook](https://github.com/mkdynamic/omniauth-facebook) (for Facebook OAuth)
* [simple_form](https://github.com/plataformatec/simple_form) (for styling form fields)
* [country_select](https://github.com/stefanpenner/country_select) (for listing countries to indicate user’s location)
* [friendly_id](https://github.com/norman/friendly_id) (for params[:slug] in the URL search bar)
* [socialization](https://github.com/cmer/socialization) (for allowing users to follow / unfollow clubs)
* [google_books](https://github.com/visoft/google_books/) (for searching books and instantiating Book objects)
* [carrierwave](https://github.com/carrierwaveuploader/carrierwave) (for uploading user profile pictures)
* [mini_magick](https://github.com/minimagick/minimagick) (works together with carrierwave)
* [libri](https://github.com/jouissances/libri-cli-app) (for displaying random book-related quotes on user profile pages)


## 6. Deployment (or Publishing)
Deployment to Heroku was relatively smooth-sailing, but I couldn’t have done it without Stack Overflow. Some errors I faced include:

* Incompatible Ruby version
* Missing gem that was listed as a dependency by other gems led to `LoadError`
* Forgetting to run `heroku run rake db:drop db:create db:migrate` after pushing to GitHub master
* Incorrect migration sequence that had a table column referencing other table, while this ‘other table’ was still not created.
* Forgetting to update `heroku config` used in Facebook OAuth led to `parameter_id` error.
* Other than that, it was a successful learning project!


Thank you for reading!



