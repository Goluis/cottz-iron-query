# cottz-iron-query
simple package to add and take parameters in the route with Meteor

## Installation
```sh
$ meteor add cottz:iron-query
```

## API
`Iron.query` works exactly as a session

#### Iron.query.set (key, value)
add or change the value of a key in the path

#### Iron.query.get (key)
gets the value of a key in the current path, if not sends `key` return an object with all the query keys

#### Iron.query.getNonreactive (key)
is exactly equal to `Iron.query.get` but non reactive (that description kills people)

## UI Helpers
helpers available in the template

#### {{ ironQuery key }}
gets the value of a key in the current path, this helper uses Iron.query.getNonreactive because in most cases
rerunning the helper in the template is not required

#### {{ ironQueryReactive key }}
is exactly equal to `ironQuery` but reactive (captain obvious help me)

## Other utilities

#### Iron.query.wait(key, value)
add or change the value of a key in the path but without redirect

#### Iron.query.go()
redirects to the path with the current keys object

to further clarify the above `Iron.query.set` is this
````js
Iron.query.set = function (key, value) {
  this.wait(key, value);
  this.go();
};
````

## Integration with iron router and fastRender
[Iron Router](https://github.com/EventedMind/iron-router) - [FastRender](https://github.com/meteorhacks/fast-render)

````js
Router.route('/home', {
    // with fastRender waiton is not very useful so I use subscriptions
	subscriptions: function () {
		var params = this.params;
		if (Meteor.isClient)
		    // in the client the query params are in params.query 
			params = params.query;

		return [
			Meteor.subscribe('news', {
			  skip: Number(params.skip),
			  search: params.q
			});
		];
	},
	fastRender: true
});

/* somewhere in the client-side */
// '10' or 10 is always a string to the route but not for Iron.query.get
Iron.query.set('skip', 10);
Iron.query.set('q', 'beautiful places');
````