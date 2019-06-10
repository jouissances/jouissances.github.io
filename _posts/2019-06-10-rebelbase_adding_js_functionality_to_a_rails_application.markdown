---
layout: post
title:      "RebelBase: Adding JS Functionality to a Rails Application"
date:       2019-06-10 16:18:26 +0000
permalink:  rebelbase_adding_js_functionality_to_a_rails_application
---


Learning JavaScript after spending so much time on Ruby is such a ride! I love spending time solving JS challenges on [Codewars](https://www.codewars.com/dashboard), but mini logic challenges are somehow different from implementing JS on a web app. This is my first time working with both Ruby and JS on a project, so I learned a lot!

## 1. Discovery
Initially, after reading the portfolio assessment requirements, I decided that I’m going to make these changes to my Rails app:

* Render user’s clubs on `Memberships` index using JS
* Allow user to click through the upcoming books list and read books list by clicking on ‘Next’ on the `Book` show page with AJAX
* Render `Discussion`’s list of `Comments` with AJAX
* Submit a `Comment` to a `Discussion` with AJAX
* Construct `Discussion` as JS Model Object
* Render the username of the user who posted the `Comment` with AJAX
* Render `Comment` creation time with AJAX

As you can tell, a lot of AJAX is going to happen! Essentially, AJAX is a technique used to create asynchronous web-apps. Asynchronous means that while we send a method call to the server, our web page is not blocked / frozen while loading the results. We can even interact with other elements on the page while waiting for the call to finish retrieving our results! But for our small virtual book club app, the time needed to retrieve the results is very short, so we won’t need to do that.

## 2. Strategy
A strategy that I employed here is to firstly serialize all my models using [Active Model Serializer](https://github.com/rails-api/active_model_serializers). AMS is a gem that easily lets me convert my data into JSON. I didn’t need to serialize all the models, as I was planning to implement AJAX for only the following models: `Memberships`, `Books`, `Discussions`, and `Comments`; but I did it anyways, just in case. After serializing, I edited my controllers to allow me to access the views as .json, in addition to .html. This was fun. I use the [JSONView](https://chrome.google.com/webstore/detail/jsonview/chklaanhfefbnpoihckbnefhakgolnmc) Chrome Extension to view my JSON data in a neat format, and to expand or collapse any data I need). AMS also allows me to specify any associations between my models.

I used the same associations as the ones I used for my schema. I realised that later on, I needed to modify this slightly — more on that later! After confirming that the data and associations were rendering perfectly, I went on to coding the JS. This is where the hard part begins.

## 3. Design
I didn’t focus much on visual design here, as this project mainly serves as a coding challenge for me. I did fix some CSS weirdness, but that was it.

## 4. Architecture
Rails’ architecture allowed me to separate my JS file according to the views and models that I’m focusing on. To implement AJAX on the `discussion#index` page, I only need to code my JS in `discussion.js` (which I changed from `discussion.coffee`— CoffeeScript is something that I’ll learn in the future, along with many other things).

In terms of the views structure, I separated `Discussions` views from those of `Book`‘s. What this means is that previously, you can look at a book’s discussions from the individual book’s `book#show` page, but I realised that this also means that the AJAX calls for `discussion#index` will need to be placed in `books.js` (as the `Discussions` are rendered via the `Books` controller). I then decided to move the `Discussions` views to its own rightful folder, which feels cleaner and more easily manageable.

## 5. Development
Longest part of the process. I had to watch many [Learn](http://instruction.learn.co/) videos to do this. I signed up for Rails+JS study groups, but they were conducted during my work hours and unfortunately I couldn’t attend them, so I resorted to the recordings instead. I find Brad’s [study session recording](https://instruction.learn.co/student/video_lectures#/383) really helpful! He even posted the code in his GitHub repository.

One of the bugs that I realised early on was that my AJAX calls were firing multiple times. I found out that this was because I have specified the following in my `application.js`:

```
//= require jquery
//= require rails-ujs
//= require jquery-ujs
//= require_tree .
//= require popper
//= require bootstrap
//= require typed
```

Do you see it? I had called on multiple jQuery files, which resulted in each AJAX request being fired twice. I deleted the `jquery-ujs` line, and everything was restored beautifully (no more double vision!).

Another bug happened when I had to click on a link twice before I see the results being appended onto my view. This was because I had accidentally nested a click function within another click function, which was a mess. This has been corrected.

One of my biggest learning points for this project was in learning to use `$.ajax()` and `$.getJSON()`. I used both for different parts of the project, just to practice more with both styles. Personally, I prefer the latter, as it is a shorthand for `$.ajax()` — it has already specified the parameter for the output, which is in JSON, and it automatically transforms my JSON output into an Object. Yum. I find `$.getJSON()` to be more configurable as well, as I can easily specify, configure, and see which URL I’m retrieving my request from.

I also learned to manipulate the serialised JSON data into giving me specific key-value pairs. In the `discussion_serializer.rb` file, for example, I added a custom method:

```
class DiscussionSerializer < ActiveModel::Serializer
  attributes :id, :title, :body, :book_quote, :comments_with_user

  # include created_at and updated_at

  belongs_to :book
  belongs_to :user

  # has_many :comments

  def comments_with_user
    array = []
    object.comments.map do |comment|
      hash = {}
      hash['id'] = comment.id
      hash['body'] = comment.body
      hash['created_at'] = comment.created_at
      hash['updated_at'] = comment.updated_at
      hash['discussion_id'] = comment.discussion.id
      hash['username'] = comment.user.username
      array << hash
    end
    array
  end

end
```

To render each comment’s username, I had to be able to access the comment’s `user_id` from my `Discussion` JS Object. What I realised was that my `Discussion` JS Object didn’t have access to the nested comment’s `user_id`, `discussion_id`, as AMS only went one-level nest-deep.

However, I was able to see the `user_id` when I navigate straight to the `Comments`.

So I needed to make my `discussion_serializer.rb` serve me my JSON as I like it, which was what the custom method `comments_with_user` is doing. I was able to construct a new key-value pair for my `Discussions` Object, retrieve the information I needed, and access it via `discussions.json`.

After I have my `comments_with_user` data, I didn’t need AMS to give me my original comments (without user) list, and so I commented the line `has_many :comments` (line 9) from `discussion_serializer.rb`.

## 6. Deployment
I checked out a new git branch to develop the JS features, and submitted my own pull and merge requests. For the live deployment, I will continue to use Heroku for the time being, but I look forward to learning more about server and hosting, especially with AWS EC2, which I’ve been hearing a lot about!

Thank you for reading!







