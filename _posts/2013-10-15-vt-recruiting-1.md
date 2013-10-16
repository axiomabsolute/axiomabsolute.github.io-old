---
layout: post
title: Virginia Tech Recruiting 2013
---

<!-- excerpt-begin -->
Every year Sphere of Influence attends a recruiting even at Virginia Tech to look for the best and brightest students to recruit for internships and full time positions.  We've had fantastic luck finding great candidates there before, but we've noticed that we have a hard time knowing what technologies students use and what kinds of questions to ask in interviews.  This year, we decided to do something to change that.
<!-- excerpt-end -->

![Wordassoc]({{ site.url }}/assets/wordassoc.png)

I decided to create a [little web app](wordassoc.herokuapp.com) to gather data on the students about what kinds of technologies they use, how comfortable students are with those techonologies, and to identify good topics to focus on during interviews.

![wordassoc question sample]({{ site.url }}/assets/wordassoc-Tutorial.PNG)

The format of the app is simple.  Students select which technologies they want to be quizzed about, then the system generates a phrase and a set of technologies for them to pick from.  The user's goal is to associate as many phrases with the appropriate technologies as possible in 90 seconds.  Right answers get you a point, wrong answers work against you.  Whomever has the highest score at the end of the day wins an iPad mini.

![wordassoc question sample]({{ site.url }}/assets/wordassoc-question-sample.PNG)

As you can see by the [commit history](https://github.com/axiomabsolute/wordassoc-flask/graphs/commit-activity), the app was developed over the course of about 6 weeks, with the vast majority of the work occuring over weekends.  In total, about 40 hours were spend developing the app.

Because of the limited development time, I decided to develop the application using a minimalist web framework in Python, a language I know well, called Flask.  The majority of the in-game logic occured on the client written in JavaScript.  The entire application was hosted on [Heroku](https://www.heroku.com/), a cloud hosted platform-as-a-service (PaaS) provider, with a PostgreSQL database for persisting game results.

The result is a fully functional tech survey application with data-driven questions, a customizable answer generation scheme, and some basic analytics for telling the user how they did afterwards.  As always, the project isn't done; there are still some issues outstanding and cleanup to do, but I reached my milestones and delivered the app on time.

## Heroku and PaaS Hosting

This was my first experience using a PaaS provider for anything more than a toy application or tutorial, and I have to say how amazed I was at how easy Heroku was to set up and use.  Integrating Heroku with Flask was as easy as changing a few config settings, most of which were handled by running a command in Heroku Toolbelt, the command line tool that comes with Heroku.

At first there were a few quirks with running the app locally (using Flask's built in server) versus deploying it on Heroku, but after a brief bit of fiddling I was able to get Foreman, a local environment more similar to the deployed Heroku environment, to run the app locally.  I was even able to configure Flask to alternatively use a SQLite3 database when run locally to avoid having to set up Postgres on my machine.

Once all the setup was done, however, Heroku really started to shine.  Deployment could not be easier: commit the changes to git, push it to GitHub, then push it to the Heroku servers.  Refresh the page and the app is deployed.  Simple as that.

## Analysis

Of course, after the event was over I had to analyze the data we gathered.  Because the data was so small I opted to do my analysis in an interactive Python environment using the Numpy and Matplotlib libraries.  To visualize the results, I used JavaScript and the fantastic [D3.js](http://d3js.org/) library to generate individual visualizations, which I then imported into the graphics editing software, [Inkscape](http://inkscape.org/) to create an infographic.  [Here's the result](https://github.com/axiomabsolute/wordassoc-flask-feeback):

![Wordassoc Infographic]({{ sit.url }}/assets/VTFeedbackInfographic.png)

This was my first attempt at an infographic and was done over the course of about three days, totaling in about 15 hours of work.  Given more time, I would have liked to dig into the data a little more and add a few more graphics, but we're now almost a full month after the event so I wanted to get this out there before it got too far away.

## What questions to ask next time

