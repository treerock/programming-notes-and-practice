AngularJS is a structural framework for dynamic web apps.

It lets you use HTML as your template language and lets you extend the HTML syntax to express the application components.

It eliminates much of the code required for data binding and ?dependency injection?

[Dependancy injection is a design pattern that allows the removal of hard coded dependencies (coupled objects) and change them at run time.]

Angular teaches the browser new syntax through 'directives':

e.g.
   * Data binding in {{}}
   * DOM Control Structures (loops and hiding)
   * Form and validation code.
   * Attaching code to DOM elements.
   * Grouping HTML into reusable components.


## Zen of Angular

"declarative code is better than imperative when it comes to building UIs and wiring software components together, while imperative code is excellend for expressing business logic"

Declarative: Expresses the logic of a computation without describing the control flow. Describes what the program should do, not how it should do it.
Imperative: Describes the algorithm in a set of explicit steps the program should carry out.

 * Decouple the DOM manipulation from the app logic. Improves testability.
 * Decouple the client side from the server side.
 * Framework guides the developers through the building of the app (angular is opinionated about how CRUD applications should be built).

Angular frees you from:

 * Registering callbacks.
 * Manipulating HTML DOM programmatically. (Declaratively describe how the application state should change).
 * Controlling data to and from the UI.
 * Writing the plumbing just to get the APP up and running.

## Concepts

Template - HTML with additional markup. The template is parsed by a 'compiler' which produces the View.
Directives - Extend HTML with custom attributes and elements. They are markers in the template that tell the compiler to attach special behaviour to the DOM element. Some built in directives include ngBind, ngModel and ngView, but you can create your own custom directives.
Model - Data shown to the user, with which the user interacts. $scope
$scope - the context where the model is stored so that directives, controllers etc can access it.
Expressions - Javascript type code snippets that are placed in bindings in the template {{}}
Compiler - parses the template and instantiates directives and expressions.
Filter - Formats the value of an expression for display to the user.
View - The user's view of the DOM.
Data Binding - Sync data between the model and the view.
Controller - The business logic behind views.
Dependency Injection - Creates and wires objects and functions.
Injector - The container for dependency injection.
Module - configures the injector.
Service - Re-usable business logic, independent of views.

[Seems that the Angular way of doing things is to keep the controller free of too much code but limitting it to the code required by the view. Any business logic that is need in the background can be placed in a service.]

## Seed App

### ngApp

The ngApp directive 'auto-bootstraps' and Angular application. It designates the root of the application (e.g. on body or html tags). Only one application can be auto-bootstrapped, but you can manually bootrap multiple apps.

    <body ng-app>...</body>

You can specify the root module to be loaded.

    <body ng-app="ngAppDemo">...</body>

### Angular.Module

This is a global place for creating, registering and retrieving module, which are collections of services, filters, directives and configuration information.

    // Create a new module
    var myModule = angular.module('myModule', []);

    // angular.module(name[, requires], configFn);

name is the name of the module to create or retrieve.
requires is an array of something. If present it creates a new module. If not, an existing module is to be retrieved.
configFn is an optional configuration function for the module.

### Adding a Controller

In Angular, a Controller is a JavaScript constructor function that is used to augment the Angular Scope. When a Controller is attached to the DOM via the ng-controller directive, Angular will instantiate a new Controller object. A new child scope will be available as an injectable parameter to the Controller's constructor function as $scope.

Although Angular allows you to create Controller functions in the global scope, this is not recommended. In a real application you should use the .controller method of your Angular Module for your application as follows:

    var myApp = angular.module('myApp',[]);
     
    myApp.controller('GreetingController', ['$scope', function($scope) {
    $scope.greeting = 'Hola!';
    }]);

The example below shows the creation of a module called 'invoice1' and adds a controller called 'InvoiceController'.

    angular.module('invoice1', [])
    .controller('InvoiceController', function() {
          this.qty = 1;
          this.cost = 2;    
          this.total = function total(outCurr) {
            return this.convertCurrency(this.qty * this.cost, this.inCurr, outCurr);
          };
          this.convertCurrency = function convertCurrency(amount, inCurr, outCurr) {
            return amount * this.usdToForeignRates[outCurr] * 1 / this.usdToForeignRates[inCurr];
          };
          this.pay = function pay() {
            window.alert("Thanks!");
          };
    });

The template will contain directives ng-app="invoice1" and ng-controller="InvoiceController as invoice". (This instatiates the controller and saves it as invoice in the local context).

It is common to attach Controllers at different levels of the DOM hierarchy. Since the ng-controller directive creates a new child scope, we get a hierarchy of scopes that inherit from each other. The $scope that each Controller receives will have access to properties and methods defined by Controllers higher up the hierarchy. See Understanding Scopes for more information about scope inheritance.


### Adding Services

For small apps, logic can be all in the controller. But as the app grows it is a good idea to move all the 'non-view' logic into "services".

In the following example we create a module called 'invoice2', with a controller called 'InvoiceController' and state that the module is dependant on another module called 'finance2'.

[note files invoice.js and finance.js must be included in the template <script> tags.

    // invoice.js
    angular.module('invoice2', ['finance2'])
    .controller('InvoiceController', ['currencyConverter', function(currencyConverter) {
      this.qty = 1;
      this.cost = 2;
      this.inCurr = 'EUR';
      this.currencies = currencyConverter.currencies;
      
      this.total = function total(outCurr) {
        return currencyConverter.convert(this.qty * this.cost, this.inCurr, outCurr);
      };
      this.pay = function pay() {
        window.alert("Thanks!");
      };
    }]);



    // finance.js
    angular.module('finance2', [])
    .factory('currencyConverter', function() {
      var currencies = ['USD', 'EUR', 'CNY'],
      usdToForeignRates = {
        USD: 1,
        EUR: 0.74,
        CNY: 6.09
      };
      return {
        currencies: currencies,
        convert: convert
      };
      
      function convert(amount, inCurr, outCurr) {
        return amount * usdToForeignRates[outCurr] * 1 / usdToForeignRates[inCurr];
      }
    });

In this example, the currencyConverter service is defined by a factory function that returns that function. 

### Routing