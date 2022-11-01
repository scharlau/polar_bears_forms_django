# Parsing Polar Bear Data and Learning about Forms in Django
This is a demonstrator app focusing on how you can use forms in a python Django web application using polar bear tracking data. This builds on a [previous example that pulled csv data into a web application](https://github.com/scharlau/polar_bears_django) to show the polar bear data. Go do that one first, so that you have everything in place. 

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
With databases we normally expect CRUD operations that allow us to create, read, update, and delete records. We can currently only read records. We can do this in a few steps by adding a forms.py file, and then editing views, urls, and adding a new template.

### First, we need a form file
Create a file called 'forms.py' next to models.py and tests.py in the 'bears' folder. This file will hold the details of our BearForm. The file should contain the following code:

       from django import forms
        from .models import Bear

        class BearForm (forms.ModelForm):

        class Meta:
            model = Bear
            fields = ('bearID', 'pTT_ID', 'capture_lat', 'capture_long', 'sex','age_class','ear_applied') 

This creates a class based on the model, which is used as an object in other files, which we can move back and forth between requests. The fields listed are based on the ones in the bear model. We don't include id, and created_date as these are non-editable and automatically generated for us by the database.

### Second, we need to add a bear_new method
Open the views.py file so that we can add a method to create new bear instances. This will use the BearForm we just created and provide an empty form, as well as the method to deal with the form submission. Add this method into the form along with the relevant imports too.

        from django.utils import timezone
        from django.shortcuts import redirect, render, get_object_or_404
        from .models import Bear, Sighting
        from .forms import BearForm

        def bear_new(request):
            if request.method=="POST":
                form = BearForm(request.POST)
                if form.is_valid():
                    bear = form.save(commit=False) # don't save yet, as want to add created_date
                    bear.created_date = timezone.now()
                    bear.save()
                    return redirect('bear_detail', id=bear.id) # path url name, and use bear.id as we now have an have instance
            else:
                form = BearForm()
            return render(request, 'bears/bear_edit.html', {'form': form}) # folder/file_name under 'templates' folder

This method sets up the 'empty form for completion in the last line, and then uses the 'POST' option for submission which saves the instance. Notice too, that it validates the form values against the model before submitting it to the database, and gives us an 'id' for the item to use as well.

With 'new' bears in place, we can use similar code for the 'bear_edit' method as follows:

        def bear_edit(request, id):
            bear = get_object_or_404(Bear, id=id)
            if request.method=="POST":
                form = BearForm(request.POST, instance=bear)
                if form.is_valid():
                    bear = form.save(commit=False)
                    bear.created_date = timezone.now()
                    bear.save()
                    return redirect('bear_detail', id=bear.id)
            else:
                form = BearForm(instance=bear)
            return render(request, 'bears/bear_edit.html', {'form': form, 'bear': bear})

The difference here is that we create a bear instance to populate the form by retrieving the data from the database. We also send the 'bear' object to the request with the form data so that we can use the information of 'id' and 'created_date', which are not form fields.

Lastly, we can add a 'delete' method to remove a bear from our system.

        def bear_delete(request, id):
            bear = get_object_or_404(Bear, id=id)
            bear.delete()
            return redirect('bear_list' )
    
    This is a basic version. A better version would (a) first check for any associated 'sightings' that go with this bear, and delete those, then (b) it would delete the bear. For our purposes, this works fine.

### Third, edit the urls file
We need to tell django about the new paths in our application, which we do by adding the new url paths to the urls.py file. Add these new paths:


        urlpatterns = [
                path('', views.bear_list, name='bear_list'),
                path('females', views.females, name='females'),
                path('bear/<int:id>/', views.bear_detail, name= 'bear_detail'),
                path('bear_new/', views.bear_new, name='bear_new'),
                path('bear/<int:id>/edit/', views.bear_edit, name='bear_edit'),
                path('bear/<int:id>/delete/', views.bear_delete, name='bear_delete'),
                ]

Add the missing ones, and notice the attempted consistency in the naming of URLs.

### Fourth, add the template file
Create the file 'bear_edit.html and place it under templates/bears/ with the other template files. Add this code to the file:

    {% block content %}
        <h1>Polar Bears: Bear Edit</h1>
        <p>Bear id: {{ bear.id }}  
            created: {{ bear.created_date }}

        <form method="POST"> {% csrf_token %}
        {{ form.as_p}}
        <button type="submit" name="submit">Save</button>
        </p>
        </form>
    {% endblock %}

We pull the bear.id and bear.created_date from the bear object we send from the request. If it's for the 'new bear', then these are empty. The form.as_p tells the form to be layed out with<p> tags, and other options are as table, or with div tags. You can also style them individually if you wish, as discussed in the Django documentation https://docs.djangoproject.com/en/4.1/topics/forms/. 

We can now add some links to make getting to the new, edit, and delete methods easier. Notice that all of the urls in our href links use the url name from urls.py, instead of the file name.

Open the bear_list.html file and add this link to open the form to create a new bear as you see here below the h1 line:

        <h1>Polar bears Tagged for Tracking</h1>
        <p><a href="{% url 'bear_new' %}">Add a new bear</a></p>

Open the bear_details.html file and add these two lines above the one for listing the sightings:

        <p><a href="{% url 'bear_edit' id=bear.id %}">Edit this bear</a> 
        <a href="{% url 'bear_delete' id=bear.id %}">Delete this bear</a></p>
        <p>Sightings for this bear via Radio Device</p>

That's it. You should now be able to run the changes and see the forms working. If you have time, you could also add some tests to confirm that these methods work correctly. Time for round two.

## Round two: Do the same for bear sightings
You should be able to replicated what you did for the bear model with the sighting model. You will need to pay attention with the relationship between the bear and its sightings. You might want to make some values 'read-only' and not editable so that users can't accidently break things.

## Rounds three: What else can we do?
Play around and see what else is feasible with what you know how to do, or would like to have happen. This is still a simple, basic application that has many little things you could fix. For example, we could move code down from the views.py into the models, and there is no navigation beyond the basics done in round one, and it would be nice to have a better styling, and so many other things.
