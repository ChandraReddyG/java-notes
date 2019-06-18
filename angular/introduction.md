# Angular

For writing a angular application we will need below frameworks installed on our machine.
* NPM (Node Package Manager) https://www.npmjs.com/
* Angular CLI (Auto generate boiler plate codes for us and also building, testing and deploying application) https://cli.angular.io/

## Modules

There are two types of modules
* ES 2015 Modules
* Angular Modules

### ES 2015 Modules
Anything we export is a module 

Export - product.ts
```
export class Product {
}
```

Import - product-list.ts
```
import {Product } from './product'
``` 

### Angular Module
angular by default has atleast one module called app module. each module has one or many component and one component is create and belongs to only one module.

### Difference between ES and Angular Modules
![module-difference.png](module-difference.png)

## Components
Component contains html template and data binding and it's class which contains properties and methods. Component also contains metatdata defined with decorators

```
import { Component } from '@angular/core';

@Component({
  selector: 'pm-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'Angular: Getting Started';
}

```

![component.png](component.png)

### Decorator
A function that adds metadata to a class, it's member or it's methods arguments. It's prefixed with @
Angular provides build in decorators. We can create our own component.

A decorator is a function and does not ends with semi colon and we can pass multiple parms to function that's why the syntax is 
```
@Component ({})
```
```
@Component ({selector:'root', template:'<div>Hello</div>'})
```
