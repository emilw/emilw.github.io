---
author: lime.postback.se
comments: true
date: 2014-08-04 15:42:48+00:00
layout: post
slug: buildprocess-with-grunt-js
title: Buildprocess with Grunt.js
wordpress_id: 168
categories:
- Automation
- Javascript
---

When i started my personal game project, i only used xcode to build the output libraries. After a while there was a lot of manual work related to scaling images, running UnitTests etc. so i started adding bash scripts that did this. The problem started to arise quite soon when it got hard to maintain. At the moment i read a blog post about Grunt.js that run on node.js. Due to that node.js can run on all platform, using Grunt.js would allow me to run the platform independent parts, e.g for C++, web or image management on all platforms. This seemed nice to me so i tried it.

**Basics:**
Grunt.js uses a folder where it expects two files to exist, these are:
- Gruntfile.js
- package.json

Gruntfile.js is the task(s) configuration and package.json holds what packages from node that are active for the project/folder.

**Installing grunt on the system:**

//Install grunt, the grunt command can be executed everywhere after this step
```
npm install -g grunt-cli
```

    Setup for a project/folder:

    //Create the package.json(The file can be created manually if there is a need to it)

    npm init

    //Init the grunt libs for the project(--save-dev adds the package dependencies to the package.json file)

    npm install grunt --save-dev

    //Download and install example task, file resize task
    npm install grunt-multiresize --save-dev


**package.json after the steps above was done:**

```javascript
{
  "name": "GruntAutomationTest",
  "version": "0.0.0",
  "description": "",
  "main": "Gruntfile.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "grunt": "^0.4.5",
    "grunt-multiresize": "^0.3.1"
  }
}
```

**Configuring a task(Gruntfile.js):**
`
module.exports = function(grunt) {
grunt.initConfig({
pkg: grunt.file.readJSON('package.json'),
multiresize: {
image1: {
src: '/Users/Emil/Repos/Bybbarna/BybbarnaHunt/BybbarnaHunt/TextureAtlases/Raw/sprites-ipad.atlas/Bybbe.png',
dest: ['Output/Bybbe.png', 'Output/Bybbe@2.png'],
destSizes: ['100x100', '200x200']
},
},
});`

// Load the plugin that provides the "image resize"
grunt.loadNpmTasks('grunt-multiresize');

// Default task(s).
grunt.registerTask('default', ['multiresize']);

};

**To run it:**
Navigate to the root folder of the project from the command line
`
//Runs the default task, that was defined above e.g. 'multiresize'
>grunt
`



**Final words:**

There is some competition between Grunt.js and Gulp.js. The big difference is that Grunt.js is more focused on configuration style and Gulp.js is more coding style when setting up the tasks. Read more here:

http://www.johnpapa.net/gulp-and-grunt-at-anglebrackets/

Good resources:
- http://gruntjs.com/plugins

- http://gruntjs.com
