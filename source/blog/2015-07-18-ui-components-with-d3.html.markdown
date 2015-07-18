;;;
"title": "UI Components with d3.js",
"date": "2015-07-18 00:47 UTC",
"background": "https://download.unsplash.com/photo-1433838552652-f9a46b332c40",
"tags":[
  "d3",
  "ui",
  "JavaScript"
]
;;;

Today, we're going to look at doing something different with d3js. Most of the material online is geared towards creating visualizations with this amazing library, which is great, but it isn't the only thing we can use it for!

Before going heads in with the code, let's take a higher level look at what we'll be building.

For one of my projects, I had to build an 'Evidence Shoebox'; a place to put objects (named entities in the project) in various Evidence Boxes. I won't get into the details about what the project would acomplish but here is what is required of the Evidence Box.

The Evidence Box should have:

1. a header
    * a title
    * a count of the entities within it
    * an option to close/expand the body to save space
2. a body
    * the entities should be visible

Okay, that's simple enough. Before we actually go ahead I'd like to sketch this 'Evidence Box' out, so that we're are both clear on what it should like this. I find that doing this makes my life easier since I have a good idea of the final product;

And....voila!
<img src="/img/blog/ui-components-with-d3js/EvidenceBox.png">

Here's what I'd like the component to look like. I've gone ahead and annoted the sketch so don't think that should be part of the ui! Interestingly, you'd might want to extend the project by giving the user an option to view something similar for say, a tutorial.

~~~javascript
// our reusable chart named EvidenceBox
function EvidenceBox(){
  // default component values
  var width  = 200,
      height = 250;
  
  // our main function that the outsidecontext interacts with
  function component(selection){
      selection.each(function(d, i){

      });
  }
  
  // setter and getter methods for our component values
  component.width = function(value){
    if(!arguments.length) return width;
    width = value;
    return component;
  }
  
  component.height = function(value){
    if(!arguments.length) return height;
    height = value;
    return component;
  }
  
  return component;
}
~~~

Looks like our usual reusable charts except that I've changed the name to component since that's what the UI element is!

First step - build the outline

~~~javascript
.

function component(selection){
  selection.each(function(d, i){
    // get the div of the current selection
    var box = d3.select(this);

    // setup box size
    box.style({
      width: width+'px',
      height: height+'px'
    });

    // setup the outline
    box.style('border', '2px solid black');
  })  
}

.
~~~

Great! Let's see if this actually works - write the following code after our EvidenceBox factory function.

~~~javascript
var eb = EvidenceBox();

var container = d3.select('#chart');
container
  .append('div')
  .attr('class', 'evidence')
  .call(eb);
~~~

You'll need an index.html file with the required dependencies; d3.js and app.js

Bring up your webserver and you should be greeted with the following empty box with a border!

<img src="/img/blog/ui-components-with-d3js/box-border.png">

Let's add a header to our box.

~~~javascript
function EvidenceBox(){
  var width        = 200,
      height       = 250,
      // default height of the header
      headerHeight = 50,
      headerColor = '#4A90E2'

  function component(selection){
    selection.each(function(d, i){
      // get the div of the current selection
      var box = d3.select(this);

      // setup box size
      box.style({
        width: width+'px',
        height: height+'px'
      });

      // setup the outline
      box.style('border', '2px solid black');

      var header = box
                    .selectAll('.header')
                    .data([d]);
                  
      header
        .enter()
        .append('div')
        .attr('class', 'header');
      
      header.style({
        height: headerHeight+'px',
        'background-color': headerColor
      });
    })  
  }
  
  .
  .
}
~~~

Awesome! This is what you should have now.

<img src="/img/blog/ui-components-with-d3js/box-header.png">

Hmmmm. Where's the data? Let's throw in some.

~~~javascript
var data = [
  {
    title: 'Evidence 2',
    entities: [
      'Casino',
      'Craps',
      'Loaded Dice'
    ]
  }
]
~~~

And modify our selection as follows

~~~javascript
var eb = EvidenceBox();
var data = [
  {
    title: 'Evidence 2',
    entities: [
      'Casino',
      'Craps',
      'Loaded Dice'
    ]
  }
]
var container = d3.select('#chart');
container
  .selectAll('.evidence')
  .data(data)
  .enter()
  .append('div')
  .attr('class', 'evidence')
  .call(eb);
~~~

Nothing on the visual front should have changed but we now have some data bound to our UI! We're going to make use of the 'title' attribute in our data by placing it in the header.

~~~javascript
.
.
header.style({
  height: headerHeight+'px',
  'background-color': headerColor
});

var headerText = header
                  .selectAll('.header-text')
                  .data([d]);

headerText
  .enter()
  .append('span')
  .attr('class', 'header-text')

headerText
  .style({
    'line-height', headerHeight+'px',
    color: 'white'
  })
  .text(function(d){ return d.title });
~~~

<img src="/img/blog/ui-components-with-d3js/box-header-text.png">