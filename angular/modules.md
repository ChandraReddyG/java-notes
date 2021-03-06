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
* imports : The module's imports array appears exclusively in the @NgModule metadata object. It tells Angular about other NgModules that this particular module needs to function properly.
* providers: The providers array is where you list the services the app needs. When you list services here, they are available app-wide. You can scope them when using feature modules and lazy loading.
* bootstrap : The application launches by bootstrapping the root AppModule, which is also referred to as an entryComponent. Among other things, the bootstrapping process creates the component(s) listed in the bootstrap array and inserts each one into the browser DOM.

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

# Frequently-used modules

An Angular app needs at least one module that serves as the root module.
As you add features to your app, you can add them in modules.
The following are frequently used Angular modules with examples
of some of the things they contain:


<table>

 <tr>
   <th style="vertical-align: top">
     NgModule
   </th>

   <th style="vertical-align: top">
     Import it from
   </th>

   <th style="vertical-align: top">
     Why you use it
   </th>
 </tr>

 <tr>
   <td><code>BrowserModule</code></td>
   <td><code>@angular/platform-browser</code></td>
   <td>When you want to run your app in a browser</td>
 </tr>

 <tr>
   <td><code>CommonModule</code></td>
   <td><code>@angular/common</code></td>
   <td>When you want to use <code>NgIf</code>, <code>NgFor</code></td>
 </tr>

 <tr>
   <td><code>FormsModule</code></td>
   <td><code>@angular/forms</code></td>
   <td>When you want to build template driven forms (includes <code>NgModel</code>)</td>
 </tr>

 <tr>
   <td><code>ReactiveFormsModule</code></td>
   <td><code>@angular/forms</code></td>
   <td>When you want to build reactive forms</td>
 </tr>

 <tr>
   <td><code>RouterModule</code></td>
   <td><code>@angular/router</code></td>
   <td>When you want to use <code>RouterLink</code>, <code>.forRoot()</code>, and <code>.forChild()</code></td>
 </tr>

 <tr>
   <td><code>HttpClientModule</code></td>
   <td><code>@angular/common/http</code></td>
   <td>When you want to talk to a server</td>
 </tr>

</table>
