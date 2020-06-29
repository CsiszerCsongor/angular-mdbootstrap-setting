# angular-mdbootstrap-setting
Angular 7 MDBootstrap beállítása

A videóban (https://www.youtube.com/watch?v=m68xvSiuijE) láttottak alapján létrehoztam egy projektet, viszont elindításkor hibába ütköztem. Ebben kérek segítséget.

## A lépések, amikkel létrehoztam a projektet:
1. `ng new frontend`
2. `cd frontend`
3. `npm i --save angular-bootstrap-md chart.js@2.5.0 font-awesome hammerjs`
4. Megnyitom az editort és az `app.module.ts` fájlba beírom a következőket
  - Importálom az `MDBBootstrapModule`-t
    - ``` 
          import { NgModule, NO_ERRORS_SCHEMA } from '@angular/core';
          import { MDBBootstrapModule } from 'angular-bootstrap-md'; 
      ```
  - Az `imports: [...]` property-be, hozzáadom az importált `MDBBootstrapModule`-t: ``` MDBBootstrapModule.forRoot() ```
  - Hozzáadok egy új property-t az NgModule-hoz: ``` schemas: [NO_ERRORS_SCHEMA], ```
5. Megnyitom az `angular.json` fájlt
  - A `"styles:"` és a `"script:"` property-ket kibővítem
    - ```
      "styles": [
              "./node_modules/font-awesome/scss/font-awesome.scss",
              "./node_modules/angular-bootstrap-md/assets/scss/bootstrap/bootstrap.scss",
              //"./node_modules/angular-bootstrap-md/assets/scss/mdb-free.scss", - ezt valamért nem töltötte le
              "src/styles.scss"
            ],
            "scripts": [
              "./node_modules/chart.js/dist/Chart.js",
              "./node_modules/hammerjs/hammer.min.js"
            ],
    ```
    - A fenti 
