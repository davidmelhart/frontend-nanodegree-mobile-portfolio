##Udacity - Front End Nanodegree - Website Optimization

##Index.html

Initally the DOM Content Loading time is around 60-70msec and it takes an addional 60-70msec to the onload event to fire.
This means that the page initial loading time is most likely above 1sec.

Initial Path: 2 render blocking CSS and 2 parser bloking JS (inline and external analytics scripts).
Number of critcal resources: 5.
Number of minimum critical path length (roundtrips): 2.
Total critical KB: 24,5 KB 

After the browser sends its request and gets its response, it starts parse the HTML.
Requests are sent out for all the stylesheets and scripts.
	First possible optimisation to look at the parsing and renderblocking scripts and stylesheets.
	
	Webfonts are needed for the optimal rendering of the page and are part of the Critical Rendering Path.
		However without the webfonts, the page load time drops drastically as the DCL drops between 30-50msec and the final onload is between 50-70msec (all-in-all)
		Additionally the total critical KB drops by 900B.
	Style.css is also needed for rendering the page and is part of the CRP.
	It seems that print.css should not be blocking as it is usally not used and not part of the CRP.

	Analytics.js is most certainly should not be part of the the CRP and should run asynchronously.
	Prefmatters.js is monitoring the CRP and already asynchronous with the critical path.

After the DOM is completed CSSOM should be built, then all the JS should run and finally layout and render should be carried out before painting.
	It seems to be an issue with the images (pizza.jpg in particular) as it takes an awful lot of time for the browser to gather the data about the images.
		Images should be optimised for rendering.

DOM Content Loading time after optimisation: around 15msec, onload event fires around 28msec.
New Critical Rendering Path:
Number of Resources: 4 (The HTML, an inline JS of analytics and an inline CSS styling for the above the fold content and the webfont's CSS).
Number of minimum CP length: 2.
Total critical KB: 3,5 KB

In addition to the sources that were touched upon in the course:
http://stackoverflow.com/questions/19374843/css-delivery-optimization-how-to-defer-css-loading
-----------------------------------------------------------------------------------------------------------------

##Pizza.html

Initially the time to Generate is around 35msec.
Initially the avarage time to Generate 10 frames is around 50msec.

In the Timeline we can see a JS script firing on load and also on scroll event causing Forced Synchronous Layout error.
	It seems that the function that causing the problem is "updatePositions" starting in line 501 and the "document.addEventListener" in line 527 that calls updatePositions.
		In updatePositions, it seems that scrollTop and left could trigger the FSL error.
		Heavy calcuations are not necessary to be made on every turn of the loop. Therefore, body.scrollTop calculation should be moved outside of the loop contained in updatePositions. This seemingly solves the problem of Forced Synchronous Layout when scrolling.
		On loading the page however, document.addEventListener still triggers a FSL error by calling updatePositions.
The generator for moving pizzas in the background generates an insane amout of pizzas at once.
	The amount of pizzas can be reduced to 21 without any visible change.
It also seems that the moving pizzas in the background are not on separate composite layers, thus forcing the whole page to be repainted in each frame.

The initial time to resize pizzas is around 175msec.

"determineDx" function in line 425 utilizing .offsetWhidth, triggering a Forced Synxhronous Layout error.
	Using methods pulled from the course we can simplify the code by large cutting out all the hard calculations.

The new time to Generate is around 20msec.
The new time to Generate 10 frames is around 0.3msec.
The new time to resize pizzas is around 0.5msec.

In addition to the sources that were touched upon in the course:
https://developer.chrome.com/devtools/docs/demos/too-much-layout
http://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/




----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Website Performance Optimization portfolio project

Your challenge, if you wish to accept it (and we sure hope you will), is to optimize this online portfolio for speed! In particular, optimize the critical rendering path and make this page render as quickly as possible by applying the techniques you've picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).

To get started, check out the repository, inspect the code,

### Getting started

####Part 1: Optimize PageSpeed Insights score for index.html

Some useful tips to help you get started:

1. Check out the repository
1. To inspect the site on your phone, you can run a local server

  ```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080
  ```

1. Open a browser and visit localhost:8080
1. Download and install [ngrok](https://ngrok.com/) to make your local server accessible remotely.

  ``` bash
  $> cd /path/to/your-project-folder
  $> ngrok 8080
  ```

1. Copy the public URL ngrok gives you and try running it through PageSpeed Insights! Optional: [More on integrating ngrok, Grunt and PageSpeed.](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

Profile, optimize, measure... and then lather, rinse, and repeat. Good luck!

####Part 2: Optimize Frames per Second in pizza.html

To optimize views/pizza.html, you will need to modify views/js/main.js until your frames per second rate is 60 fps or higher. You will find instructive comments in main.js. 

You might find the FPS Counter/HUD Display useful in Chrome developer tools described here: [Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).

### Optimization Tips and Tricks
* [Optimizing Performance](https://developers.google.com/web/fundamentals/performance/ "web performance")
* [Analyzing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp.html "analyzing crp")
* [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/optimizing-critical-rendering-path.html "optimize the crp!")
* [Avoiding Rendering Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css.html "render blocking css")
* [Optimizing JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript.html "javascript")
* [Measuring with Navigation Timing](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/measure-crp.html "nav timing api"). We didn't cover the Navigation Timing API in the first two lessons but it's an incredibly useful tool for automated page profiling. I highly recommend reading.
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/eliminate-downloads.html">The fewer the downloads, the better</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer.html">Reduce the size of text</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization.html">Optimize images</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching.html">HTTP caching</a>

### Customization with Bootstrap
The portfolio was built on Twitter's <a href="http://getbootstrap.com/">Bootstrap</a> framework. All custom styles are in `dist/css/portfolio.css` in the portfolio repo.

* <a href="http://getbootstrap.com/css/">Bootstrap's CSS Classes</a>
* <a href="http://getbootstrap.com/components/">Bootstrap's Components</a>

### Sample Portfolios

Feeling uninspired by the portfolio? Here's a list of cool portfolios I found after a few minutes of Googling.

* <a href="http://www.reddit.com/r/webdev/comments/280qkr/would_anybody_like_to_post_their_portfolio_site/">A great discussion about portfolios on reddit</a>
* <a href="http://ianlunn.co.uk/">http://ianlunn.co.uk/</a>
* <a href="http://www.adhamdannaway.com/portfolio">http://www.adhamdannaway.com/portfolio</a>
* <a href="http://www.timboelaars.nl/">http://www.timboelaars.nl/</a>
* <a href="http://futoryan.prosite.com/">http://futoryan.prosite.com/</a>
* <a href="http://playonpixels.prosite.com/21591/projects">http://playonpixels.prosite.com/21591/projects</a>
* <a href="http://colintrenter.prosite.com/">http://colintrenter.prosite.com/</a>
* <a href="http://calebmorris.prosite.com/">http://calebmorris.prosite.com/</a>
* <a href="http://www.cullywright.com/">http://www.cullywright.com/</a>
* <a href="http://yourjustlucky.com/">http://yourjustlucky.com/</a>
* <a href="http://nicoledominguez.com/portfolio/">http://nicoledominguez.com/portfolio/</a>
* <a href="http://www.roxannecook.com/">http://www.roxannecook.com/</a>
* <a href="http://www.84colors.com/portfolio.html">http://www.84colors.com/portfolio.html</a>
