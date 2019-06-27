# Omega

`Omega`, is an evolution towards Angular 8, of the well known https://grapesjs.com. 
It adds it a server-side, a distributed server-side, and makes it collaboration ready.
The server side IS distributed, as you'll see, and out-of-the box, just because `git` is, distributed.


# How it all started

As I was working on a software that had to send e-mails, based on beautiful templates. I found `MJML`, a markup language designed and standardized by `MailJet`, and I soon found a _wysiwyg_ `MJML` editor, `GrapesJS`.
`GrapesJS` allow non-technical users (that barely know `HTML`, or a bit of web design, typically), to edit new email templates for their mailing campaigns, all on their own.
That `MJML` editor was still is) a fork of [GrapesJS](https://grapesjs.com/), an `HTML` editor : 
* Indeed, my first need was to find out how (and where) my users would persist each new email templates (for a given mailing campaign)
* So digging into the `MJML` _wysiwyg_ editor's documentation, I really quickly ended up on [the GrapesJS `HTML` wysiwyg editor](https://grapesjs.com/) 's documentation. And found out : 
  * that both [GrapesJS](https://grapesjs.com/) 's `HTML` and `MJML` _wysiwyg_ editors were using a Javascript plugin mechanisms, that allows any developer to implement his own persistence module. 
  * That this persistence module allows the developer to define how and where the editor's content is to be persited.
  * That a couple of Persistence Module are provided by the [GrapesJS](https://grapesjs.com/) project team, like modules allowing to persist your edited template to firebase, cf. https://github.com/artf/grapesjs-firestore .

So starting from there I had one good idea, plus two goods reasons, to fork `GrapesJS`, and launch a new project :

* **[The good idea] Make GrapesJS Collaboration-Ready (bring in a Git-based default Persistence Module)** : Well I was first astonsihed to find out that there are no Git oriented persistence Module, SO here I go with A good Idea, cause now people would be able to collaborate / validate on a given new `MJML` / `HTML` template ! (`MJML as Code` / `HTML as Code` , or wasn't it code in the first place...?). That it not only makes `GrapeJS` Collaboration-Ready, but it makes it _distributed_. : The `GrapesJS` initial Designer, still today, has a side company proposing SAAS hosted `GrapesJS` offers, where using the Using the Git Persistence Module, each collaborator uses his own server (the `Omega` server), most of the time a docker container on his own machine, and the git repos to persist and share with collaborators.  
* **[good reason no. 1] Bring in Dependency Injection** it's a pure client app : no server there. Plus it's implemented in pure old fashioned JavaScript. So here there's an opportunity turning that into a pure `Angular 8 PWA` (Progressive Web App). If only that, for the sake of dependency injection (Thankfully, `GrapesJS` initial designer code relatively neat,but still, it's real old style JavaScript)
* **[good reason no. 2] Bring in a TDD Build Process** since it's pure JavaScript, it's like there are zero build steps, and zero build process at all, so about the test stack, ouch.. Here Another Reason to bring webpack and Karma / Mocha / Jest / Jasmine


# Omega in the guts

So : 

* `Omega` will be `GrapesJS` re-implemented in `Angular 8 / TypeScript`, plus a TypeScript server side  :
  * defining a clean build process, pipeline-ready, for both the client, and the server.
  * using dependency injection, especially to implement the Persistence Module plugin mechanism (with an `npm install omega-git-persistence-module@0.2.7`, and a configuration for the build)
* When a user editing an `HTML` or `MJML` template with `Omega` : 
  * clicks the `save` button, a dialog box prompts the user to fill in commit message, and then the template is saved to a git repo with a : 
  
  ```bash
  # The Omega user  doesn't know about the [/path/on/the/server] he just sees the file and directory 's tree inside [/path/on/the/server]. Plsu inside [/path/on/the/server] is where the git clone happened.
  export FILE_BEING_EDITED_WITH_OMEGA=/path/on/the/server/blackfriday-champions.mjml
  export COMMIT_MESSAGE="This is the text typed by the omega user, in the dialog box, through his web page, so angular app will fill that one up"
  git add $FILE_BEING_EDITED_WITH_OMEGA && git commit "$COMMIT_MESSAGE" && git push
  ```

Basically here, I soon understood that in order to actually have a Git Persistence Module, I HAD, to implement A RESTful API on a server, that the Angular App wil call :
* Indeed, If I want to persist to a git repo, and collaborate with others, I have to be able to git clone it in a filesystem, inside a system where the git software is installed. I would dream of an implementation of git inside our browsers, using a browser managed virutal filesystem, etc... 
* Before we have all that, in our daily web browsers, well the easiest and fastest way to execute git commands, for an Angular 8 App, is to ask a REST ful API (_the backend_, _the server_) to do it : 
  * we need a server with a RESTfull API, 
  * inside a container which has git installed, why not a `NodeJS / TypeScript / Swagger / https://github.com/steveukx/git-js ` stack, 
  * and that way the Angular App can _`git clone "FIR_REPO_URI" && git add --all && git commit -m "message from omega user" && git push`_, just out of calling an `OpenAPI`-compliant `RESTful API`.

# Tech Stack

* `NVM`, 
* `NodeJS`,
* `typescript`,
* [`SWAGGER` TO `TYPESCRIPT`] The swagger typescript generator : https://www.npmjs.com/package/swagger-typescript-codegen 
* [`TYPESCRIPT` TO `SWAGGER`] Re-generate `JSON` or `YAML` config file from Generated Source Code : https://www.npmjs.com/package/typescript-rest-swagger
* [**`typescript-rest` is ExpressJS' Typesript Extension, perfectly fit for `SWAGGER` `<=>` `TYPESCRIPT`**] : https://github.com/thiagobustamante/typescript-rest
* [**`RxJS/TypeScript`**] :  using rxjs installed with all modules, including those required for using `RxJS` in `TypeScript`, cf. https://rxjs.dev/guide/installation#all-module-types-cjs-es6-amd-typescript-via-npm

Ouh, and an `OCI` container engine, `Docker`. With `Docker Compose`.

# TODO List

For a start I'll decompose all tasks in this section of the projet 's root `README.md`, before task management / scheduling is managed by a dedicated tool.

* Implement a first RESTfull API Endpoint, using `typescipt-rest`, swagger generator, without any `RxJS`. This Endpoint will be a very simple allowing you to multiply any two positive or null integers.
* Add a new Endpoint, that basically returns a list of text files, among a given set of files, inside a given folder :
  * the endpoint will always return a map : 
    * key being the path of the file, relative to workspace root,
    * the mapped value being the actual files itself as binary content. The workspace is a folder inside the `OCI` container, in which the `RESTful API` runs.
    

