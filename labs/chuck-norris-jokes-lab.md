# Chuck Norris Joke Generator Lab

## Step 1

Launch a new [Angular Stackblitz Project](https://stackblitz.com/fork/angular)

## Step 2

* Add the following `DEPENDENCIES`:
  _ `@angular/material`
  _ `@angular/animations`

## Step 3

* Delete `app/hello.component.ts`
* Remove `HelloComponent` from `app/app.module.ts` `imports` and `import`

## Step 4

* Rename `styles.css` to `styles.scss`
* Update in `angular.json` all instances of `styles.css` to `styles.scss`
* Add the following to `styles.scss`:

    ```
    @import "~@angular/material/prebuilt-themes/deeppurple-amber.css";
    @import url("https://fonts.googleapis.com/icon?family=Material+Icons");

    * {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }
    ```

## Step 5

* Create a `jokes` Module
* Create `joke-card-item` Component in `jokes` module
* Create `joke-card-list` Component in `jokes` module
* Copy/Paste the following code into `jokes` module

	```
	import { NgModule } from '@angular/core';
	import { CommonModule } from '@angular/common';
	import { MatCardModule, MatProgressBarModule, MatIconModule } from '@angular/material';
	import { JokeCardItemComponent } from './joke-card-item/joke-card-item.component';
	import { JokeCardListComponent } from './joke-card-list/joke-card-list.component';
	
	@NgModule({
	  imports: [
	    CommonModule,
	    MatCardModule,
	    MatProgressBarModule,
	    MatIconModule
	  ],
	  declarations: [JokeCardItemComponent, JokeCardListComponent],
	  exports: [JokeCardItemComponent, JokeCardListComponent ]
	})
	export class JokesModule { }
	```

## Step 6

* Create `models` Folder in the `app` folder
* Create `joke` interface inside `app/models` folder with the following details:

  ```
  export interface Joke {
  id: string;
  joke: string;
  }
  ```

* Create `joke-result` interface inside `app/models` folder with the following details:

  ```
  import { Joke } from './joke'

  export interface JokeResult {
  type: string;
  value: Joke[];
  }
  ```

* Create `index.ts` file in `app/models` folder and export the two models as follows:

  ```
  export * from './joke';
  export * from './joke-result';
  ```

## Step 7

* Create `services` folder in `app` folder
* Create a `joke` service in the `app/services` folder

  ```
  import { HttpClient } from '@angular/common/http';
  import { Injectable } from '@angular/core';
  import { Observable } from 'rxjs';
  import { map } from 'rxjs/operators';
  import { Joke, JokeResult } from '../models';
  @Injectable()
  export class JokeService {
    private API_BASE_URL = 'https://api.icndb.com';
    constructor(private http: HttpClient) {} 
    getJokes(): Observable<Joke[]> {
      return this.http
        .get<JokeResult>(
          `${this.API_BASE_URL}/jokes/random/5?escape=javascript&limitTo=[nerdy]`
        )
        .pipe(map(result => result.value));
    }
  }
  ```

* Create `index.ts` file inside `app/services` folder and export the service
  `export * from './joke.service';`

## Step 8

* Export the Joke Card Item and Joke Card List Components from the `jokes.module`

  ```
  @NgModule({
  ...
  exports: [JokeCardItemComponent, JokeCardListComponent]
  })
  ...
  ```

* Add the Joke Card List to `app.component` html template:
  `... <app-joke-card-list></app-joke-card-list>`

* Delete this line from `app.component`
	`... <hello name="{{ name }}"></hello>`

## Step 9

* Import `JokesModule` and `HttpClientModule` into `app.module`

	```
	import { JokesModule } from './jokes/jokes.module';
	import { HttpClientModule } from '@angular/common/http';

	@NgModule({
  imports:      [ BrowserModule, FormsModule, JokesModule, HttpClientModule ],
	
	```

## Step 10

* Replace `joke-card-list.component.ts` with the following:

  ```
	import { Component, OnInit } from '@angular/core';
	import { Observable } from 'rxjs';
	import { Joke } from '../../models';
	import { JokeService } from '../../services';

	@Component({
	  selector: 'app-joke-card-list',
	  templateUrl: './joke-card-list.component.html',
	  styleUrls: ['./joke-card-list.component.css']
	})
	export class JokeCardListComponent implements OnInit {
	  jokes$: Observable<Joke[]>;
	  constructor(private jokeService: JokeService) {} 
	  ngOnInit() {
	    this.jokes$ = this.jokeService.getJokes();
	  }
	  refreshJokes() {
	      this.jokes$ = this.jokeService.getJokes();
	  }
	}
  ```

* Update `joke-card-list.component.html` with the following:
  ```html
  <h1>Chuck Norris Jokes</h1>

    	<div *ngIf="!(jokes$ | async)">
    	  <mat-progress-bar mode="indeterminate"></mat-progress-bar>
    	</div	
    	<div *ngIf="jokes$ | async as jokes">
    	  <div>
    	    <button mat-fab (click)="refreshJokes()">
    	      <mat-icon>refresh</mat-icon>
    	    </button>
    	  </div>
    	  <app-joke-card-item *ngFor="let joke of jokes" [joke]="joke"></app-joke-card-item>
    	</div>
  ```

## Step 11

* Update `joke-card-item.component.html` with the following:

  ```html
  <mat-card>
    <mat-card-title>Joke #{{ joke.id }}</mat-card-title>
    <mat-card-content>{{ joke.joke }}</mat-card-content>
  </mat-card>
  ```

* Update `joke-card-item.component.css` with the following:

  ```
  :host {
    padding: 15px;
    margin: 15px;
  }
  ```

* Add an input named `joke` of type `Joke` to the `joke-card-item.component.ts` as follows:

  ```
  ...
  @Input() joke: Joke;
  ...
  constructor() {}
  ```
