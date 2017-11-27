## Progressive Web App + Firebase Function + Chrome Headless on App Engine = Happy linkbots

You want the Firebase hosting speed for your PWA, but you really need some way to server render that sweet JavaScript so linkbots stop making your page look horrible on Facetweetlinkedspider card timeline gizmo. Let's piece together a solution.

### But how?

This consists of a few pieces to provide our example:

1. Polymer Starter Kit with PRPL that changes page metadata with JavaScript. Could be any PWA, but this is what I've used for the sake of speed of this example.
2. A Firebase function that checks UserAgent against a known list of linkbots that might render your content without JavaScript.
3. [Rendertron](https://github.com/GoogleChrome/rendertron), a dockerized, headless Chrome rendering solution designed to render & serialise web pages on the fly, acts are our go between.

### Setup

1. Deploy and use Chrome teams' [Rendertron](https://github.com/GoogleChrome/rendertron), a dockerized, headless Chrome rendering solution designed to render & serialise web pages on the fly.
2. Create a project on Firebase.
3. Clone this repo.
4. Run `bower install` in the project directory
5. Run `npm install` in the functions directory.
6. Have that URL handy from step 1? Great! Set the firebase config variable `firebase functions:config:set rendertron.server="https://YOUR_PROJECT_HERE.appspot.com"`
7. Cool. Now tell firebase where this domain is: `firebase functions:config:set site.domain="YOUR_SUB_DOMAIN_HERE.firebaseapp.com"`
8. Build the sample with the polymer-cli: `polymer build`
8. Deploy with `firebase deploy`. Have a victory dance.

### What do I do now?

You could give one of the views a spin in say the Twitter [Card Validator](https://cards-dev.twitter.com/validator). In the screenshot below, you can see the expected result: Firebase function kicked, the bot was found, it was sent to our render server, and the the DOM returned with proper JavaScript switched metadata, all of which Twitter reads correctly.

![image](https://user-images.githubusercontent.com/643503/27811117-42006570-6017-11e7-8580-27ab3bbc41d2.png)

### Gotchas

1. The PWA shell is dupped as `pwashell.js`. I suspect there is a way around this, I just haven't read the docs fully enough.
2. The `index.html` page (your PWA shell), basically will always fire as a direct match because of "Hosting Priorities" (see https://firebase.google.com/docs/hosting/url-redirects-rewrites). Your `index.html` should not expect to have JavaScript written metadata.
3. You'll need a paid plan for Firebase (Flame or Blaze), otherwise the external network request to App Engine (or where ever you deployed the docker container) will fail.
3. Probably other things that I'm missing the day before a holiday.

### Additional Notes / Questions
1. Initially, this was centered around Sam's initial concept [samuelli/bot-render](https://github.com/samuelli/bot-render), which is what became [Rendertron](https://github.com/GoogleChrome/rendertron).
2. _"Justin, do I have to run rendertron or can I just do this myself?"_ - You can run chrome headless on your desktop, you can use my [chrome-headless docker image](https://hub.docker.com/r/justinribeiro/chrome-headless/) and talk to it via [puppeteer](https://github.com/GoogleChrome/puppeteer) (which utilizes [Chrome DevTools Protocal](https://chromedevtools.github.io/devtools-protocol/)).
