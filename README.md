# angular-mdbootstrap-setting
Angular 7 MDBootstrap beállítása

A videóban (https://www.youtube.com/watch?v=m68xvSiuijE) látottak alapján létrehoztam egy projektet, viszont elindításkor hibába ütköztem. Ebben kérek segítséget.

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
  - A fenti részben az mdb-free.scss-t nem töltötte le, de nem tudom miért.
- Ha ezekkel a beállításokkal elindítom az angular projektet, akkor a következő hibaüzenetet kapom:
    - `ERROR in No NgModule metadata found for 'AppModule'.`

Kérdésem, hogy ha ugyanazokat a lépéseket megcsináltam mint a videóban, akkor miért kapok én hibát? Mit kell még beállítsak?

# [Megoldásom]
Az alább felsorolt lépéseket végeztem el, hogy végül hiba nélkül lefusson az `ng serve` parancs.
A fentebb írt hiba: `ERROR in No NgModule metadata found for 'AppModule'.` a leírtak alapján azért kaptam, mert fel kellett cseréljem a sorrendet a `tsconfig.json`-ban a hozzáadott "include" property-ben(bug lehet...talán): (https://stackoverflow.com/a/50285039)
```
Át kellet írjam erről:
    "include": ["node_modules/angular-bootstrap-md/**/*.ts", "src/**/*.ts"]
erre:
    "include": ["src/**/*.ts", "node_modules/angular-bootstrap-md/**/*.ts"]
```

Ezután már más hibákat írt ki:
```
ERROR in node_modules/angular-bootstrap-md/lib/free/buttons/checkbox.directive.d.ts(19,19): error TS1086: An accessor cannot be declared in an ambient context.
node_modules/angular-bootstrap-md/lib/free/buttons/checkbox.directive.d.ts(20,19): error TS1086: An accessor cannot be declared in an ambient context.
node_modules/angular-bootstrap-md/lib/free/buttons/radio.directive.d.ts(20,9): error TS1086: An accessor cannot be declared in an ambient context.
node_modules/angular-bootstrap-md/lib/free/cards/mdb-card-body.component.d.ts(6,9): error TS1086: An accessor cannot be declared in an ambient context.
...
```

Ezen a stackoverflow oldalon az olvasható, hogy update-lni kell az angular core-t és a cli-t, mivel nagyobb verzió számú volt egy dependency, mint megengedhető lett volna az adott angular verzióhoz képest(érdekes, hogy nekem viszont nem volt megadva `@angular/cdk` a `package.json` fájlban, de gondoltam kipróbálom, lássam mi változik):
```
ng update --next @angular/cli --force
```
Ezzel a paranccsal updatelődtek a verziók:
```
    Updating package.json with dependency @angular/cli @ "10.0.0" (was "7.3.10")...
    Updating package.json with dependency @angular/compiler-cli @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular-devkit/build-angular @ "0.1000.0" (was "0.13.10")...
    Updating package.json with dependency @angular/language-service @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/compiler @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/common @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/animations @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/forms @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/core @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/platform-browser @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/platform-browser-dynamic @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency @angular/router @ "10.0.1" (was "7.2.16")...
    Updating package.json with dependency rxjs @ "6.5.5" (was "6.3.3")...
    Updating package.json with dependency typescript @ "3.9.5" (was "3.7.5")...
    Updating package.json with dependency zone.js @ "0.10.3" (was "0.8.29")...
```
Oké, most ahogy befejezte az update-t egy újabb probléma ütötte fel a fejét(az update parancs console kimenetének végén volt olvasható):
`Error: error TS100: angular-bootstrap-md\angular-bootstrap-md.ts(13,13): Error during template compile of 'ModalModule'
              Could not resolve @angular/cdk/a11y relative to [object Object]..`
Futtattam egy `ng serve` parancsot, lássam, akkor is mit ad ki. Két error volt, de ezektől függetlenül elindult az alkalmazás a 4200-as porton:

```
1. Schema validation failed with the following errors:
    Data path "" should NOT have additional properties(es5BrowserSupport).
  
illetve:  
  
2. ERROR in The target entry-point "angular-bootstrap-md" has missing dependencies:
    - @angular/cdk/a11y
```

Az 1. error-ra a megoldás itt olvasható: https://stackoverflow.com/a/56865388 Az `angular.json` fájlban ki kellett venni a ,`"es5BrowserSupport": true` property-t.

A 2. error-a a megoldás itt olvasható : https://github.com/angular/components/issues/8306#issuecomment-342852641 . Hiányzott `@angular/cdk` csomag. Erre a következő parancsot írtam be:
```
npm install --save @angular/cdk
```
Majd futtattam az angulart: `ng serve`
Most megint egy újabb hibába ütköztem:
```
ERROR in node_modules/@types/node/index.d.ts:73:11 - error TS2300: Duplicate identifier 'IteratorResult'.

73 interface IteratorResult<T> { }
             ~~~~~~~~~~~~~~

  node_modules/typescript/lib/lib.es2015.iterable.d.ts:41:6
    41 type IteratorResult<T, TReturn = any> = IteratorYieldResult<T> | IteratorReturnResult<TReturn>;
            ~~~~~~~~~~~~~~
    'IteratorResult' was also declared here.
```
Két library "összeakadt". Erre rákeresve kaptam (mivel nem volt időm tűzetesen átnézni, hogy mi lehet ennek a hátterében, ezért nem tudom leírni, hogy miért épp ezt a parancsot kellett, viszont működött ezek után. Annyit tudok, hogy a TypeScript declaration fájljaihoz lehet köze ) - ( https://stackoverflow.com/a/57984660 ) :
```
    npm install --save-dev @types/node
```
De mivel ezzel is ugyanazt a hibát kaptam, a kommentek között tovább kutakodva azt láttam, hogy a `@type/node`-nak a verziószámát kell növelnem:
```
    npm update --save-dev @types/node
```
Az utolsó alternatívája, hogy beszúrom a `tsconfig.json` fájlba ezt a sort: `"skipLibCheck": true` ( https://stackoverflow.com/a/60090933 )

## És ezután már sikeresen lefordult nekem. 
