Corgi
==

An idea for generating and tracking web site coverage

Problem Space
==

When developers and full-time testers write tests, we always confront the question, "How much have I really tested?" This deceptively simple question leads to a series of complex answers:

 * How much of the code has been exercised?
   * Low fidelity coverage
   * We have merely exercised the code without setting expectations for granular behavior.
 * How much of the code is forced to do its job based on the assertions of the tests?
   * High fidelity coverage
   * Not only do we exercise the code, we verify expected behavior
 * When we talk about "code coverage," what is code?
   * Server-side code in the primary language of the project is simple to target for coverage (C#, java, Ruby, etc.)
   * Is configuration code?
   * Is HTML code?

Within a web project, code can be tested using a variety of testing methodologies (full-integration testing hitting the entire application stack, functional testing, unit testing isolated classes) targeting a variety of application layers (HTML, services, isolated classes). All of these testing methods are useful to drive a software project toward functional stability and predictability.

Using these established testing methods, a variety of existing tools apply instrumentation to the server-side code to easily generate code coverage reports for that code. Tools like nCover, Emma, and SimplyCov make it easy to instrument the server-side procedural code then report low-fidelity coverage numbers based on any type of testing you wish to throw at the system.

Ideally, code coverage tools lead developers and testers to write tests for _critical_ parts of the system that are currently untested in order to reduce risk in that area of the system. Risk can be introduced by a variety of factors such as the absence of testing, frequent change, and vague requirements to name a few. Code coverage tools provide one vector for information when assessing risk in a software system.

Browser Code Coverage
==

When we move from the server and start talking about the code that actually hits the browser, coverage tooling becomes much more sparse. Browser code artifacts are made up primarily of HTML, CSS, and javascript.

Tools like [Blanket](http://blanketjs.org/) can help developers build the code coverage picture for javascript -- even in-browser javascript. Tools like [Dust-Me](https://addons.mozilla.org/en-US/firefox/addon/dust-me-selectors/) can help us locate dead CSS code. At a very coarse level, tools like [Sikuli](http://www.sikuli.org/) can help testers drive graphical user interfaces and make graphical assertions.

However, Google searches for [HTML code coverage](http://www.google.com/search?q=HTML+code+coverage), [web code coverage](http://www.google.com/search?q=web+code+coverage), [browser test coverage](http://www.google.com/search?q=browser+test+coverage) reveal that as of this writing, HTML code coverage tooling appears non-existent.


Web Testing
==
Before discussing web site coverage, it helps to have the context for web page testing established first.

HTML is a declarative language. Browsers always render all visible HTML elements. So, from a very simple level, anytime we load a page in a browser, we are covering all of the visible HTML elements. So, what is there to cover, really?

To understand coverage, we must first understand how we might test the full web site using the browser. We could load a single page in the browser, then exercise functionality exposed via that page, then verify the results of specific actions. Let's call this type of testing, "page testing." We could also start on the landing page of a web site, then exercise various actions (links, buttons, etc.) to navigate through a series of pages on the site. Let's call these tests, "browse tests."

Page Tests
--
Within the context of page tests, we are generally concerned with questions such as:
 * Is the page layout correct?
 * Do all expected elements render?
 * Is the javascript on the page working?
 * Can the server render the page under test given specific input parameters?
 * Are specific output values visible in specific elements?
 * Is the page styled properly?

Some of these questions are relatively easy to answer using browser automation tools and client-side javascript testing tools. We even have tools for generating code coverage for the javascript that hits the browser. Assertions about styling are much more difficult to verify. We could use screen grab tools to make assertions about the visualization, but this type of testing is typically brittle in the face of any change.

We also have tools like Dust-Me and Blanket for generating coverage reports for the CSS and javascript respectively. What about the HTML? As stated above, the HTML is always rendered in the browser. So, to some small degree, as long as the HTML is well formed enough that the browser can render it, we always cover the HTML. This coverage is equivalent to the low-fidelity coverage we get when we know we can execute a block of server-side code without an exception.

While we can execute page tests that request a single, specific page and verify that the page is rendered correctly, we can also test more complex navigation paths that involve multiple pages and multiple actions -- what we are calling, "browse tests."

Browse Tests
--
Browse tests are generally based on complex scenarios. Depending on the problem space of the web site, browse tests may be attempting to answer questions such as:

 * Can a user shop for button-down shirts in a specific size and color?
 * Do all the payment pages work well enough to accept a Visa credit card?
 * Can a user compose and send an email?

These tests generally hit multiple pages and rely on the navigations actions presented on each page to navigate to the next page in the scenario. They test whether or not the correct information and actions are presented to allow the user to complete a complex series of actions to reach a bigger goal.

What is Web Site Coverage?
==
What if we looked at web pages as a combination of information and actions? Information is largely presented via text and augmented with styling. Actions are presented via affordances on the web page -- buttons, links, and other controls. Given this view of a web page, what would we want to cover? Likely, we would want to make certain that tests perform assertions about a combination of both information and actions.

What if we could see a reported list of possible actions on a given page along with which actions were executed? This could inform our concept of coverage as it pertains to exposed page actions. Additionally, if we could build a list of information elements on the page, then we could track which elements we searched for on that page during the course of a test.

So then, web page coverage could be viewed as a combination of information and action coverage.

Possible Solutions
==
An effective web coverage tool would provide the following information and affordances:
 * An enumeration of actions on a page along with the amount of coverage those actions are given by executing a suite of tests.
 * An enumeration of information on a page along with the amount of coverage that information is given
 * The ability to ignore uncovered, low-risk elements
 * Consise reporting, possibly laid out in a hierarchy similar to the site under test
 * An unobtrusive method for instrumenting the code under test or otherwise gathering information
 
 
The current solution rattling around in my head involves building on top of the web driver (Selenium, Watir...) such that whenever a new page is loaded, we inventory the elements on the page, then track which of those elements are requested or exercised during testing. This likely involves putting a decorator around usage of the web driver as well as possibly hooking into page load events from the web driver. Querying all elements on the page presents an obvious performance concern. Given their full-stack nature, web tests are slow enough already. So, performance with and without the instrumentation should be monitored.
