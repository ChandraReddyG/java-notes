# Template
Each Angular template in your app is a section of HTML that you can include as a part of the page that the browser displays. An Angular HTML template renders a view, or user interface, in the browser, just like regular HTML, but with a lot more functionality.


## Data binding
Without a framework, you would be responsible for pushing data values into the HTML controls and turning user responses into actions and value updates. 
Writing such push and pull logic by hand is tedious, error-prone, and a nightmare to read, as any experienced front-end JavaScript programmer can attest.

* The {{hero.name}} interpolation displays the component's hero.name property value within the <li> element.
* The [hero] property binding passes the value of selectedHero from the parent HeroListComponent to the hero property of the child HeroDetailComponent.
* The (click) event binding calls the component's selectHero method when the user clicks a hero's name.

### Interpolation
Text interpolation allows you to incorporate dynamic string values into your HTML templates. With interpolation, you can dynamically change what appears in an application view, such as displaying a custom greeting that includes the user's name.

Interpolation refers to embedding expressions into marked up text. By default, interpolation uses the double curly braces {{ and }} as delimiters.

Example if we have variable with value in component
```ts
@Component({
  selector:    'app-hero-list',
  templateUrl: './hero-list.component.html',
  providers:  [ HeroService ]
})
export class HeroListComponent implements OnInit {
   currentCustomer = 'Maria';
   
   function convertToDate(){
      const date = new Date();
      return date.toLocaleDateString();
    }
}
```
In HTML Template
```
<h3>Current customer: {{ currentCustomer }}</h3>
<p> {{ convertToDate() }} </p>
<p> {{ new Date() }} </p>
```

### Property Binding
Property binding in Angular helps you set values for properties of HTML elements or directives. With property binding, you can do things such as toggle button functionality, set paths programmatically, and share values between components.

The brackets, [], cause Angular to evaluate the right-hand side of the assignment as a dynamic expression. Without the brackets, Angular treats the right-hand side as a string literal and sets the property to that static value.

Property binding is a one-way mechanism that lets you set the property of a view element. It involves updating the value of a property in the component and binding it to an element in the view template. Property binding uses the [] syntax for data binding. An example is setting the disabled state of a button.

```
// component.ts
@Component({
  templateUrl: 'component.html',
  selector: 'app-component',
})
export class Component {
  buttonDisabled = true;
}
```

```
// component.html    
<button [disabled]="buttonDisabled"></button>
```

### Difference between Property Binding and Interpolation

<table>

 <tr>
   <th style="vertical-align: top">
     Interpolation
   </th>

   <th style="vertical-align: top">
     Property Binding
   </th>
 </tr>

 <tr>
   <td>
      Angular evaluates all expressions in double curly braces, converts the expression results to strings, and concatenates them with neighboring literal strings. Finally, it assigns this composite interpolated result to an element or directive/component property.
   </td>
   <td>   
   Property binding does not convert the expression result to a string.
   So if you need to bind something other than a string to your directive/component property, you must use property binding.   
   </td>   
 </tr>

 <tr>
   <td>
      Interpolation uses the {{ expression }} to render the bound value to the component’s template. Interpolation is a special syntax that Angular converts into a property binding
   </td>
   <td>
       Property binding uses [] to send values from the component to the template. Property Binding: to set an element property to a non-string data value, you must use property binding
   </td>   
 </tr>

</table>

## Event Binding
Event binding allows you to listen for and respond to user actions such as keystrokes, mouse movements, clicks, and touches.

To bind to an event you use the Angular event binding syntax. This syntax consists of a target event name within parentheses to the left of an equal sign, and a quoted template statement to the right. In the following example, the target event name is click and the template statement is onSave().

```
<button (click)="onSave()">Save</button>
```
The event binding listens for the button's click events and calls the component's onSave() method whenever a click occurs.

![event-binding.svg](images/event-binding.svg)

### EventEmitter
EventEmitter can be used if we want to send value from one component to another. Below is the example

Let's says we have app-child component like:

app-child-template.ts
```
@Component({
    selector: 'app-child',
    templateUrl: './app-child-template.html'    
})
export class AppChildComponent implements OnInit {
    counter = 0;

    @Output() counterValueEvent = new EventEmitter();

    constructor() { }

    ngOnInit(): void { }

    handleclick() {
        console.log('clicked');

        this.counter = this.counter + 1;
        this.counterValueEvent.emit(this.counter);
    }
}
```

app-child-template.html
```
<button class='btn btn-primary' (click)="handleclick()">Cliick Me</button>
```

If we want to just handle the click event then on click 'handleclick' function will get executed. But let's say we want to send value of counter to another component.

To send the value to another component first we need to add EventEmitter like below. This will raise event using **this.counterValueEvent.emit(this.counter);**
```
@Output() counterValueEvent = new EventEmitter();
```

**Now how do we handle the event on other side?**

Let's say we want to add the counter value in productList Template and Component.

product-list-component.html
```
<div *ngIf="products && products.length">
    <div *ngFor="let product of products">
        <div>{{product.id}}</div>
        <div>{{product.name}}</div>
    </div>
</div>
<app-child (counterValueEvent)='displayCount($event)'></app-child>
```
Above we have done following changes:
* Added <app-child> directive which has event listener. So it listens to counterValueEvent and passes it to displayCount function in component.


product-list-component.ts
```
@Component({
    selector: 'product-list',    
    templateUrl: './product-list-template.html'
})
export class ProductList implements OnInit {

    products: any[] = [
        {
            "id": 1,
            "name": "Bike"
        },
        {
            "id": 2,
            "name": "Monitor"
        }
    ];

    constructor() { }

    ngOnInit(): void { }

    displayCount(count: any) {
        console.log(count);
    }

}
```

In Above code displayCount function handles the event value and prints it in console.

Examples:

https://stackblitz.com/angular/emayrnldyko?file=src%2Fapp%2Fapp.component.ts




## Two Way Binding

Two-way binding gives components in your application a way to share data. Use two-way binding to listen for events and update values simultaneously between parent and child components.

Angular's two-way binding syntax is a combination of square brackets and parentheses, [()]. The [()] syntax combines the brackets of property binding, [], with the parentheses of event binding, (), as follows.

```
<app-sizer [(size)]="fontSizePx"></app-sizer>
```

**How two-way binding works**

For two-way data binding to work, the @Output() property must use the pattern, inputChange, where input is the name of the @Input() property. For example, if the @Input() property is size, the @Output() property must be sizeChange.

The following sizerComponent has a size value property and a sizeChange event. The size property is an @Input(), so data can flow into the sizerComponent. The sizeChange event is an @Output(), which allows data to flow out of the sizerComponent to the parent component.

Next, there are two methods, dec() to decrease the font size and inc() to increase the font size. These two methods use resize() to change the value of the size property within min/max value constraints, and to emit an event that conveys the new size value.

sizer.component.ts
```
export class SizerComponent {

  @Input()  size: number | string;
  @Output() sizeChange = new EventEmitter<number>();

  dec() { 
    this.resize(-1); 
  }
  
  inc()  { 
    this.resize(+1); 
   }

  resize(delta: number) {
    this.size = Math.min(40, Math.max(8, +this.size + delta));
    this.sizeChange.emit(this.size);
  }
}
```

The sizerComponent template has two buttons that each bind the click event to the inc() and dec() methods. When the user clicks one of the buttons, the sizerComponent calls the corresponding method. Both methods, inc() and dec(), call the resize() method with a +1 or -1, which in turn raises the sizeChange event with the new size value.

sizer.component.html
```
<div>
  <button (click)="dec()" title="smaller">-</button>
  <button (click)="inc()" title="bigger">+</button>
  <label [style.font-size.px]="size">FontSize: {{size}}px</label>
</div>
```

In the AppComponent (Other) template, fontSizePx is two-way bound to the SizerComponent.

app.component.html
```
<app-sizer [(size)]="fontSizePx"></app-sizer>
<div [style.font-size.px]="fontSizePx">Resizable Text</div>
```

In the AppComponent, fontSizePx establishes the initial SizerComponent.size value by setting the value to 16.

app.component.ts
```
fontSizePx = 16;
```
Clicking the buttons updates the AppComponent.fontSizePx. The revised AppComponent.fontSizePx value updates the style binding, which makes the displayed text bigger or smaller.

The two-way binding syntax is shorthand for a combination of property binding and event binding. The SizerComponent binding as separate property binding and event binding is as follows.

```
<app-sizer [size]="fontSizePx" (sizeChange)="fontSizePx=$event"></app-sizer>
```
The $event variable contains the data of the SizerComponent.sizeChange event. Angular assigns the $event value to the AppComponent.fontSizePx when the user clicks the buttons.


Example:

https://stackblitz.com/angular/qkknqlpxybjm?file=src%2Fapp%2Fapp.component.html

## Different Data binding
![event-binding.svg](images/data-binding-difference.png)