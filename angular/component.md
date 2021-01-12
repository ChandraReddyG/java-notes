# Components are the main building block for Angular applications. Each component consists of:

* An HTML template that declares what renders on the page
* A Typescript class that defines behavior
* A CSS selector that defines how the component is used in a template
* Optionally, CSS styles applied to the template

The @Component decorator identifies the class immediately below it as a component class, and specifies its metadata.

The metadata for a component tells Angular where to get the major building blocks that it needs to create and present the component and its view. In particular, it associates a template with the component, either directly with inline code, or by reference. Together, the component and its template describe a view.

In addition to containing or pointing to the template, the @Component metadata configures, for example, how the component can be referenced in HTML and what services it requires.

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

## Data binding
Without a framework, you would be responsible for pushing data values into the HTML controls and turning user responses into actions and value updates. Writing such push and pull logic by hand is tedious, error-prone, and a nightmare to read, as any experienced front-end JavaScript programmer can attest.

Angular supports two-way data binding, a mechanism for coordinating the parts of a template with the parts of a component. Add binding markup to the template HTML to tell Angular how to connect both sides.

The following diagram shows the four forms of data binding markup. Each form has a direction: to the DOM, from the DOM, or both.

<div class="lightbox">
  <img src="images/databinding.png" alt="Data Binding" class="left">
</div>

## Pipes
Angular pipes let you declare display-value transformations in your template HTML. A class with the @Pipe decorator defines a function that transforms input values to output values for display in a view.

Angular defines various pipes, such as the date pipe and currency pipe; for a complete list, see the Pipes API list. You can also define new pipes.

To specify a value transformation in an HTML template, use the pipe operator (|).

{{interpolated_value | pipe_name}}


## Directives
Angular templates are dynamic. When Angular renders them, it transforms the DOM according to the instructions given by directives. A directive is a class with a @Directive() decorator.

A component is technically a directive. However, components are so distinctive and central to Angular applications that Angular defines the @Component() decorator, which extends the @Directive() decorator with template-oriented features.

In addition to components, there are two other kinds of directives: structural and attribute. Angular defines a number of directives of both kinds, and you can define your own using the @Directive() decorator.

Just as for components, the metadata for a directive associates the decorated class with a selector element that you use to insert it into HTML. In templates, directives typically appear within an element tag as attributes, either by name or as the target of an assignment or a binding.

## Attribute directives
Attribute directives alter the appearance or behavior of an existing element. In templates they look like regular HTML attributes, hence the name.

The ngModel directive, which implements two-way data binding, is an example of an attribute directive. ngModel modifies the behavior of an existing element (typically <input>) by setting its display value property and responding to change events.

