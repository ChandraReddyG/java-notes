# Angular Commands
Common angular commands

## Install Angular CLI
npm install -g @angular/cli@latest

## Create Angular Project
ng new sample-app

## Start development server
ng serve

## Install Bootstrap and FontAwesome
npm install bootstrap font-awesome

### Include css files in styles.css
@import "~bootstrap/dist/css/bootstrap.css";

@import "~font-awesome/css/font-awesome.css";

## Create Module
ng generate module ui --module app.module

Inside the @NgModule -> exports array we add a reference to the components

exports: [LayoutComponent]

## Create Component
ng generate component ui/layout
