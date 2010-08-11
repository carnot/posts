---
layout: post
title: DOMLoader, A Javascript Library Making Easier To Handle Frontend Dependencies of Web Apps
categories:
  - domloader
  - projects
  - javascript
  - css

date: 08-08-2010
---

I’m glad to announce the library I’ve been working on; DOMLoader, which does handle DOM dependencies of web applications. The objective of this project is to increase reusability and maintainability of elements like apps, widgets, plugins and even libraries of a web project. In detail, here are some aims and goals of DOMLoader:

* *Modular Building*: Since dependencies like Javascript and CSS documents are growing parallel with the project and users want to be able to customize and load the part that they want, it’s not a great idea to build all source code into just one file and load it on initialization. 

* *Reusability*: Both of the mechanisms loading dependencies of a web page serverside and clientside would be able to calculate dependencies of any element of a project. Nevertheless, complete isolation of every single element of the project is vital to make reusable different pieces of the application we develop. Moreover, developers would be able to import an application located on another domain (e.g: Firebug Lite) to my web page, without using iframe element if needed. Because of this needs, DOMLoader bases dependency definition documents and supports XML and JSON by default.

* *Maintainability*: Because it makes the elements like widgets replaceable and easy to develop because of taking advantage of seperation, complete isolation is the key thing to increase maintainability of the project.

* *Low Coupling*: DOMLoader is an unobtrusive library and does depends on nothing except JSON or XML support of the web browser running it. What is more is that, the DOMLoader itself can be replaced with an alternative mechanism easily.

## Getting Started

To get started to use DOMLoader, all we need to do is to download latest version of DOMloader and put it in a directory which can be accessible from the web page from which we will import it. To take advantage of DOMLoader, I create a directory tree like as follows for every project keep isolated constituents of my projects:
 
{% highlight yaml %}
- /my_project:
    - apps/
    - lib/
    - widgets/
{% endhighlight %}

## Defining Dependencies

Now let’s get our hands dirty. Assuming that we’ll code one or more web applications which will be consisting of several DOM widgets demanded to be pluggable and isolated from other widgets and the application including it, we’re starting coding our new project from developing the widgets firstly.  Here it comes the first one:

{% highlight xml %}
    /my_project/widgets/foobar/index.xml

    <widget>
      <dependencies>
        <script src=”foobar.js” />
        <stylesheet src=”foobar.css” />
      </dependencies>
    </widget>
{% endhighlight %}

## Import

In the example above, I’ve started defining dependencies by coding a wrapper element named “widget”. Note that, it’s possible to use “index” and “application” aliases for wrapper elements as well. Then, we put the elements storing source uri’s of the dependencies in another element named “dependencies”. As you guess, script (you can also use “module” alias instead) and stylesheet represent javascript and CSS documents respectively. Now let’s code a web page importing this widget:
   
{% highlight html %}
    foobar.html
    
    <!DOCTYPE html>
    <html>
      <head>
        <script type=”text/javascript” src=”domloader.js”></script>
        <script type=”text/javascript”>
          domloader.load(‘path/to/my_project/widgets/foobar/index.xml’,function(){
            var fb = new FoobarWidget();
            /* … */
          });
        </script>
      </head>
      <body>
      </body>
    </html>
{% endhighlight %}

## Nesting

It’s that simple to import a widget with all of the dependencies. But as you expect, we need to make things a little complicated to designate advantage of using DOMLoader:

{% highlight xml %}
    /my_project/widgets/foobar-wrapper/index.xml

    <widget>
      <dependencies>
        <script src=”foobar-wrapper.js” />
        <stylesheet src=”themes/default/main.css” />
        <widget src=”../foobar/index.xml” />
      </dependencies>
    </widget>
{% endhighlight %}

This example above demonstrates creating a widget wrapping another one, using widget element to import dependencies of another widget. As you guess, widget is just alias indicating index dependency in the example above, which means, 5. line is equivelent of this two import examples: 

{% highlight xml %}
    <index src=”../foobar/index.xml” />

    <application src=”../foobar/index.xml” />
{% endhighlight %}

## Object Dependencies

To demonstrate another type of dependency, object dependencies, we're going to create one more widget named baz:

{% highlight xml %}
    /my_project/widgets/baz/index.xml

    <widget>
      <dependencies>
        <script src=”baz.js” />
        <object name=”jQuery” src=”http://ajax.googleapis.com/ajax/libs/jquery/1.4/jquery.min.js” />
      </dependencies>
    </widget>
{% endhighlight %}

As you’ve noticed, I’ve defined jQuery as an object dependency in the fourth line of the example above. Since it’s possible to duplicate dependency definition of some commonly used javascript libraries in several index documents being imported in same web page, we can test whether a library putting its context in a global variable is loaded before or not.  As you expect, DOMLoader will load jQuery’s source if only global context has not a variable named jQuery.  Besides of basic global variable testing, property elements make possible to add more specific conditions using regular expressions:

{% highlight xml %}
    <object
      name=’jQuery’
      src=”http://ajax.googleapis.com/ajax/libs/jquery/1.4/jquery.min.js”>
      <property name=’jQuery.fn.jquery’ match=’1.4.[2-9]' />
    </object>
{% endhighlight %}

The only thing remaining to be done to complete our example is definition of an application gathering some widgets now, which doesn’t differ from widget or other index definitions. I guess we’re now ready to get it done;

{% highlight xml %}
    /my_project/apps/hello_world/index.xml

    <application>
      <dependencies>
        <widget src=’../../widgets/foobar-wrapper/index.xml’ />
        <widget src=’../../widgets/baz/index.xml’ />
      </dependencies>
    </application>
{% endhighlight %}

And here is the example of importing the application we’ve defined above, almost same as the widget import example:

{% highlight html %}
    hello_world.html

    <!DOCTYPE html>
    <html>
      <head>
        <script type=”text/javascript” src=”domloader.js”></script>
        <script type=”text/javascript”>
          domloader.load(‘path/to/my_project/apps/hello_world/index.xml’,function(){
            var fwb = new FoobarWrapperWidget();
            var baz = new BazWidget();
            /* … */
          });
        </script>
      </head>
      <body>
      </body>
  </html>
{% endhighlight %}

Since I’ve coded these examples using metasyntactic namings to make it easier to read and understand, it’s not available to download but you can check these working examples and also several example documents coded for testing can be found at the project repository.

I hope this document would be helpful to get started using DOMLoader in your projects. In the next blog posts I’ll tell about using JSON and other formats like YAML and another feature of DOMLoader providing namespace initialization which will be very important to take advantage of the “async” attribute coming with HTML5. 

Nevertheless, please feel free to share your opinions and any kind of contributions with me. For now, here are the another pages/examples would be helpful to learn more about DOMLoader:

* [Source Code](http://github.com/azer/domloader/tree/master/src)
* [Tests](http://github.com/azer/domloader/tree/master/test)
* [Usage Examples](http://github.com/azer/roka-examples)