# Introduction to modules

Angular apps are modular and Angular has its own modularity system called NgModules. NgModules are containers for a cohesive block of code dedicated to an application domain, a workflow, or a closely related set of capabilities. They can contain components, service providers, and other code files whose scope is defined by the containing NgModule. They can import functionality that is exported from other NgModules, and export selected functionality for use by other NgModules.

**The prefix ng stands for "Angular;" all of the built-in directives that ship with Angular use that prefix**

Every Angular app has at least one NgModule class, the root module, which is conventionally named AppModule and resides in a file named app.module.ts. You launch your app by bootstrapping the root NgModule.

While a small application might have only one NgModule, most apps have many more feature modules. The root NgModule for an app is so named because it can include child NgModules in a hierarchy of any depth.

Sample module 

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

The @NgModule decorator identifies AppModule as an NgModule class. @NgModule takes a metadata object that tells Angular how to compile and launch the application.

* declarations : The module's declarations array tells Angular which components belong to that module. As you create more components, add them to declarations.
* imports—import: BrowserModule to have browser specific services such as DOM rendering, sanitization, and location.
* providers—the service providers.
* bootstrap—the root component that Angular creates and inserts into the index.html host web page.

## declarations
The module's declarations array tells Angular which components belong to that module. As you create more components, add them to declarations.

You must declare every component in exactly one NgModule class. If you use a component without declaring it, Angular returns an error message.

The declarations array only takes declarables. Declarables are components, directives and pipes. All of a module's declarables must be in the declarations array. Declarables must belong to exactly one module. The compiler emits an error if you try to declare the same class in more than one module.

These declared classes are visible within the module but invisible to components in a different module unless they are exported from this module and the other module imports this one.

```
declarations: [
  YourComponent,
  YourPipe,
  YourDirective
],
```

### Using directives with @NgModule
Use the declarations array for directives. To use a directive, component, or pipe in a module, you must do a few things:

* Export it from the file where you wrote it.
* Import it into the appropriate module.
* Declare it in the @NgModule declarations array.

Those three steps look like the following. In the file where you create your directive, export it. The following example, named ItemDirective is the default directive structure that the CLI generates in its own file, item.directive.ts:

Example - item.directive.ts
```
import { Directive } from '@angular/core';

@Directive({
  selector: '[appItem]'
})
export class ItemDirective {
// code goes here
  constructor() { }

}
```

