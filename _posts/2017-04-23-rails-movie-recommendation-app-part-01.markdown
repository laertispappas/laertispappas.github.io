---
layout: post
title: Create a Movie Recommendation App with Rails, Angular 2 and Ionic Part I
date:   2017-04-23
categories: "complete-app"
tags: [Ruby, Rails, Angular 2, Recommendation Systems, Machine Learning]
author: Laertis Pappas
published: false
comment: true
---

In these post series we are going to build a Rails application to recommend movies to our users. We are going to use Rails 5
as our backend API and Angular 2 in the front end. We will start by creating the main functionality of the application
which is to display and show movies and add the ability for users to rate or comment. Later on we will finish our series
by adding some machine learning algorithms and build a movielens clone in order to recommend movies to users thus providing
a more personalized experience. We will explore some of the most common used algorithms in Recommender Systems
and then expand them by combining different approaches. We will probably use scala to build the Recommendation Engine but we will come
back on this in a future post since we might build it in Python 😀. In the end of the serries we will continue and build the application
for our mobile users using Ionic framework.

First we will create a folder that will include all our inner projects. For simplicity all projects will be under the same
git repo.

```bash
λ> cd ~
λ> mkdir movies_app
λ> cd movies_app
λ> git init
```

Under `movies_app` folder we will create the Rails project for the API, the Angular 2 project for the web, the Ionic project for mobile
and the recommender engine project.


## Angular 2 Setup

To setup the angular 2 project we will use the <a href="http://example.com/" target="_blank">Angular CLI</a>. In order to
install the CLI, we'll use <a href="https://nodejs.org/" target="_blank">Node and npm.</a>

```bash
λ> npm install -g angular-cli
```

We can start using the CLI to build out our angular2 application.

```bash
λ> cd ~/movies_app
λ> ng new movies-web
λ> cd movies-web
λ> git add .
λ> git commit -m "Init ng2 web app"
```

The majority of our application is under src/app. This is where we will be working. `ng` will also install all `npm` dependencies
required in `package.json`.

## Rails Setup

First make sure Rails 5 is installed in your system.

```bash
λ> rails -v
Rails 5.0.1
```

Then navigate to the `movies_app` folder and init a new Rails API only application using postgresql for database.

```bash
λ> cd ~/movies_app
λ> rails new movies_api -T --api --database=postgresql
λ> cd movies_api
λ> git add .
λ> git commit -m "Init Rails API project"
```

The next thing we need to do is to modify `config/database.yml` with the correct user credentials.

```ruby
default: &default
  adapter: postgresql
  encoding: unicode
  timeout: 5000
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: <%= ENV['DB_USER'] || 'movies_app' %>
  password: <%= ENV['DB_PASSWD'] || '' %>

development:
  <<: *default
  database: movies_api_development

test:
  <<: *default
  database: movies_api_test

production:
  <<: *default
  database: movies_api_production
  host: <%= ENV['DB_HOST'] || 'localhost' %>
  port: <%= ENV['DB_PORT'] || 5432 %>

```

To manage environmental variables we will use `dotenv-rails` gem and create the corresponding .env files for each
environment. For simplicity we will create only `.env` file that will include the local development variables. We will
also commit this file in our git repo since it will not contain any production sensitive information.

We therefore add `dotenv-rails` in Gemfile:
```ruby
 # File Gemfile
 gem 'dotenv-rails', groups: [:development, :test]
```

And run `bundle install` to install the gem:

```bash
λ> bundle install
```

We create `.env` file and export `DB_USER` and `DB_PASSWD` variables. The `.env` file should have the following content:

```bash
DB_USER=YOUR_DB_USERNAME
DB_PASSWORD=YOUR_DB_PASSWORD
```

We are now ready to setup the database:

```bash
λ> rake db:setup
```

This will create development and test databases, set their owners to the user specified, and create
"schema_migrations" tables in each. This table is used to record your migrations to schemas and data.

Next we commit our changes.

```bash
λ> git add .
λ> git commit -m "Adds dotenv gem and configures database.yml"
```
