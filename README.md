# Angular 2
Notes from Code School's [Accelerating Through Angular 2](http://campus.codeschool.com/courses/accelerating-through-angular-2/) course

How is angular 2 different from v1?

1) Components -- much simpler than scopes and controllers from angular 1
2) Simpler directives
3) Intuitive data binding -- when we need to link data to an HTML element or listen for a button clicking on the page, there is an intuitive syntax
4) Services are now just a class.

We'll be using TypeScript in this course, transpiling it into JavaScript for the browser. Angular 2 source code is programmed in TypeScript.

Transpiling locations
1) Send TypeScript into the browser and leave it to the browser to trnspile into JS.
2) Transpile beforehand, then ship to browser (makes application load faster)


## Building out index

In the header we'll load our angular libraries
Check out the [angular 2 quickstart guide](https://angular.io/docs/ts/latest/quickstart.html) for reference on how to start.

    <body>
      <my-app>Loading App...</my-app>  # Where the application will load
    </body>

According to quickstart guide, we will use SystemJS to load our libraries

    System.import('app')
      .catch(function(err) { console.error(err); })

    # System will look for a main.ts file in app/main.ts,
    # which will load our application's code
    # Note: the code above also makes sure that any errors we encounter
    # will be printed out to the browser's console

Let's write our first TypeScript file

    # app/main.ts

    import { Component } from '@angular/core'; # angular 2 library
                                               # `import` is ES2015 feature used to
                                               # import functions, objects, or primitives
    # `Component` is the function we will use to create our first Component

Components are the basic building blocks of Angular 2 applications
A component controls a portion of the screen.

`Component` is a `Decorator` function -- decorators add behavior to our class from outside the class
It must be declared immediately before the class (the decorator is what turns our plain old JavaScript class into a component)...

    import { Component } from '@angular/core';

    # decorator (the configuration lines inside the decorator are sometimes called metadata)
    @Component({                           # used to apply our component decorator to our class
                                           # **Decorators are a TypeScript feature**
      selector: 'my-app',                  # the CSS selector for the HTML element where we want the component to load
      template: '<h1> Ultra Racing </h1>'  # the content we want to load inside our selector
    })

    # JavaScript class
    class AppComponent { ... }

Now we need to declare our **Root Angular Module**, which is required to launch the application.

    import { NgModule, Component } from '@angular/core';

    ...

    @NgModule({                       # decorator for the application class
      declarations: [ AppComponent ]  # list of all components within the module
    })

    class AppModule { ... }

Now we need to import some dependencies to render our application...

    import { NgModule, Component } from '@angular/core';
    import { BrowserModule } from '@angular/platfor-browser';
    import { platformBrowserDynamic } from '@angular/platfor-browser-dynamic';

    # BrowserModule is the module needed for running Angular 2 websites
    # platformBrowserDynamic is an anhular library that will render the website,
    # bootstrapping/launching the application

    @NgModule({
      declarations: [ AppComponent ],
      imports: [ BrowserModule ],     # loads the required dependencies to launch
                                      # the app in a browser
      bootstrap: [ AppComponent ]     # indicates our root component
    })

    class AppModule { ... }

    platformBrowserDynamic()
      .bootstrapModule(AppModule);

So now when our apllication gets loaded:
  1) it gets bootstrapped, calls AppModule,
  2) AppModule realizes that it's the AppComponent that should get loaded
  3) it loads the AppComponent, which goes looking for the my-app selector
  4) the my-app selector gets loaded in our index.html

Components are the building blocks of our Angular 2 applications
They easily nest inside one another

    # example structure

    --------------------------------------------------
    ----------- Main application component -----------

      ------------------------------------------------
      --------------- Header component ---------------
      ------------------------------------------------

      ----------------------    ----------------------
      -- Sidebar component--    --- List component ---
      ----------------------
                                  --------------------
                                  -- List item comp --
                                  --------------------

                                  --------------------
                                  -- List item comp --
                                  --------------------

                                  --------------------
                                  -- List item comp --
                                  --------------------

                                ----------------------
    --------------------------------------------------

Eaxch component can have its own:
  * class file
  * html file
  * css file

### Sending Data Around
How do we send a property from our component class into our HTML?

    @Component({
      selector: 'my-app',
      template: '<h1>{{ title }}</h1>'
    })

    class AppComponent {
      title: 'Ultra Racing';
    }

    # Note that inside a TypeSript class we do not use the `var` or `let`
    # keywords to declare class properties
    # We'll still use them inside regular TypeScript methods

What if we have an object we want to print to the screen?

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>     # NOTE: for multi-line templates, use
        <h2>{{ carPart.name }}</h2>       # back ticks instead of single quotes
        <p>{{ carPart.description }}</p>  # this is a ES2015 feature enabled by
        <p>{{ carPart.inStock }}</p>`     # TypeScript
    })

    class AppComponent {
      title: 'Ultra Racing';
      carPart = {
        "id": 1,
        "name": "Super Tires",
        "description": "The very best tires",
        "inStock": 5
      }
    }

## Structural Directives

A directive within angular is how we add dynamic behavior to our HTML
E.g., a Component is a kind of directive, one that has a template.

Besides Component, there are two other kinds of directive:
  * Structural -- alters our layout by adding, removing, or replacing HTML elements
  * Attribute (won;t be covering in this course)

Back to our racing app, what if we had more than one car part....? How would we print that into the template?

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        <ul>
          <li *ngFor="let carPart of carParts">   # ngFor is a structural directive -- gives us `for` syntax functionality
            <h2>{{ carPart.name }}</h2>           # carPart is a lcoal variable
            <p>{{ carPart.description }}</p>      # carParts is the array to loop through
            <p>{{ carPart.inStock }} in stock</p>
          </li>
        </ul>`
    })

    class AppComponent {
      title: 'Ultra Racing';
      carParts = [
        {
          "id": 1,
          "name": "Super Tires",
          "description": "These tires are the very best",
          "inStock": 5
        },
        {
          "id": 2,
          "name": "Reinforced Shocks",
          "description": "Shocks made from kryptonite",
          "inStock": 4
        },
        {
          "id": 3,
          "name": "Padded Seats",
          "description": "Super soft seats for a smooth ride",
          "inStock": 0
        }
      ]
    }

Now we want to add the functionality to be able to display that a part is out of stock when `part.inStock` is 0... we'll use another structural directive called `ngIf`, which allows us to evaluate conditionals.

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        <ul>
          <li *ngFor="let carPart of carParts">
            <h2>{{ carPart.name }}</h2>
            <p>{{ carPart.description }}</p>
            <p *ngIf="carPart.inStock > 0">{{ carPart.inStock }} in stock</p>
            <p *ngIf="carPart.inStock === 0">Out of stock</p>
          </li>
        </ul>`
    })

## Pipes and Methods

We use pipes in angular to take in data and transform it to its desired output (like filters in angular 1.x) -- works just like Linux pipes.
How can we write out our car part names in capital letters? How about the price shown in currency?
Note: find more information about built-in angular pipes in [API reference](https://angular.io/docs/ts/latest/api/#!?type=pipe)

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        <ul>
          <li *ngFor="let carPart of carParts">
            <h2>{{ carPart.name | uppercase }}</h2>     # just pipe it to built-in uppercase pipe
            <p>{{ carPart.description }}</p>
            <p>{{ carPart.price | currency:'EUR':true }}</p>    # use Euro currency, true indicates to use symbol
            <p *ngIf="carPart.inStock > 0">{{ carPart.inStock }} in stock</p>
            <p *ngIf="carPart.inStock === 0">Out of stock</p>
          </li>
        </ul>`
    })

    # we'll use the ISO 4217 currency code (USD, EUR, CAD)

Additonal pipes built in to Angular 2:
  * lowercase
  * date
  * number
  * decimal
  * replace - creates a new string, replacing specified characters
  * slice - creates a new list or string containing a subset of the elements or characters
  * json - transforms any input into a JSON-formatted string (**great for debugging**)

How might we list the total number of car parts in stock at the top of the page?

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        <p>There are {{ totalCarParts() }} total parts in stock.</p>  # we need to create a method to total the available parts
            ...
    })

    class AppComponent {
      title = 'Ultra Racing';
      carParts = [ ... ];
      totalCarParts() {       # Note inside a TypeScript class, we do not use the keyword `function`, just like we do not use `let` to declare properties
        let sum = 0;

        for (let carPart of this.carParts) {  # ES2015 syntax for a `for` loop
          sm += carPart.inStock;
        }

        return sum;
      }
    }

Our current application structure is:

    -root-directory
    ---index.html
    ---app
    -----main.ts

### Simplifying a sum using reduce

    # refactor the sum `totalCarParts()` method using `reduce`

    totalCarParts() {
      let sum = 0;

      for (let carPart of this.carParts) {  # ES2015 syntax for a `for` loop
        sm += carPart.inStock;
      }

      return sum;
    }

    # becomes...
    totalCarParts() {
      return this.carParts.reduce(function(prev, current) { return prev + current.inStock; }, 0);
    }
    # 0 parameter at the end indicates to start with a value of 0 and iterate through, summing inStock values

    # or, using the ES2015 arrow function...
    totalCarParts() {
      return this.carParts.reduce((prev, current) => prev + current.inStock, 0);
    }

## Splitting code into multiple files
Cannot put large app into a single main.ts file

  * **main.ts** file will be just for bootstrapping the application, loading our first component
  * **app.component.ts** file will be for a component that contains our page header
  * **car-parts.component.ts** file will be for a component that contains the list of our car parts

    # app/main.ts
    ...

    @NgModule({
      declarations: [ AppComponent ],
      imports: [ BrowserModule ],
      bootstrap: [ AppComponent ]     # this will not work, because we do not have access yet to classes from external files
    })

    class AppModule { ... }

    # app/app.component.ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        ...
      `
    })

    class AppComponent {
      title = 'Ultra Racing';
      carParts = [{ ... }, { ... }, { ... }, ...]
      totalCarParts() { ... }
    }

Need to **export** the desired class from one file and **import** in another.

    # app/main.ts
    import { AppComponent } from './app.component';  # we do not have to include the `.ts` file extension // name of component must match EXACTLY

    @NgModule({
      declarations: [ AppComponent ],
      imports: [ BrowserModule ],
      bootstrap: [ AppComponent ]     # this will not work, because we do not have access yet to classes from external files
    })

    class AppModule { ... }

    # app/app.component.ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        ...
      `
    })

    export class AppComponent {                     # added `export` keyword
      title = 'Ultra Racing';
      carParts = [{ ... }, { ... }, { ... }, ...]
      totalCarParts() { ... }
    }

Now we'll split out our carParts component into a new file...

    # app/car-parts.component.ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'car-parts',
      template: `
        <p>There are {{ totalCarParts() }} total parts in stock.</p>
        <ul> ... </ul>`
    })

    export class CarPartsComponent {
      carParts = [ { ... }, { ... }, { ... }, ... ]
      totalCarParts() { ... }
    }

And now we have to tell our application about this component so we can use it...

    # app/main.ts
    import { AppComponent } from './app.component';
    import { CarPartsComponent } from './car-parts.component';    # import the component explicitly

    @NgModule({
      declarations: [
        AppComponent,
        CarPartsComponent             # add the component to our module declarations array so it can be used throughout rest of application
      ],
      imports: [ BrowserModule ],
      bootstrap: [ AppComponent ]
    })

    class AppModule { ... }

    # then use it in app/app.component.ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'my-app',
      template: `<h1>{{ title }}</h1>
        <car-parts></car-parts>`        # inserted our new component selector, which will be replaced with all the markup
    })

    export class AppComponent {
      title = 'Ultra Racing';    # removed car parts properties, now in car-parts.component.ts
    }

## Component HTML and CSS
We can also include CSS by adding a `styles` array to our `@Component` decorator

    # app/car-parts.component.ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'car-parts',
      template: `
        ...
        <p class="description">{{ carPart.description }}</p>
        <p class="price">{{ carPart.price | currency: 'USD': true }}</p>
      `
      styles: [`              # NOTE that the styles property is an array
        .description {        # the CSS is scoped to the component
          color: #444;
          font-size: small
        }
        .price {
          font-weight: bold;
        }
      `]
    })

    export class CarPartsComponent { ... }

How is the CSS scoped to the component? Angular adds a custom attribute to the generated markup, as well as to the generated CSS:

    # resulting HTML source...
    <p _ngcontent-dcy-2 class="description">These tires are the very best</p>
    <p _ngcontent-dcy-2 class="price">$4.99</p>

    # resulting CSS source...
    .description[_ngcontent-dcy-2] {
      color: #444;
      font-size: small
    }
    .price[_ngcontent-dcy-2] {
      font-weight: bold;
    }

### Splitting the files even further
Let's remove the HTML and CSS into their own files so that they're not sitting there alongside our JavaScript.

    # app/car-parts.component.ts -- we'll reference the external files inside our component metadata
    import { Component } from '@angular/core';

    @Component({
      selector: 'car-parts',
      templateUrl: 'app/car-parts.component.html',
      styleUrls: ['app/car-parts.component.css']    # Note this is still an array, will still be scoped to this component
    })

    export class CarPartsComponent { ... }

    # HTML: app/car-parts.component.html
    <p>There are {{ totalCarParts() }} total parts in stock.</p>
    <ul> ... </ul>`

    # CSS: app/car-parts.component.css
    .description {
      color: #444;
      font-size: small
    }
    .price {
      font-weight: bold;
    }

## Mocks and Models
TypeScript gives us the ability to be more object-oriented with our data, so we can create a model for car part (basically a class in JavaScript), and declare what data type each of the car part's properties should be.

    # app/car-part.ts
    export class CarPart {
      id: number;
      name: string;
      description: string;
      inStock: number;
      price: number;
    }

    # this will allow our compiler to check our code and ensure that
    # we're getting and setting the data properly, using the correct
    # names for properties and setting their values appropriately

    # back in app/car-parts.component.ts
    import { Component } from '@angular/core';
    import { CarPart } from './car-part';      # import the model

    @Component({ ... })

    export class CarPartsComponent {
      carParts: CarPart[] = [           # tells TypeScript to treat this like an array of `CarParts`
        {
          "id": 1,
          "name": "Super Tires",
          "description": "These tires are the very best",
          "inStock": 5,
          "price": 4.99
        }, ...
      ]
    }

Now, if we accidentally referred to `carPart.title` in our code instead of `carPart.name`, the TypeScript compiler will throw an error, so we can catch bugs more quickly.

Eventually we want to call out to a web service/API to get our `carParts` data, but for now we'll want to mock it in from an external file.

    # mocks.ts
    import { CarPart } from './car-part';

    export const CARPARTS: CarPart[] = [
        {
          "id": 1,
          "name": "Super Tires",
          "description": "These tires are the very best",
          "inStock": 5,
          "price": 4.99
        }, { ... }, { ... }, ...
      ]

    # app/car-parts.component.ts
    import { Component } from '@angular/core';
    import { CarPart } from './car-part';
    import { CARPARTS } from './mocks';      # import the mocked data

    @Component({ ... })

    export class CarPartsComponent {
      carParts: CarPart[];

      ngOnInit() {                    # the `ngOnInit()` method is invoked after the component is constructed,
        this.carParts = CARPARTS;     # and is the best place to initialize property values
      }                               # ...also makes the application easier to test
    }

## Property and Class Binding

Imagine we've just received raw HTML and CSS files for a template from our designer. We can add base styles to a main CSS stylesheet in its own directory, and component specific styles can go in the separate component CSS file. We'll also split the HTML up; the base application HTML will go in index.html, the root app component HTML will go in `app.component.ts`, and the car parts component HTML will go in the separate `car-parts.component.html` file

Our application structure now looks like:

    -root-directory
    ---index.html
    ---css
    -----style.css (for app-wide styles)
    ---app
    -----app.component.ts
    -----car-parts.component.ts
    -----car-parts.component.html
    -----car-parts.component.css

The ways data can flow in our application
  * JavaScript to HTML -- like we've been doing with properties from our components
  * HTML to JavaScript -- like on mouse click, hover, or key press
  * both ways -- like a text box, which should stay in sync

How would we go about adding images to our screens?
First, we need to an an image property to our model:

    # app/car-part.ts
    export class CarPart {
      id: number;
      name: string;
      description: string;
      inStock: number;
      price: number;
      image: string;
    }

    # mocks.ts
    ...
    export const CARPARTS: CarPart[] = [
      {
        "id": 1,
        "name": "Super Tires",
        "description": "These tires are the very best",
        "inStock": 5,
        "price": 4.99,
        "image": "/images/tire.jpg"   # we'll stash images in a new images folder under the root directory of the application
      }, { ... }, { ... }, ...
    ]

How to add the images to our template now?

    # we could do the following, in car-parts.component.html
    ...
    <li class="card" *ngFor="let carPart of carParts">
      <div class="panel-body">
        <div class="photo">
          <img src="{{ carPart.image }}">      # just link to the `carPart.image` property
        </div>
        <table class="product-info">
          <tr>
            <td>
              <h2>{{ carPart.name | uppercase }}</h2>
            </td>
          </tr>
        </table>
      </div>
    </li>

However, there's an alternative syntax we can use when we want to set DOM element property values. Introducing **Property Binding**...

    # car-parts.component.html
    ...
    <img [src]="carPart.image">    # the [] brackets tell angular to set this DOM element property to our component property
                                   # and **if the component property changes, update this**

Additional property binding examples:

    # hide an element if user is not an admin
    <div [hidden]="!user.isAdmin">secret stuff</div>

    # disable a button if object's property indicates it's not for sale/in stock
    <button [disabled]="isDisabled">Purchase</button>

    # set the alt text property of an object's image
    <img [alt]="image.description">

Now we want to add a `featured` class to a given car part when it is selected to be featured.
First we'll add a featured property to our model, then to our mocks:

    # app/car-part.ts
    export class CarPart {
      id: number;
      name: string;
      description: string;
      inStock: number;
      price: number;
      image: string;
      featured: boolean;
    }

    # mocks.ts
    ...
    export const CARPARTS: CarPart[] = [
      {
        "id": 1,
        ...
        "featured": false
      },
      {
        "id": 2,
        ...
        "featured": false
      },
      {
        "id": 2,
        ...
        "featured": true
      }
    ]

Now, we need to conditionally add a class if the `featured` property is true.

### Using a Class Property Binding
There is a unique syntax for binding to a class.

    # car-parts.component.html
    <ul>
      <li class="card" *ngFor="let carPart of carParts" [class.featured]="carPart.featured">  # adds `featured` class only if property is `true`
        <div class="panel-body">
          ...
        </div>
      </li>
    </ul>

How **NOT** to use class property binding:

    <div [class]="property">  # THIS WILL OVERWRITE ALL CLASSES

Instead, do:

    <div [class.name]="property">  # This will only add/remove the specific class

Side note: class names with dashes will also work fine, e.g. :

    <div [class.this-name]="property">    # YEP

## Event Binding

Listening for events, like mouse click, key press, hover, and send notification from HTML to JavaScript.

We'll add a click listener for each car part in the list in our HTML, to increase the quantity of that car part in the cart -- defaults to 0.

We'll add a quantity property to our model and start our quantity at 0 for all car parts.

    # app/car-part.ts
    export class CarPart {
      id: number;
      name: string;
      description: string;
      inStock: number;
      price: number;
      image: string;
      featured: boolean;
      quantity: number;
    }

    # mocks.ts
    ...
    export const CARPARTS: CarPart[] = [
      {
        "id": 1,
        "name": "Super Tires",
        "description": "These tires are the very best",
        "inStock": 5,
        "price": 4.99,
        "image": "/images/tire.jpg",
        "featured": false,
        "quantity": 0
      }, { ... }, { ... }, ...]

    # app/car-parts.component.ts
    import ...

    @Component({ ... })

    export class CarPartsComponent {
      ...

      upQuantity() {
        if (carPart.quantity < carPart.inStock) {
          carPart.quantity++;
        }
      }
    }

    # car-parts.component.html
    ...
    <td class="price">{{ carPart.price | currency: 'USD': true }}  </td>
    <td>
      <div class="select-quantity">
        {{ carPart.quantity }}
        <button class="increase" (click)="upQuantity(carPart)">+</button>
        }
      </div>
    </td>

Now add the same for decreasing the number of given car part in cart:

    # app/car-parts.component.ts
    import ...

    @Component({ ... })

    export class CarPartsComponent {
      ...

      downQuantity() {
        if (carPart.quantity > 0) {
          carPart.quantity--;
        }
      }
    }

    # car-parts.component.html
    ...
    <td class="price">{{ carPart.price | currency: 'USD': true }}  </td>
    <td>
      <div class="select-quantity">
        <button class="decrease" (click)="downQuantity(carPart)">-</button>
        {{ carPart.quantity }}
        <button class="increase" (click)="upQuantity(carPart)">+</button>
        }
      </div>
    </td>

Any standard DOM event can be listened for by wrapping it in parentheses and removing the word `on` from the event.

    # examples...
    <div (mouseover)="call()">

    <input (blur)="call()" />

    <input (focus)="call()" />

    <input type="text" (keydown)="call()" />

    <form (submit)="call()">

Sometimes you need more information about the event, like which key was pressed, or where the mouse is on the screen, so you need to use the angular event object.
We can send the `$event` object into our methods.

    <input type="text" (keydown)="showKey($event)" />

    showKey(event) {
      alert(event.keyCode);
    }

    <h2 (mouseover)="getCoord($event)"">Hover me</h2>

    getCoord(event) {
      console.log(event.clientX + ', ' + event.clientY);
    }

We can also call `event.preventDefault();` to prevent a clicked link from being followed or a form from being submitted.

## Two-way Binding

Keeping data in sync between script and view, for example with a text field.

We'll add an input field in between the - and + in the car part list:

    # car-parts.component.html
    ...
    <td class="price">{{ carPart.price | currency: 'USD': true }}  </td>
    <td>
      <div class="select-quantity">
        <button class="decrease" (click)="downQuantity(carPart)">-</button>
        <input class="number"
               type="text"
               [value]="carPart.quantity"                                   # This is the verbose way to bind the data in the input to
               (input)="carPart.quantity = $event.target.value">            # the quantity of the selected car part, but there's a better way to do this....
        <button class="increase" (click)="upQuantity(carPart)">+</button>
        }
      </div>
    </td>


Use `ngModel` directive to bind in two directions.
In order to get access to this, we need to import the `FormsModule` into our application module:

    # main.ts
    ...
    import { FormsModule } from '@angular/forms';

    @NgModule({
      declarations: [
        AppComponent,
        CarPartsComponent
      ],
      imports: [
        BrowserModule,
        FormsModule
      ],
      bootstrap: [ AppComponent ]
    })

    # now we can use `ngModel` in the markup:
    <td class="price">{{ carPart.price | currency: 'USD': true }}  </td>
    <td>
      <div class="select-quantity">
        <button class="decrease" (click)="downQuantity(carPart)">-</button>
        <input class="number"
               type="text"
               [(ngModel)]="carPart.quantity">            # sometimes called `banana in a box` syntax
        <button class="increase" (click)="upQuantity(carPart)">+</button>
        }
      </div>
    </td>

When we use the `ngModel` syntax, we can only set it equal to a data bound property, so we'll mostly use it in form fields.

    # works with component properties
    [(ngModel)]="user.age"
    [(ngModel)]="firstName"

    # DOES NOT work with component methods -- will error out
    [(ngModel)]="fullName()"

## Services
Used to organize and share code across the app -- usually where we put our data access methods.

    car-parts.component.ts
      ||
      || asks the service for data
      ||
    racing-data.service.ts
      ||
      || fetches the appropriate data
      ||
    mocks.ts

    # racing-data.service.ts
    import { CARPARTS } from './mocks';

    export class RacingDataService {
      getCarParts() {
        return CARPARTS;
      }
    }

    # car-parts.component.ts
    import { RacingDataService } from './racing-data.service';
    ...
    export class CarPartsComponent {
      carParts: CarPart[];

      ngOnInit() {
        let racingDataService = new RacingDataService();
        this.carParts = racingDataService.getCarParts();
      }
    }

Above, our data layer is decoupled from the application logic, but every time we need racing data in the application, we have to create a new instance of the `RacingDataService` class, which takes up memory.
Instead we can take advantage of angular 2's dependency injection to more efficiently share data across the application.

The application creates a dependency injector, and it's in charge of knowing how to create and send things.

If the `CarPartsComponent` needs racing data, it asks the dependency injector for the `RacingDataService`, and if the dependency injector knows what that is, it creates it (if needed) and sends it (injects it) into the component.
If the injector has already created a service, it can re-send the same service.

So, how does the dependency injector know what it CAN inject? We have to register "providers" with it.
To make the dependency injector work to inject the `RacingDataService` appropriately in the components that depend on it, we need to do three things:
  1) Add the `injectable` decorator to `RacingDataService`
  2) Let our injector know about our service by naming it as a provider
  3) Inject the dependency into our `car-parts.component.ts`

#### Step 1: Adding the Injectable Decorator

    # racing-data.service.ts
    import { CARPARTS } from './mocks';
    import { Injectable } from '@angular/core';

    @Injectable()                       # DON'T FORGET THE PARENTHESES!
    export class RacingDataService {
      getCarParts() {
        return CARPARTS;
      }
    }

#### Step 2: Let Our Injector Know About the Service

    # main.ts
    ...
    import { RacingDataService } from './racing-data.service';    # import the service

    @NgModule({
      declarations: [
        AppComponent,
        CarPartsComponent
      ],
      imports: [
        BrowserModule,
        FormsModule
      ],
      bootstrap: [ AppComponent ],
      providers: [ RacingDataService ]      # Add the service to the providers property in the metadata of the application module
    })

Now al subcomponents can ask for (inject) our `RacingDataService` when they need it, and an instance of `RacingDataService` will either be delivered if it exists, or it will be created and delivered.

#### Step 3: Injecting the Depenedency

    # car-parts.component.ts
    import { RacingDataService } from './racing-data.service';

    @Component({ ... })

    export class CarPartsComponent {
      carParts: CarPart[];

      constructor(private racingDataService: RacingDataService) { }

      ngOnInit() {
        this.carParts = this.racingDataService.getCarParts();
      }
    }
    # Above, `private` means that TypeScript automatically defines component properties based on the parameters.
    # The generated JavaScript is:
    #
    #   function CarPartsComponent(racingDataService) {
    #     this.racingDataService = racingDataService;
    #   }
    #
    # And, the TypeScript syntax indicates that `racingDataService` that is argument to the constructor is of type `RacingDataService`

Now our app is scalable and testable
  * Scalable because our dependencies are not strongly tied to our classes
  * Testable because it would be easy to mock services when we test the components

## Http

How might we fetch our data from the internet instead of mocking it? We'll use our service to return the data rather than `CARPARTS`.

When a user visits our web page, the Angular app loads first in our browser (HTML, CSS, JS from the server), then once Angular is loaded, it can issue requests to the server/API for data, whuch gets sent back to the browser as JSON.

To use the HTTP library:
  1) We'll create a JSON file with car parts data                                                         # car-parts.json

  2) We'll ensure our application includes the libraries it needs to do Http calls (`HTTP` and `RxJS`)

  3) We'll tell our injector about the `http` provider                                                    # app.component.ts

  4) We'll inject the `http` dependency into our service and make the `http` get request                  # racing-data.service.ts

  5) We'll listen for data returned by this request                                                       # car-parts.component.ts

  # car-parts.json
  {
    "data": [
      {
        "id": 1,
        "name": "Super Tires",
        ...
      },
      {
        ...
      },
      {
        ...
      }
    ]
  }

HTTP library provides the `get` call we will use to call across the internet
The RxJS library stands for Reactive Extensions (or ReactiveX) and provides some advance tooling for our `http` calls
If you have used the 5-minute quickstart mentioned before, this has already been included with SystemJS, so don't need to worry about it.
Moving along...

Now we need to tell our injector it can inject HTTP, so we need to import the `HttpModule`:

    # app.component.ts
    ...
    import { HttpModule } from '@angular/http';

    @NgModule({
      declarations: [
        AppComponent,
        CarPartsComponent
      ],
      imports: [
        BrowserModule,
        FormsModule,
        HttpModule
      ],
      bootstrap: [ AppComponent ],
      providers: [ RacingDataService ] # Note: we don't need to add to providers, because it's already being added as a provider inside the HttpModule
    })

Let's inject HTTP into our service and use it:

    # racing-data.service.ts
    import { CarPart } from './car-part';
    import { Injectable } from '@angular/core';
    import { Http } from '@angular/http';
    import 'rxjs/add/operator/map';

    @Injectable()
    export class RacingDataService {

      constructor(private http: Http) { }   # ...so that  we can inject Http as a dependency -- we can do this because our service class is `Injectable`
                                            # If it were not Injectable, it would not have additional dependencies

      getCarParts() {
        return this.http.get("app/car-parts.json")
                   .map(response => <CarPart[]>response.json().data);
      }
    }

    # Breaking down the `getCarParts()` function
    # Though you might expect `http.get()` to return a promise, it actually returns an observable.
    # Observables give us additional functionality, one of which is to treat the return value like an array.
    # ...so we can use the `map()` method on the response
    # We call the `json()` method on each element of the response to parse the string into JSON
    # `.data` is because the array we want is under the `data` keyword
    # And the `<CarPart[]>` tells the TypeScript compiler to treat the data returned like an array of CarParts

And finally, we need to subscribe to the stream of arriving data (the observable) and tell our component what to do when our data arrive:

    # car-parts.component.ts
    ...
    export class CarPartsComponent {

      constructor(private racingDataService: RacingDataService) { }

      ngOnInit() {
        this.racingDataService.getCarParts()
          .subscribe(carParts => this.carParts = carParts);   # when the car parts data arrive, set them equal to our local carParts array
      }
    }

If we now fired this up in our browser, it would throw an error that it cannot read the property "length" of undefined at CarPartsComponent.totalCarParts...
This is because the array of carParts is not yet defined. So we need to make sure that `this.carParts` is an array before we try to use the `for` loop to sum the total parts.

    # car-parts.component.ts
    ...
    export class CarPartsComponent {
      ...
      totalCarParts() {
        let sum = 0;

        if (Array.isArray(this.carParts)) {     # can use the `isArray` method on the Array object to check
          for (let carPart of thiscarParts) {
            sum += carParts.inStock;
          }
        }

        return sum;
      }
      ...
    }

### Last Thoughts On Implementation
  1) We have not added in error handling, which obviously is needed calling an external API across the internet
  2) Since we isolated our network calls as a service, we could easily write a `RacingDataServiceMock` service and inject it when we're testing or developing offline
  3) Observables can be quite powerful and are worth learning about if you are making a lot of `http` calls










