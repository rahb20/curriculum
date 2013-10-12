# Active Record

validations, associations, queries, validation errors / is_valid?... thinking data-first??? The important skill is knowing data structure of a problem first, then worrying about the rest.

## Introduction

Presumably you're here to learn web development (otherwise... you may be in the wrong place...).  Whether your goal is to be able to produce your own website or to begin a career as a developer, the most important skillset to take away from all this is the ability to think logically and to break down a problem into its component pieces.  Then you can address those pieces one at a time.  It's the essence of engineering.

Probably the most important way that logical thinking is required when building a website is in setting up your data model properly.  Data is the foundation of almost all major web applications, from a simple blog site to Facebook's massively complex web of data.  Having an obscure or overly complex data model can cripple you when you try to grow and make your life as a developer exceedingly painful.  If you're working with the wrong tools, something "simple" like asking to display all the comments a user has made on another user's web posts can take up far too many brain and CPU cycles to accomplish.

If data is the most important piece of a web application, then how Rails handles data should be very interesting to you.  Luckily, this is one of the most significant reasons that Rails has performed so well compared with the options available just a few years ago.  Active Record is the interface that Rails gives you between the database and your application.  It lets you structure your data models for your users, blog posts, comments, followers, etc. in a logical and nearly plain-english way.  If it seems complicated (which it will at points), just think of life before Active Record.

Having a solid understanding of Active Record will make the rest of Rails seem simple by comparison.  Remember that we previously learned that the Model in MVC is the part that does all the heavy lifting.  

What is Active Record anyway?  Recall that Rails is actually seven main Ruby gems that work harmoniously together.  Active Record is, to put it inelegantly, the gem that takes care of all the data stuff.  It's known as an "ORM".

## What is an ORM?

ORM stands for Object-Relational-Mapping.  It basically means that Active Record takes data which is stored in a database table using rows and columns, which needs to be modified or retrieved by writing SQL statements (if you're using a SQL database), and it lets you interact with that data as though it was a normal Ruby object.  

So if I want to get an array containing a listing of all the users, instead of writing code to initiate a connection to the database, then doing some sort of `SELECT * FROM users` query, and converting those results into an array, I can just type `User.all` and Active Record gives me that array filled with User objects that I can play with as I'd like.  Wow!  

Even more impressive, it doesn't really matter which type of database you're using (as long as you've set up the `config/database.yml` file properly), Active Record smooths out all the differences between those databases for you so you don't have to think about it.  You focus on writing code for your application, and Active Record will think about the nitty gritty details of connecting you to your database.  It also means that if you switch from one database to another, you don't actually need to change any major application code, just som configuration files.  Sounds logical, right?

## Rails Models

That's a step ahead of ourselves, though, because first it makes sense to think about what the relationship is between Rails and a database anyway.  It's actually pretty straightforward -- you want to store information about your users, so you create a database table called `users`.  You want to be able to access that data from your application, so you create a model called `User`, which is really just a Ruby file which inherits from Active Record and thus gets to use all the handy methods I was alluding to earlier like `all` and `find` and `create`.  One table == one model which inherits from Active Record.

## Your Assignment

That was really just a teaser about what Active Record can do.  In the reading below, you'll learn about how to specifically interact with Active Record in your models.  

1. I'm assuming that you've already read and followed along with the example application that was created in the [Getting Started with Rails](http://guides.rubyonrails.org/getting_started.html) intro section of the Rails Guides.  If you haven't, do that first!
2. Read the [Active Record Basics](http://guides.rubyonrails.org/active_record_basics.html) section of the Rails Guides.  

    * We'll go more into Migrations and Validations in the next section and Callbacks later in the course.
    * Model files in Rails live in the `app/models` folder and are just normal .rb files.  The key points are that the file is named after the table in your database, the class name is the same (but singular), and that class inherits from ActiveRecord::Base to get its super powers.

## Migrations

### When You Need Them

Imagine you're staring at a blank computer screen and you need to start your new Rails project.  What's the first thing you do?  You type `$ rails new MyProjectName` then `cd` into that directory...  Then what?

Figure out the data models that you'll need to use for the first iteration of your site and start getting them set up.  For our purposes, we'll just assume all you need is the ubiquitous User model to keep track of all the dozens of users who will be on your site someday (just kidding, you'll hit it big).  After you've actually created the database in the first place (using `$ rake db:create`), to create that model you'll need to do two steps:

2. Create a model file in `app/models` which is set up like you just learned above.
2. Create a database table called "users" that has the appropriate columns.  This is done using a migration file and then running the migration.

The best part is that Rails knows that you want to do this and has given you a handy shortcut for doing so: the `$ rails generate model YourModelNameHere` command.  When you type it in, you will see in the Terminal output which files are being created.  Don't worry about any specs or test files that also get created, the important ones are the model file and the migration file.  Rails has lots of these handy generators which don't do much except create new files in the right spots of your application for you.  

The model file that the generator creates is just a bare-bones model file in the `app/models` directory (which you could easily have created yourself).  The other main file is the migration file in the `db/migrations` folder, which starts with a complicated looking timestamp like `20130924230504_create_users.rb`.  If you dive into that file, you'll see that there's not much in it except another bare-bones ruby class that inherits from `ActiveRecord::Migration`.

If you want to only create the database migration file (without the Model or any of the test files), just use `$ rails generate migration NameYourMigration`.  You'll end up using this one more once you've got things up and running since you'll probably be modifying your data table instead of creating a new one.  There's a syntax for specifying additional parameters when you call this (which you'll see in the reading), but there's no need to remember that syntax because you can also manually go in and edit the migration file yourself.

### What Are They?

So what's a migration?  A migration is basically a script that tells Rails how you want to set up or change a database.  It's the other part of Active Record magic that allows you to avoid manually going in and writing SQL code to create your database table.  You just specify the correct Ruby method (like the aptly named `create_table`) and its parameters and you're almost good to go.

Migrations are just a script, so how do you tell Rails to run that script and actually execute the code to create your table and update your database's schema?  By using the `$ rake db:migrate` command, which runs any migrations that haven't yet been run.  Rails knows this because it keeps track of which migrations have been run (using timestamps) behind the scenes.  When you run that command, Rails will execute the proper SQL code to set up your database table and you can go back to actually building the website.

Why is this useful?  Obviously it lets you set up your database using user-friendly Ruby code instead of SQL, but it's more than that.  Over time, you'll build up a bunch of these migration files.  If you decide that you want to blow away your database and start from scratch, you can do that easily and then rerun the migrations.  If you decide to deploy to the web, you will run those same migrations and the production database will be there waiting for you... even if it's a different type of database!  Again, Active Record does the heavy lifting for you here so you can focus on building your website.

The most immediately useful feature of migrations is when you've screwed something up because they're (usually) reversible.  Let's say you just migrated to create a new database column but forgot a column to store the user's email... oops! You can just type `$ rake db:rollback` and the last series of migrations that you ran will be reversed and you're back to where you were.  Then you just edit the file, rerun the migrations, and move on with your life.

This introduces the last nuance of migrations that we'll talk about here -- reversibility.  For each method that you use in the migration, you want to specify how to reverse it if you have to.  The reverse of adding a table is dropping that table, of adding a column is removing the column and so on.  Many methods have a really obvious reverse case, so you don't need to explicitly state it and can set up the whole migration file using the `change` method.  But some of them do not, so you will need to separately specify `up` and `down` methods.  You'll read more about that in the assignment.

A final note, you never want to rollback migrations unless you've screwed something up.  In situations where you have a legitimate case for removing a column (because you no longer need it for any purpose), you actually create a new migration that removes that column using the `remove_column` method.  It preserves the database.  Once you get advanced with this stuff, you can build a database just using the schema file... You're not there yet :)

### How Much Database Stuff do I Need to Know?

Migrations don't involve writing SQL, but you do need to understand enough about databases to know how you want yours structured!  Which columns do you want?  Which ones should be indexed (and why)? Should you set a default value?  What data type will be stored in your column... a string or text?

These are great questions, and you should feel comfortable asking them even if you aren't totally sure about the answers.  If you have no idea what I'm talking about, you'll need to go back and read up on basic databases.  

Start with the [Databases section of Web Development 101](/curriculum/web_development_basics/web_programming_basics/database_basics.md).  If you get the itch to keep going, do the [Databases section of the curriculum](/curriculum/databases/), which you should have already done before getting here anyway.

### Your Assignment

1. Read the [Migrations chapter of Rails Guides](http://guides.rubyonrails.org/migrations.html).  

    * Don't worry about 3.6-3.8.
    * Just skim sections 7 and 8
    * Seeds (section 9) are useful and you'll be using them later.  It saves you a lot of work, especially when you're learning and will end up blowing away your database and starting over a lot.

## Basic Validations

Imagine you've got your database up and running and want to make sure that the data people are sending to your database is good data...

http://guides.rubyonrails.org/active_record_validations.html

## Basic Associations

Imagine you've got a simple data model like a User but now you want that User to relate to other things like maybe his Profile or every Post he has authored on your blog.

http://guides.rubyonrails.org/association_basics.html

## Basic Data Design