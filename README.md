# Blog: Model View Controller Revisited
Revisiting the MVC pattern

## All that confusion

The **model view controller pattern (MVC)** in general is the common model people use, when asking how to structure software with a user interface. The MVC pattern is vague enough to match almost all cases of well structured software und precise enough to describe the minimal set of software organisation of this kind.

On the other hand the term MVC is always a source of confusion. Everybody has his own understanding of what it exactly means and how model view and controller should interact. There is a zoo of acronyms of three or four letters describing alternative implementations of the MVC pattern. In this article I don't intend to go as far as to propose a new implementation or another acronym. I just like to analyse, what's wrong with the MVC pattern, that it causes that much confusion.

## The origional MVC

Let's start with the original idea from the early times of programming, when computers where large and data was small. At that time there were no visual elements, that you could directly touch and click. All you had to interact with the computer was the keyboard. The interaction between user and computer was a simple feedback cycle with two unidirectional channels. One direction from the monitor to the eyes, the other direction from the fingers to the keyboard. In between there was the program here and the brain there. Matters were more simple.

In the MVC pattern the controller was the part of the program responsible to manage the keyboard input, the view was the part of the program responsible to mange the monitor output. The remaining part was the model holding the data of the program. Already at this point you may ask, what about the programming logic, the brain of the program. You get the feeling, there could be a fourth part required. Actually in MVC the logical part is split. Logic closer to the model goes into the model. Logic closer related to updating the view goes into the view and the rest goes into the controller.

## Changing the game

Nowadays the simple feedback cycle has changed. Input does not come directly from the keyboard. Instead it is first caught by view elements of the advanced libraries and then broadcasted as events. If we hold to the MVC pattern our controller needs to receive the broadcasted events. In this case it has nothing to do with keyboard input any more. The actual keyboard input has become inbuilt part of the view elements. The name giving part of the original controller has been moved into the view.

Already in the simple old world the question pops up, how passive is the view. How much is the view controlled by the controller? How much does the view update itself. One classical answer to this is the **observer pattern**. The view is observing the model. When the model is changed in reaction of controller input, the view updates itself to reflect the changed data of the model. We see the old world view can be a very active part of the program. It observes the model. It serves itself with data from the model and it plugs the data into it's own view elements upon change of the model. Are the views of the new world as active?

The model observing view sounds practical. It is practical, as long as the whole model is painted upon the view. This works well for very small models like in a ping pong game in the good old times. But that is **not** what programs of today do. In times of big data only a very small part of the data is presented upon the view. When you query a search engine, you see a small subset of all data printed upon the screen, the subset you are interested in. 

You would not expect, that the screen is observing the actual database and and changes, when the database is updated. You would need to query the database again and again to keep track of the changes. Automatically querying the database again and again would be an unproductive idea in most cases. Some newspapers do this and and bother their readers with popups, that new articles are available.  What the user actually wants, is to get a small snapshot of the big data, limited to the moment of the query and to the focus of his interest.

So the idea of a view, that is permanently observing the model, that is bound to the model, is confronted with more practical requirements. A very typical view displays a single dataset. You can't bind every dataset to it's own observing view. Views are too expensive in relation to data. They need to be reused whenever possible. This changes the game. A new type of data comes in, the result of a query, a copy of one ore more related datasets. I already addressed this small pice of data as a snapshot in time. Another term used within the field of databases is the data view. Not to be confused with the view in terms of the visible components, but related in terms form the point of view of the user. The models data view becomes a visual view of this data upon the screen.



## No common term for what it is all about

**Now what is the model?** The big data model? The data view taken from this big data by any kind of a query? Or both? This are two reasons, why the MVC pattern leads to confusion. First there are two kinds of data, the big one and the small copy. The small copy is a central part. For the DB it's a query result, for JSP it's a JavaBean, other frameworks speak of domain objects, DAO, and, and, and ... Dispite the small data copy is a central key in the MVC pattern it lacks a common term. Second, in the moment the small copy comes in, the observer pattern becomes pointless. A copy doesn't change. There is no reason to bind to a copy to observe it. The classical MVC pattern with it's observing view doesn't hold, but it is vage enough to not insist upon observing and survives.

Update: For example Android Room goes a long way to actually observe changes in the database and update the object displayed in the view. It depends on the usecase, if this is helpful or anoying for the user und should be applied. In fact it is a giant step back towards the orginal concept of MVC. If this a a general trend, the observation of this article is more true in the field of web development.

## The view becomes the controller and the controller becomes the view

We can't directly bind to the big data any longer and it is pointless to bind to the small data views to observe them, as **they don't change but need to be exchanged instead**. Now, who does exchange them? The controller or the view? 

If the view should exchange them, it would need to trigger the queries upon the model and serve itself from the returned data. It would stay a similar active part as the observing old world view. We already learned that the modern view libraries already handle the keyboard input, the original task of the controller. What would be the resulting job of the controller? Let's say for the sake of simplicity nothing import. We arrive at a two layer architecture of model and view. In fact this two layer architecture is the architecture typically found in hello world tutorials. After all there is nothing wrong with this. A clean separation of the model is most important. It is certainly not MVC any more.

**So what all the buzz about MVC?** What is the controller doing? Again one answer is **the exchange of the small data views**. In this case the controller is querying the big data and exchanges the returned small data views, while the visual view is reused if possible. It listens to the events coming from the view and reacts to them by querying the model. 

This new world controller does not handle keyboard input any more. Either it is rather limited to decision making or it additionally takes up active tasks of the classical view, querying the model and serving data to the view. The remaining view is reduced to more or less passive layout tasks. The second form is the typical use of the controller today. The tasks of the new world controller have shifted closer towards the view. There is **still one characteristic task**, that it has in common with the old world controller. Depending on the users input, **it decides what activity is to be done next**.

Apart from this it mainly takes up jobs of the classical view, querying the model, serving and even setting data into the view. This explains, why it is often **difficult to clearly separate the controller from the view** today. This becomes especially obvious in the case of list adapters, that set model data in very detail deep into the view. They combine logical and visual elements. It's not clear-cut if you want to count them to the controller or to the view. You have to make a decision and hold to it, if you want to us a clean MVC pattern.

## Getting towards a clean separtion again

Let's consider the rare form of a minimised controller with a very active view. All it does is decision making. The view fully queries the model. The user is sending two types of data. The first type is data to be stored by the current view into the model (POST). The second type is data, that is required as parameters to do a query (GET) by the following activity. Each call to the controller has two phases. The first phase finishes the current activity, i.e. it commands the view to store the given POST data. Then it makes the decision about the following activity. In the second phase it starts the new activity and handles over the GET data. The new activity tells it's view to use the GET data to query the model. 

This minimised form is rarely used, but very clean. Also clean is the form, that queries and updates the model, but lets the view actively consume the given data without further help. In this case list adapters clearly belong to the view. Both forms can be combined by using a clean decision making controller, that delegates to activity objects that do the additional tasks.

## Finally

A final thought. How important is the MVC pattern at all? You can easily reuse a model in a completely separate environment. You can use the same model in an Android app or within your web server. I never encountered a case, that a view could be reused separated from the controller or vice versa. It is more important to separate the resources like XML and HTML from the code of view and controller, because different groups of people are qualified to maintain them.
