{
	"title": "Getting Backbone to Work With Require.js",
	"date": "Sep 10, 2012",
	"tags": ["javascript", "backbonejs", "requirejs"],
  "desc": "The first time I ever tried to build an app with Backbone.js and Require.js, I ran into huge problems. Turns out it was easier than I thought."
}
--

I love [backbone.js](http://backbonejs.org). I've used it to build some simple single-page apps before, and I really like the feature set it provides while not being to cumbersome _(huge frameworks like sproutcore terrify me)_.

But the first time I tried to set up a Backbone app with [require.js](http://requirejs.org), I ran into a ton of problems. I was used to building my Backbone apps inside a single global object like this:

__Before require.js:__

    // global object container
    var myapp = myapp || {};

    // model definition
    myapp.Model = Backbone.Model.extend({
    	// model code
    });

    // instance of the model
    var mymodel = new myapp.Model;

This all worked well fine and well when all the code lived in a single `.js` file, but when I tried splitting each definition into it's own file, I would get intermittent load errors. Basically, 2 out 3 times, the entire app just wouldn't load.

After reading [this article](http://backbonetutorials.com/organizing-backbone-using-modules/), I learned that defining all of your app's models and views inside a single global object works against the way require.js is designed. It's much better to define each of your app's components like this:

__After require.js:__

    // define our component's dependencies
    define(['backbone'], function (Backbone) {
      var myModel = Backbone.Model.extend({
    		// model code
    	});

      // component returns a Backbone model definition
      return myModel;
    });

Now that the model's definition isn't tied to the global `myapp` object, we can include it as a dependency in the main require.js function:

	// require.js setup
	requirejs.config({
		// shim configuration for non-AMD modules
		shim: {
			'backbone': {
				deps: ['jquery','json2','underscore'],
				exports: 'Backbone'
			}
		},
	});

	// our main requirejs function
	requirejs([	'jquery',	'backbone',	'path/to/model'],
	function	($,			Backbone,	Model) {
		// create a new instance of our model
		var mymodel = new Model();
	});

After switching all of my app's components to this modular pattern, it cleared up all of the load errors I was having.

This was probably blatantly obvious for you JS experts out there, but for some reason it took me a while to wrap my head around the whole [AMD concept](http://backbonetutorials.com/organizing-backbone-using-modules/).
