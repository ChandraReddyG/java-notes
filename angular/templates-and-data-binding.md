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

## Two Way Binding

