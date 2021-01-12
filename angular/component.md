# Components are the main building block for Angular applications. Each component consists of:

* An HTML template that declares what renders on the page
* A Typescript class that defines behavior
* A CSS selector that defines how the component is used in a template
* Optionally, CSS styles applied to the template

![component.png](images/component.png)

## Creating a component using the Angular CLI
To create a component using the Angular CLI:

From a terminal window, navigate to the directory containing your application.
Run the ng generate component <component-name> command, where <component-name> is the name of your new component.
By default, this command creates the following:

* A folder named after the component
* A component file, <component-name>.component.ts
* A template file, <component-name>.component.html
* A CSS file, <component-name>.component.css
* A testing specification file, <component-name>.component.spec.ts

Where <component-name> is the name of your component.

Example component
```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.less']
})
export class AppComponent {
  title = 'konnect-test';
}

```

![component-explain.png](images/component-explain.png)

* Decorators mark their type and provide metadata that tells Angular how to use them


