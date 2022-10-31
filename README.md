# Parsing Polar Bear Data and Learning about Forms in Django
This is a demonstrator app focusing on how you can use forms in a python Django web application using polar bear tracking data. This builds on a [previous demonstrator that pulled csv data into a web application](https://github.com/scharlau/polar_bears_django) to show the polar bear data. Go do that one first, so that you have everything in place. 

Django follows a model-view-controller architecture so that you can put code in a 'good' place for reuse. However, in Django the names are different. Models are models, views in Django are controllers, and it uses templates as views. Remember this as we go forward and it will help to keep things clearer.

The purpose of this exercise is to add forms to the application, which currently has none. That means we can only change data by deleting data in the database, and then editing the csv, and reloading it. That's a fine to start, but not really how we'd go about using this to add new bears, and sightings, or editing data.

The goal of 'deliberate practice' is to think about how you'd solve this challenge, and to work at developing code to make this work. There is no single 'correct' version of this code. The purpose of the exercise it become familiar with different ways of making the application work. You should explore how this simple application is done in python with flask and sqlite3 so that you understand how the components work together to show up in the views you see in the browser.

Under 'deliberate practice' we offer up the challenge, then think about options for developing a solution, and code for 12 minutes. After that we pause to discuss how people are approaching the problem, and what they're trying to do. This should be repeated three times and then wrapped up with time for people to express what they found most useful during the session. This should take an hour.

## Doing the work
Now that the basics are working, we can see what else is possible in this application.

Round one should have you adding in a basic form to edit a 'bear'. 
Round two should be adding forms to edit 'sightings'.
Round three should be exploring what else might be possible, even if only to experiment with other options of what display, and make editable on a page.

## Round one: Adding CRUD forms for a bear
With databases we normally expect CRUD operations that allow us to create, read, update, and delete records. We can currently only read records.

## Round two: Do the same for bear sightings
You should be able to replicated what you did for the bear model with the sighting model. You will need to pay attention with the relationship between the bear and its sightings. You might want to make some values 'read-only' and not editable so that users can't accidently break things.

## Rounds three: What else can we do?
Play around and see what else is feasible with what you know how to do, or would like to have happen. This is still a simple, basic application that has many little things you could fix. For example, there is no navigation, and it would be nice to have a better styling, and so many other things.
