---
layout: post
title:      "Libri: CLI Scraper RubyGem for Bibliophiles"
date:       2018-11-14 04:24:44 +0000
permalink:  libri_cli_scraper_rubygem_for_bibliophiles
---


I’ll share my process-based approach on how I created Libri and published it on [RubyGems.org,](http://rubygems.org) alongside some technical roadblocks that I faced during the development phase. This project specifically focuses on scraping, which is a term used to describe the act of retrieving HTML-and-CSS-based data from a website page. Here is a walkthrough video of how Libri works:

<iframe src="https://player.vimeo.com/video/277752446" width="640" height="400" frameborder="0" allowfullscreen></iframe>

## 1. Discovery
After sifting through several scraping ideas—including scraping [Noti.st](https://noti.st/events), or [80,000 Hours’ Problem Profiles](https://80000hours.org/problem-profiles/), or [Adafruit’s Raspberry Pi projects](https://learn.adafruit.com/category/raspberry-pi)—I settled on going back to a theme that can be simple, meaningful, and usable by many: Books. In searching for which website to scrape from, I had several options: the [Man Booker](http://themanbookerprize.com/international) website, [Goodreads’ Awards](https://www.goodreads.com/award) section, as well as [Penguin’s Award Winners](https://www.penguinrandomhouse.com/books/award-winners) list.

I chose [Barnes & Noble’s](https://www.barnesandnoble.com/b/books/awards/_/N-29Z8q8Z1d6q?showMoreIds=10008) awards webpage to scrape as it seems to be the most comprehensive and it’s also quite up-to-date.

## 2. Strategy
To build a gem using Bundler, I started by running `bundle gem libri` in the terminal at the Libri working directory. This will create file structures (called [scaffold directory](https://bundler.io/v1.16/guides/creating_gem.html)) for our gem, so we can start coding right away.

I made sure that my computer has also installed the following dependencies:

* **Rake**, used to build a local copy of our gem, which we’ll use to push and publish to RubyGems.org
* **OpenURI**, used to open a URL as if it is an HTML file
* **Nokogiri**, used to parse HTML and XML values from a webpage
* **Pry**, used as a local sandbox and a debugging tool
* **Colorize**, used to style text in the terminal using different colours

## 3. Architecture
Now, for Libri, I wanted to make 3 things work on my terminal:

* Display the various awards
* Display the books belonging to a chosen award
* Display the information of a chosen book

To do this, I structured my `lib` folder in this manner, separating the `CLI`, `scraper`, `awards`, `books`, and `book` classes.

![](https://cdn-images-1.medium.com/max/720/1*YmKk_1m67ONVidLpO7Xv0g.png)
*Simplified directory structure for Libri*

Each class is responsible for different parts of the gem:

* The `CLI` class is responsible for the terminal interface that interacts with the user
* The `scraper` class scrapes text-based contents off the webpage
* The `awards` class creates new instances of `Awards` object from hash values returned by the `Scraper.scrape_barnes_noble` method
* The `books` class creates new instances of `Books` object from hash values returned by the `Scraper.scrape_award(award)` method
* The `book` class creates new instances of `Books` object from hash values returned by the `Scraper.scrape_book(book)` method

## 4. Development
This stage took the longest to complete, but all in all, it was a success, and I have several notes to make:

* I learned to use a multi-line string via `HEREDOC`, which in itself has various methods to achieve the same thing (e.g. `%{...}, %Q{...}`, `<<-EOS...EOS`)
* Initially, whenever an `exit` command was called, the `Please try again.` message would also be displayed. This was fixed by using a single-level `if/else...end` conditional rather than `while input != 'exit'...end` loop.
* I knew that I wanted to access several levels of information, scraping from various URLs, and being able to pass in different URL based on the user’s input (e.g. if user inputs for the Pulitzer Awards, the `Scraper.scrape_award()` method must return information based on the Pulitzer Awards URL. If user inputs for Man Booker Prize, the expected return should be from the Man Booker URL). I knew then that I needed to pass in the URL as an argument for the `Scraper.scrape_award()` method. Knowing this, I included a `:url` key into the top-level `awards` hash, whose value will be passed in to `Scraper.scrape_award()`. Then, the second-level `books` hash can scrape from and access from the passed in URL—the same concept applies as we scrape from a third-level URL for individual book information. I wasn’t sure if this was workable, as previous labs I worked on hasn’t used a multi-level, real-time updated website, and therefore had no need for this flow. But it was! This was the best revelation I learned while building this project, knowing that versatility can be built into code.
* I couldn’t access HTML values for attributes which are not `href`. The rating values on the B&N website was stored within the `aria-label` attribute, which does not return a value when I attempted to access it. I also couldn’t access the books listed under the **Customers Who Bought This Item Also Bought** section, which returns nothing as well. I’m still searching for answers.
* Originally, upon scraping, I realised that I could access hash values and display them from the `CLI` class using `Hash[:key]`, even without instantiating new objects and assigning them their arguments / attributes. This led to an oversight, where I published the working gem without practicing the Ruby object relationship methods, such as `has-many`. This was fixed by editing the awards, books, and book classes accordingly. Now we can access hash values, such as `book.title` and `book.author` using the `attr_accessor`.
* At one point, as the terminal displayed a list of books, then went back to select another award, the list of books displayed was accumulated, resulting in 20–40–60… number of books. This was a disaster, and I had almost given up. However, it was soon realised that the bug was caused by `CLI#make_book(award)` method being called every time `CLI#menu_award` was called, and this adds a new array of books onto `Books.all`. `#make_book(award)` is needed to instantiate our `Book` object and to access various attributes of our `Book`, and we need that. To fix this, a method to clear the previous instantiated object was included before `#make_book(award)` is called, thus resetting the `Books.all` return value for each menu call.

All in all, I wouldn’t have been able to overcome these challenges without talking through my code line by line, component by component, flow by flow, as suggested by Dakota.

By talking out my thought process based on this rough flow:

* What am I trying to do?
* Is Ruby doing what I’m expecting her to do? (Yn)
* If no, what’s happening instead, and why do we think it’s happening?
* If it’s happening because of X—then, if we change Y, we expect Z to happen.
* We test our hypothesis by changing Y, and we see if Z happens.
* If Z happens, based on our understanding of X, we should know how to fix it and achieve what we were trying to do.
* If Z doesn’t happen, don’t give up! Read up and look for help, and test different understandings to find the one that works with Ruby.
* This is a simple project, however, with several different components interacting with one another, it was soon quite easy to lose track of one of them (e.g. how best to access and display every single piece of information, at which stage have objects been instantiated and at which stage they have not been, etc.), and when I lost that one, I soon lost focus on the big picture and I had to start all over again. So here’s to remember to keep practicing, and to practice it **right**!

## 5. Publishing
Lastly, to publish a gem for the first time, I followed these simple steps:

1. Edit the Gemspec file and update the `Summary` as well as `Description` specification. Make sure that all `todo` on the file has been rewritten to prevent any potential errors when publishing. Next, comment out the entire code block that says `Prevent pushing this gem to RubyGems.org`, otherwise we won’t be able to push our gem.
2. Change `spec.bindir` and `spec.executables`.
3. Add dependencies via `spec.add_development_dependency` and `spec.add_dependency`.
4. Update the `version.rb` file if necessary, following [semantic versioning](https://guides.rubygems.org/patterns/#semantic-versioning) standards. There are many guides out there, including [this](https://www.jvandemo.com/a-simple-guide-to-semantic-versioning/) and [this](https://www.sitepoint.com/semantic-versioning-why-you-should-using/).
5. Update the `README.md` file as well. This is to help users have an overview of the gem, as well as how to install and run the gem.
6. Make sure that your GitHub repo has all its files updated (latest commit and push).
7. Make sure that `rake` is installed—so we can run `rake build` followed by `rake release`, which will push our latest gem version onto RubyGems.org for others to use! Alternatively, I also tried using `gem build` and `gem push libri-0.x.x.gem` to a similar effect. Another alternative is to install the `gem-release` gem, which provides several methods for helping with gem development that I will explore with further projects.

<hr>
*Hope you enjoyed this post, and I hope that makes sense to you! Drop in any suggestions for the gem and I’ll work on it. Happy coding! Originally published on Medium [here](https://medium.com/@janicedarikho/libri-cli-scraper-rubygem-for-bibliophiles-ad1a936e3c95).*

