---
title: Paragliding Tasks Checker
date: 2021-09-20
type: post
categories:
  - Software
  - React
url: "/posts/paratasks"
image: /posts/paratasks/demo.gif
---

It's so easy to build react apps these days.  Over two nights, I knocked up a quick website to be able to upload igc flight files, and an xctask files to locally compute if you managed to hit the targets.

It's something to play with more later, but I'm hoping to start putting these widgets into my logbook app to be able to log flights against tasks when they're done.

App:  https://scottyob.github.io/paragliding-task-checker/

Source code:  https://github.com/scottyob/paragliding-task-checker

I followed [this guide](https://dev.to/yuribenjamin/how-to-deploy-react-app-in-github-pages-2a1f) to host on github pages.  It basically entailed:
1.  Adding **homepage** in **package.json**
2.  Adding in predeploy and deploy scripts **package.json**
    ```
    "scripts": {
    //...
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
    }
    ```
3. running ```yarn run deploy``` to deploy it.

![demo video](/posts/paratasks/demo.gif)