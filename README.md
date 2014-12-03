# Some Jasmine / Backbone Tips

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
        this.mixedObject = _.extend({}, textMixin);
    });
});
```

Main idea: you can be creative.  Any valid javascript will work in tests.  Not many restrictions.
Up to you to determine the code that best conveys purpose.

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

### Backbone.Models
