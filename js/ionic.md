---
---

# Angular & Ionic

AWS Amplify helps developers to create high-quality Angular and Ionic apps quickly by handling the heavy lifting of configuring and integrating cloud services behind the scenes. It also provides a powerful high-level API and ready-to-use security best practices.

## Installation

AWS Amplify provides Angular Components that you can use in [aws-amplify-angular](https://www.npmjs.com/package/aws-amplify-angular) npm package.

Install `aws-amplify` and `aws-amplify-angular` npm packages into your Angular app.

```bash
$ npm install --save aws-amplify
$ npm install --save aws-amplify-angular
```

## Setup the AWS Backend

To configure your Angular app for AWS Amplify, you need to create a backend configuration with Amplify CLI and import the auto-generated configuration file into your project. 

Following commands will enable Auth category and will create `aws-exports.js` configuration file under your projects `/src` folder. 

```bash
$ npm install -g @aws-amplify/cli
$ amplify init
$ amplify add auth
$ amplify add storage
$ amplify push # Updates your backend
```

Please visit [Authentication Guide]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/authentication)  and [Storage Guide]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/storage) to learn more about enabling these categories.
{: .callout .callout--info}


A configuration file is placed inside your configured source directory. To import the configuration file to your Angular app, you may need to rename `aws-exports.js` to `aws-exports.ts`. You can setup your `package.json` npm scripts to rename the file for you, so that any configuration changes which result in a new generated `aws-exports.js` file get changed over to the `.ts` extension.

## Import and Configure Amplify

Import the configuration file and configure Amplify in your `main.ts` file. 

```javascript
import Amplify from 'aws-amplify';
import amplify from './aws-exports';
Amplify.configure(amplify);
```

In your [home page component](https://angular.io/guide/bootstrapping) `src/app/app.module.ts`, you can import Amplify modules as following:

```javascript
import { AmplifyAngularModule, AmplifyService } from 'aws-amplify-angular';

@NgModule({
  ...
  imports: [
    ...
    AmplifyAngularModule
  ],
  ...
  providers: [
    ...
    AmplifyService
  ]
  ...
});
```

### AmplifyService Angular Provider

`AmplifyService` will pull the core services of the Amplify JS library and will make them available to your application as a provider.  This will cause dependencies of the various Amplify modules to be included in your app's bundle.  

If you want to configure the `AmplifyService` using only a subset of Amplify modules - and thus limit included dependencies and bundle size impact - you can use the `AmplifyModules` helper within Angular's `useFactory` provider attribute.

To do this, you must:

a) Make sure that `main.ts` is import Amplify via `@aws-amplify/core` and not `aws-amplify`.  
b) Import `AmplifyModules` from `aws-amplify-angular`;
c) Import the Amplify modules that you want to include in the provider;
d) Pass the modules to the AmplifyModules function in the useFactory attribute;

For example, if you want to use only Auth and Storage you could do the following in `app.module.ts`:

```javascript
import { AmplifyAngularModule, AmplifyService, AmplifyModules } from 'aws-amplify-angular';
import Auth from '@aws-amplify/auth';
import Storage from '@aws-amplify/storage';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AmplifyAngularModule
  ],
  providers: [
    {
      provide: AmplifyService,
      useFactory:  () => {
        return AmplifyModules({
          Auth,
          Storage
        });
      }
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Using the AWS Amplify API

AmplifyService is a provider in your Angular app, and it provides AWS Amplify core categories through dependency injection. To use *AmplifyService* with [dependency injection](https://angular.io/guide/dependency-injection-in-action), inject it into the constructor of any component or service, anywhere in your application.

```javascript
import { AmplifyService } from 'aws-amplify-angular';

...
constructor(
    public navCtrl:NavController,
    public amplifyService: AmplifyService,
    public modalCtrl: ModalController
) {
    this.amplifyService = amplifyService;
}
...
```

You can access and work directly with AWS Amplify Categories via the built-in service provider:

```javascript
import { Component } from '@angular/core';
import { AmplifyService }  from 'aws-amplify-angular';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

export class AppComponent {
  
  constructor( public amplify:AmplifyService ) {
      
      this.amplifyService = amplify;
      
      /** now you can access category APIs:
       *
       * this.amplifyService.auth();          // AWS Amplify Auth
       * this.amplifyService.analytics();     // AWS Amplify Analytics
       * this.amplifyService.storage();       // AWS Amplify Storage
       * this.amplifyService.api();           // AWS Amplify API
       * this.amplifyService.cache();         // AWS Amplify Cache
       * this.amplifyService.pubsub();        // AWS Amplify PubSub
       * this.amplifyService.interactions();  // AWS Amplify Interactions
       *     
      **/
  }
  
}
```

You can access all [AWS Amplify Category APIs](https://aws-amplify.github.io/amplify-js/media/developer_guide) with *AmplifyService*. 

## Subscribe to Authentication State Changes

Import AmplifyService into your component and listen for auth state changes:

```javascript
import { AmplifyService }  from 'aws-amplify-angular';

  // ...
constructor( public amplifyService: AmplifyService ) {

    this.amplifyService = amplifyService;

    this.amplifyService.authStateChange$
        .subscribe(authState => {
            this.signedIn = authState.state === 'signedIn';
            if (!authState.user) {
                this.user = null;
            } else {
                this.user = authState.user;
                this.greeting = "Hello " + this.user.username;
            }
        });

}
```

## Use View Components

AWS Amplifies provides components that you can use in your Angular view templates. 

### Authenticator

The Authenticator component creates an out-of-the-box signing/sign-up experience for your Angular app. 

Before using this component, please be sure that you have activated [Authentication category](https://aws-amplify.github.io/amplify-js/media/authentication_guide):
```bash
$ amplify add auth
```


To use Authenticator, just add the `amplify-authenticator` directive in your .html view:
```html
  ...
  <amplify-authenticator></amplify-authenticator>
  ...
```

### Photo Picker

Photo Picker component will render a file upload control that will allow choosing a local image and uploading it to Amazon S3. Once an image is selected, a base64 encoded image preview will be displayed automatically.

Before using this component, please be sure that you have activated [*user-files* with Amplify CLI](https://docs.aws.amazon.com/aws-mobile/latest/developerguide/aws-mobile-cli-reference.html):

```bash
$ amplify add storage
```

To render photo picker in an Angular view, use *amplify-photo-picker* component:

```html
<amplify-photo-picker
    (picked)="onImagePicked($event)"
    (loaded)="onImageLoaded($event)">
</amplify-photo-picker>
```

The component will emit two events:

 - `(picked)` - Emitted when an image is selected. The event will contain the `File` object which can be used for upload.
 - `(loaded)` - Emitted when an image preview has been rendered and displayed.
 - `path` - An optional S3 image path (prefix).
 - `storageOptions` - An object passed within the ‘options’ property in the Storage.put request. This can be used to set the permissions ‘level’ property of the objects being uploaded i.e. ‘private’, ‘protected’, or ‘public’.  
 
 [Learn more about S3 permissions.]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/storage#file-access-levels).


### S3 Album

S3 Album component display a list of images from the connected S3 bucket.

Before using this component, please be sure that you have activated [*user-files* with Amplify CLI](https://docs.aws.amazon.com/aws-mobile/latest/developerguide/aws-mobile-cli-reference.html):

```bash
$ amplify add storage
```

To render the album, use *amplify-s3-album* component in your Angular view:

```html
<amplify-s3-album  
    path="pics" (selected)="onAlbumImageSelected($event)">
</amplify-s3-album>
```

- `options` - object which is passed as the 'options' parameter to the .get request.  This can be used to set the 'level' of the objects being requested (i.e. 'protected', 'private', or 'public'
- `(selected)` event can be used to retrieve the URL of the clicked image on the list:

```javascript
onAlbumImageSelected( event ) {
      window.open( event, '_blank' );
}
```

### Interactions

The `amplify-interactions` component provides you with a Chatbot user interface. You can pass it three parameters:

1. `bot`:  The name of the Amazon Lex Chatbot

2. `clearComplete`:  A flag indicating whether or not the messages should be cleared at the
end of the conversation.

3. `complete`: A function that is executed when the conversation is completed.

```html
<amplify-interactions bot="yourBotName" clearComplete="true" (complete)="onBotComplete($event)"></amplify-interactions>
```

See the [Interactions documentation]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/interactions) for information on creating an Amazon Lex Chatbot.

### XR

#### Sumerian Scene

The `amplify-sumerian-scene` component provides you with a prebuilt UI for loading and displaying Amazon Sumerian scenes inside of your website:

{% include_relative common/scene-size-note.md %}

```javascript
// sceneName: the configured friendly scene you would like to load
<amplify-sumerian-scene sceneName="scene1" ></amplify-sumerian-scene>
```

See the [XR documentation]({%if jekyll.environment == 'production'%}{{site.amplify.docs_baseurl}}{%endif%}/js/xr) for information on creating and publishing a Sumerian scene.

### Styles

To use the aws-amplify-angular components you will need to install '@aws-amplify/ui'.

Add the following to your styles.css file to use the default styles:
```@import '~aws-amplify-angular/theme.css';```

You can use custom styling for AWS Amplify components. Just import your custom *styles.css* that overrides the default styles which can be found in `/node_modules/aws-amplify-angular/theme.css`.

## Ionic

The Angular components described in this section each have corresponding
Ionic components that can be used in an Ionic 4+ app.  The Ionic components combine the functionality and much of the styling of the Angular components with standard Ionic elements.

The Ionic elements can be accessed in two ways:

1) By passing `framework="ionic"` to the component markup.  For example, to use the Ionic version of the Authenticator component, you can use `<amplify-authenticator framework="ionic"></amplify-authenticator>`.

2) By using the Ionic-specific tag.  For example: `<amplify-authenticator-ionic></amplify-authenticator-ionic>`. 

## Angular 6 Support

Currently, the newest version of Angular (6.x) does not provide the shim for the  `global` object, which was provided in previous versions. Specific AWS Amplify dependencies rely on this shim.  While we evaluate the best path forward to address this issue, you have a couple of options for re-creating the shim in your Angular 6 app to make it compatible with Amplify.

1.  Add the following to your polyfills.ts: `(window as any).global = window;`.

2.  Add the following script to your index.html `<head>` tag:
``` 
    <script>
        if (global === undefined) {
            var global = window;
        }
    </script>
  ```
