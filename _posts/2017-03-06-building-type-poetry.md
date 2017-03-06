---
layout: post
title: Building 'Type Poetry'
---
This blog post will outline how I started with an idea for a web app, and followed it into [full production and deployment](http://typepoetry.com/).

## Idea and Architecture
Seeking to further my web development skills, I wanted to create a new web app while integrating new technologies and industry methods. Due to my interest in poetry and education, I found the idea of *'retyping the greats'* of poetry very enticing. The initial concept was a web app where users could select classic poems, and retype them in a minimal environment, similar to learning devices for beginning typists.

I decided on using the MEAN stack (MongoDB, Express, Angular, Node) from the beginning, due to my experience with it and the need for all the features that come with it. I wanted a lightweight database to potentially store user information or poem data and client side rendering. 

I searched for datasets/APIs for public domain poetry, but ended up deciding on [PoetryDB](http://poetrydb.org/index.html). This is mainly due to it being the only free API I could find and the general ease of use.  However, there were a few issues that arose that I will cover during the implementation section. With the API decided, I began planning the system architecture for the application. 

![Application architecture]({{site.baseurl}}/assets/typoetry-diagram.png)

This architecture shows a wrapper REST API that serves 2 purposes:
* Accessing the PoetryDB API with web calls
* Accessing the MongoDB database using Mongoose as an interface

The Angular application then uses this wrapper API to get the appropriate data to display to the user.

## Implementation

### Starting Out

My first approach was to use the `express` generator to build a quick boilerplate application that I could easily extend. I then refactored this to a directory structure that I am more comfortable working with:

```
├── app_api
│   ├── controllers
│   └── routes
├── app_client
├── app_server
│   ├── routes
│   └── views
├── bin
└── public
    └── stylesheets

```
Using this structure, `app_client` deals with all the Angular components of the application, `app_server` handles the Express side, and `app_api` separates the wrapper API from the rest of the application. 

I started by defining basic routes to the PoetryDB API in my wrapper API - such as 'GET all poems' and 'GET all authors'. I then created the main Angular application and created routes for all poems and a specific poem. If I were to re-do this process with my new knowledge, I would almost certainly create **unit tests** for the API, instead of manually testing. 

In the view for the specific poem my initial approach was to create the main functionality of the application, that being the 'typing' component where a user can type along with a poem. For this, I used a simple implementation of two overlaying HTML `input` fields. This was achieved with CSS styles and placeholders - one input has a `background: none` style and another with a placeholder of the whole line. Angular's `ng-repeat` was essential for this, as it allowed me to retrieve the poem's data in JSON format from the API and then iterate through it, setting attributes where necessary. 

With basic pagination in place from [`angularUtils/pagination`](https://github.com/michaelbromley/angularUtils/tree/master/src/directives/pagination), I had routes for all poems, all authors, and a specific poem. However, the application was looking very cluttered and there was a lot of re-use, which provided a good point for me to try out new development techniques.

### Optimizing 

My first order of business was to refacstor all API calls into a factory and inject it into all controllers. This way I was following best practices for Angular by separating the view from the business logic, while also creating a neater application.

The next step was implementing a proper task runner in Gulp. To get started, I watched a fair bit of John Papa's [Pluralsight course](https://app.pluralsight.com/courses/javascript-build-automation-gulpjs). This was my first proper foray into task runners, only previously using it with wiredep for injecting dependencies. 

These were the tasks I created using Gulp:
* Vetting (JSHint and JSCS)
* Google Fonts downloader
* Injection (Wiredep and custom CSS)
* Serve (Development and Build environments)
* Optimization (Cleaning, Minification, Template Caching into a Build folder)

In the end, making these tasks saved me a tremendous amount of time when developing and made it substantially easier to install new dependencies. 

The next new technology I added to my development was a CSS preprocessor, **Sass**. Previously I had been writing my custom CSS in a single file, which was not very maintainable or helpful. Because of this, and to be up to date with industry standards, I decided to implement Sass into this project. 

```
public/stylesheets
├── abstracts
├── base
├── components
├── fonts.css
├── layout
├── main.css
├── main.scss
└── pages
```
This was the architecture structure I went for - a slight modification of the suggested 7-1 Pattern from [Sass Guidelines](https://sass-guidelin.es). The separation of partials proved very useful as I added components and variables into my styles, with the `main.scss` file importing all partials through globbing. 

One of the most useful additions was the **breakpoint manager** suggested in the guidelines. This provided a simple mixin that allowed me to quickly create media queries, while being able to change the breakpoints globally at any time. 

### Starting Styling

With these optimizations and new methods in place, I then worked on the basic styles for the home page and poem page. In hindsight, my method for this was not great - I developed starting from the **largest breakpoint** as opposed to **mobile first**. After completing this project, I watched Udacity's [Responsive Web Design course](https://www.udacity.com/course/responsive-web-design-fundamentals--ud893) which showed me a lot of mistakes I made on this project.

![Home]({{site.baseurl}}/assets/typoetry-home.png)

This home 'info card' design was creating using negative margins, which may have been a bit of a hacky implementation. This project also taught me a lot about color schemes, where I used [this article](https://www.smashingmagazine.com/2016/04/web-developer-guide-color/) to create a cohesive palette with harmonious grays.

### Problems

One of the problems I alluded to before was characters that could not be typed by the user appearing in poems. The PoetryDB API would return lines with characters like '—' and '’', which are generally not on the average user's keyboard, if any at all. 
’
My solution to this was extremely simple, please message me if there was a more elegant solution to this that I am unaware of! As you can see below, it is just a series of `replace` methods that occurs in a map function.

```
var trimmedLines = vm.poem.lines.map(function(s) {
	return s
		.trim()
		.replace('—', '--')
		 replace('’', '\'');
 });
```

The second problem that arose was those same characters being present in the URL/title of the poem. This would lead to situations where the symbols were not being encoded by the request. 

```
var urlString = 'http://poetrydb.org/title/' + encodeURIComponent(req.params.title) + ':abs';
```

The fix here was a lot more simple, this handy JavaScript global function encodes the special characters, which fixed the problems where the RESTful GET request would not interpret the URI correctly.

A final, frustrating issue was inconsistencies in **textarea** element's across browsers. My initial attempts were using small padding/margin hacks to align them, but this soon proved futile and an overall bad practice. After some searching, I found `vertical-align: top;` to be sufficient for consistent rendering in Chrome and Firefox. 

### Deployment

Once the application was finished with random generation, audio and proper functionality, I was ready to deploy it for production. If I were to re-do this process, I would include Karma tests for this browser functionality instead of manually testing all features. 

My first step was purchasing a domain from [Namecheap](https://www.namecheap.com/), which was a painless process. The next step was finding sufficient hosting; Github Pages wouldn't work here, as I needed something to host the back-end of the application. I settled on [DigitalOcean](https://www.digitalocean.com/), due to its fairly low costs and great tutorials. 

I set up a $5 Ubuntu droplet and SSH'd into it, installing the necessary technologies and then cloning my repo. Using NGINX and PM2, I established a 'watch' task to restart the app on any changes. After linking my domain name with the droplet, [Type Poetry](http://typepoetry.com/) was complete!