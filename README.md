# Angular Selection Model

> Angular directive for managing selections in tables and lists

## Huh? What about ngGrid, ngTable, or ...

They are all great at what they do and `selectionModel` is not meant to be a
replacement for any of those things. `selectionModel` works directly with the
`ngRepeat` directive, it does **nothing** other than keep track of which of the
iterated over items are selected.

How does it work? The directive looks for a particular attribute on your
collection items, by default that's `selected`. When an item becomes selected
that attribute is set to `true`... when it gets deselected (surprise) it's set
to `false`. You can programmatically flip the state of that attribute as well
and the directive will respond by updating your view. For convenience we also
expose a read only list of just the selected items.

So when should you use `selectionModel`? You might consider it if:

- You want to make a list or table selectable but don't need lots extra bells
  and whistles.
- You'd want a grid/list whose styles painlessly match the rest of your app
- You're making your own fancy grid directive and want to offload selection
  management

Here's a simple example, we'll start with the controller:

```javascript
angular.module('myApp').controller('FancyStuffCtrl', function() {
  this.stuff = [
    {selected: false, label: 'Scotchy scotch'},
    {selected: true, label: 'Monacle'},
    {selected: true, label: 'Curly mustache'},
    {selected: false, label: 'Top hat'}
  ];
});
```

and the markup:

```html
<ul ng-controller="FancyStuffCtrl as fancy">
  <li ng-repeat="item in fancy.stuff" selection-model>
    {{$index+1}}: {{item.label}}
  </li>
</ul>
```

(don't forget to include the `selectionModel` module in you app).

```javascript
angular.module('myApp', ['selectionModel']);
```

That's it! Your list will key in on the "selected" attribute by default, respond
to mouse clicks, and reflect programmatic changes to `stuff`.


## Pieces of flare

So how do you let the user know about their selection? By default
`selectionModel` adds a `selected` class to its selected `li` or `tr`. It's up
to you to style those elements differently. If you're using checkboxes you can
also have their checked state match the item's selected state.


## Going farther

You can customize the behavior of your selection model by setting different
attributes on your `ngRepeat`ed element.

### selectionModelType
Default: `'basic'`

Supports either `'basic'` or `'checkbox'`. When set to basic the directive will
look for the first input element in each item (assume it is a checkbox) and
update its selected status to match the state of the item.

```html
<table>
  <tr ng-repeat="item in fancy.stuff"
      selection-model
      selection-model-type="checkbox">
    <td><input type="checkbox"></td>
    <td>{{$index+1}}</td>
    <td>{{item.label}}</td>
  </tr>
</table>
```

Note that you do not need to manually set the checkbox state.

### selectionModelMode
Default: `'single'`

May be be either `'single'` or `'multiple'` (or `'multi'`). Make use of this
option to to allow the user select multiple items using their `shift` and `ctrl`
keys.

The behavior of the multi select mode is modeled after ExtJS data grids. By
default a vanilla click (no `shift` or `ctrl`) will set the entire selection
to the single item clicked. Use `multiple-additive` to have vanilla clicks
add to the selection (and remove when the item is already selected).

```html
<table>
  <tr ng-repeat="item in fancy.stuff"
      selection-model
      selection-model-mode="multiple-additive">
    <td><input type="checkbox"></td>
    <td>{{$index+1}}</td>
    <td>{{item.label}}</td>
  </tr>
</table>
```


### selectionModelSelectedItems
Type: `Array`
Default: `undefined`

If used this should resolve to an initially empty array.  The directive will
keep the contents of that array up to date with the selection in your
collection. Note that this is a **read only** list. Adding items will have no
effect on your collection - and order is not guarenteed.

Also keep in mind that unless you're using a mode which allows for more than one
selected item this will always be an array of length 1.

In your controller:

```javascript
myApp.controller('SillyCtrl', function() {
  this.items = [ /* a bunch of stuff */ ];

  // Should start empty even if you have an initial selection
  this.selectedItems = []; 
});
```

In your view

```html
<div ng-controller="SillyCtrl as silly">
  <ul>
    <li ng-repeat="item in silly.items"
        selection-model
        selection-model-mode="multiple-additive"
        selection-model-selected-items="selectedItems">
      Click me!
    </li>
  </ul>

  <p>
    You've selected {{silly.selectedItems.length}} item(1)
  </p>
</div>
```


## Providing Configuration

### The `selectionModelOptionsProvider`

Use the `selectionModelOptionsProvider` in your module's `config` method to set
global options.

```javascript
myApp.config(function(selectionModelOptionsProvider) {
  selectionModelOptionsProvider.set({
    selectedAttribute: 'mySelectedObjectAttribute',
    selectedClass: 'my-selected-dom-node',
    type: 'checkbox',
    model: 'multiple-additive'
  });
});
```


## Even more...

Check out the docs (as soon as I hit the codebase with dox that is...)


## Running tests

Install dependencies with `npm` and `bower` then run `grunt test`. You'll need
the `grunt-cli` module installed globally.


## Running examples

Install dependencies with `npm` and `bower` then run `grunt server`. You'll need
the `grunt-cli` module installed globally.


## Release history

- 2014-01-10 v0.4.0 Expose read only list of selected items
- 2014-01-08 v0.3.0 Add `selectionModelOptionsProvider` for global configuration
- 2013-12-30 v0.2.0 Add new mode `'multi-additive'`.
- 2013-12-30 v0.1.2 Deselect filtered out items.
- 2013-12-28 v0.1.1 Initial release.


## License

[MIT](https://raw.github.com/jtrussell/angular-selection-model/master/LICENSE-MIT)
