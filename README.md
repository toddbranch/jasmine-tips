# Some Tips

## Use Underscore

Underscore can make writing tests much easier.

Type inspection:
```
expect(_.isString(result)).toBe(true);
expect(_.isArray(result)).toBe(true);
expect(_.isObject(result)).toBe(true);
```

Loops:
```
it('should return undefined on bad inputs', function() {
    var badInputs = [undefined, 1, '1', []];

    _.each(badInputs, function(input) {
        expect(testFunction(input)).not.toBeDefined();
    });
});
```

To test things like mixins:
```
describe('my test mixin', function() {
    beforeEach(function() {
        // NOTE: I like to attach test objects / views like this
        this.mixedObject = _.extend({}, textMixin);
    });
});
```

Main idea: you can be creative.  Any valid javascript will work in tests.  Up to you to determine the code that best conveys purpose.

## Stubs (Spies)

Fake a function or object property.  See if it has been called and the arguments passed to it.

```
it('should call Backbone.trigger', function() {
    spyOn(Backbone, 'trigger');

    testFunction();

    expect(Backbone.trigger).toHaveBeenCalled();
});
```

You can also access the arguments that were passed to the spy for inspection:
```
it('should pass appropriate first arg to Backbone.trigger', function() {
    spyOn(Backbone, 'trigger');

    testFunction();

    var firstArg = Backbone.trigger.calls.mostRecent().args[0];
    
    expect(_.isObject(firstArg)).toBe(true);
    expect(firstArg.property).toBeDefined();
});
```

By default, functions that are spied on return undefined.  But you can change that.
```
    var backboneTriggerSpy = spyOn(Backbone, 'trigger');

    // calls to Backbone.trigger will return undefined here

    backboneTriggerSpy.and.returnValue('test');

    // calls to Backbone.trigger will return 'test' here

    backboneTriggerSpy.and.callFake(function(e) {
        return e;
    });

    // calls to Backbone.trigger will call the function, which returns the first argument
```

## Mocks

An object that can be passed in to a function and respond in pre-defined ways.

```
var mock = jasmine.createSpyObj('mock', ['play', 'pause', 'stop']);

// Each method on the mock can be used as a spy

mock.play.and.returnValue('Playing!');
mock.pause.and.callFake(function(song) {
    return 'Paused ' + song;
});

testFunction(mock);

expect(mock.stop).toHaveBeenCalled();
```

### Event Handlers

A lot of times you're going to want to call `preventDefault` on the event object passed in:

```
function eventHandler(e) {
    e.preventDefault();

    // do something useful 
}
```

To test:

```
it('should call preventDefault on event', function() {
    var event = jasmine.createSpyObj('event', ['preventDefault']);

    eventHandler(event);

    expect(event.preventDefault).toHaveBeenCalled();
});
```

Also useful for Backbone.Models, etc.

# Hard Things to Test

## Globals

Don't use them.  If you must, create a function that accesses the global variable and stub it out.

## Big Functions

Refactor into smaller functions.

## Anonymous Callbacks

Turn these into named functions and test them separately.

# General Thoughts

## Isolate Your Code

You shouldn't be testing Backbone or Handlebars functions or functions your code is requiring!  Stub them!
