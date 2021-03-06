
# Ember Validations

Ember-validations is a Ember.js library that can handle object validations. If you have to check the validity of object properties, 
this library does it for you. You just have to declare which property you want to validate, and which kind of validation you want for this property.

This library is inspired by the validations of the Ruby gem `ActiveRecord`.

#### Is it "Production Ready"?

No. The development has just started.

#### Getting ember-validations

Currently you must build ember-validations yourself. Clone the repository, run `bundle` then `rake dist`. You'll find `ember-validations.js` in the `dist` directory.

#### Roadmap

* One-property validation
* Automatic validation option


## Validations

Use validations to check the properties validity on an object.

Here's how you define validations on an object:

``` javascript
// the object should extend Ember.Validations mixin
MyApp.User = Ember.Object.extend(Ember.Validations, {

  // all property validations are set in an object 'validations'
  validations: {

    // the next validation is used to check the presence of the 'name' property.
    // the library will automatically find the `Ember.Validator` called `PresenceValidator`
    // in the namespace `Ember.Validators`
    name: {
      presence: true
    },

    // the next validation is used to check the numericality of the 'zipCode' property,
    // and check that the length of this property is between 3 and 10.
    zipCode: {
      numericality: true,
      length: {
        moreThan: 3,
        lessThan: 10
      }
    },

    // the next validation use a custom validator (that extends Ember.Validator)
    // 'custom' is an arbitrary name for the validation
    email: {
      custom: {
        validator: MyApp.EmailValidator,
        options: {
          domain: 'gmail'
        }
      }
    },

    // the next validation use a on-the-fly validator
    // (corresponding to the `validate` method of an Ember.Validator)
    password: {
      custom: {
        validator: function(object, attribute, value) {
          if (!value.match(/[A-Z]/)) {
            this.get('errors').add(attribute, "does not contain capital letters");
          }
        }
      }
    }
  }
});

// Later, you can call the 'validate' method to launch all properties validations.
// It will add errors to the object if there are invalid properties.
var aUser = MyApp.User.create();
aUser.validate();

// Now, properties 'isValid' and 'isInvalid' are available
aUser.get('isValid') // false
aUser.get('isInvalid') // true, as expected(!)
```

## Errors

Once the `validate` method is called, if some properties are invalid, the object property `errors` is updated.

You can get the message error on each invalid property, as follow :

``` javascript
// Given a presence error on the 'name' property, and a length error on the 'address.zipCode' property


// Using `fullMessages` property. Returns all error formatted.

myUser.getPath('errors.fullMessages');
// ["name can't be blank", "address.zipCode should have between 5 and 10 characters"]
myUser.getPath('errors.name.fullMessages'); // ["can't be blank"]
myUser.getPath('errors.address.zipCode.fullMessages'); // ["should have between 5 and 10 characters"]


// Using `messages` property. Returns only error corresponding to the exact path

myUser.getPath('errors.name.messages'); // ["can't be blank"]
myUser.getPath('errors.adress.zipCode.messages'); // ["should have between 5 and 10 characters"]
myUser.getPath('errors.messages'); // `undefined`, because there is no error at this path


// Using `allMessages` property. Returns all errors, corresponding to the exact path and nested errors

myUser.getPath('errors.name.allMesssages');
// [["", "can't be blank"]]

myUser.getPath('errors.allMessages'); 
// [["name", "can't be blank"], ["address.id", "should have between 5 and 10 characters"]]
```

There are also `keys`, `allKeys` properties that works like messages.

# Building Ember-Validations

1. Run `rake dist` task to build Ember-validations.js. Two builds will be placed in the `dist/` directory.
  * `ember-validations.js` is a unminified version (generally used for development)
  * `ember-validations.min.js` is the minified version, production ready

If you are building under Linux, you will need a JavaScript runtime for
minification. You can either install nodejs or `gem install
therubyracer`.


# How to run Unit Tests

## Setup

1. Install Ruby 1.9.2+. There are many resources on the web can help; one of the best is [rvm](https://rvm.io/).

2. Install Bundler: `gem install bundler`

3. Run `bundle` inside the project root to install the gem dependencies.

## In Your Browser

1. To start the development server, run `rackup`.

2. Then visit: `http://localhost:9292/tests/index.html

## From the CLI

1. Install phantomjs from http://phantomjs.org

2. Run `rake test` to run a basic test suite or run `rake test[all]` to
   run a more comprehensive suite.

NOTE: There is a QUnit-runner available at `tests/qunit/run-qunit.js`

# Build API Docs

NOTE: Require `node.js` to generate it.

## Preview API documentation

* run `rake docs:preview`

* The `docs:preview` task will build the documentation and make it available at <http://localhost:9292/index.html>

## Build API documentation

* run `rake docs:build`

* HTML documentation is built in the `docs` directory