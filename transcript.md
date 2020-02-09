Hey everyone!
| So Web Performane Hacks!
| Reading the title it may seem like I'm going to talk about how to improve website's performance
| Well.. I'm going to disappoint you all, The talk is more about how to fake a fast website
| So I'll tell you some hacks which will make your website appear as if it is loading fast but actually it won't really load fast. Infact we will delay things
| So let's see...

|| and Yes! My name is Saurabh
| I'm a final year engineering student and I like open source
| There are some NPM packages and VSCode Extensions on my GitHub, you can check them out

|| SO! Are you all ready? I'm very excited since this is my first talk!

|| Let's start with hack number 1
| This is the easiest one to implement and I can guarantee you a 100 lighthouse score!.
| And... Here we go with hack number 1... 

|| $wait10s
| So if your boss asks you to get performance up by tomorrow morning... you know what to do!
| Don't take my name
| So throughout the talk we'll remember two points..
| to not compromise with user interface and to not compromise with the content
| so we will not do this

|| Now let's actually start with the talk
| We'll go step by step, first let's see how web works

|| I type a URL in my browser, hit enter, and a request is sent to the server
| server responds with index.html
| but... when we visit a website, we don't see html code, we see web page with colors and backgrounds and animations and all the pretty pretty stuff
| so there's a process that turns html to the webpage
| and yeah... obviously... we all know what that process is

|| ITS MAGIC!
| something happens and we get the interface
| well... since I am giving this talk, I had to do some research on that point
| oh by the way when I say research, I mean stackoverflow
| so it appears that the spell is called...

|| Parsing and Rendering! $wait2s

|| Let's see how HTML is parsed and rendered on screen

|| So initially you have HTML text
| For parser, its just a random sequence of characters
| So the HTML text goes through a process called tokenization
| Tokenization is common in almost every programming language
| Tokenization will split the characters and will make tokens
| These tokens tell, when a tag starts and when it ends
| Now, if I know when my HTML starts and after that if I know my head is starting, I can create a tree right?
| so the HTML would be on top and then head as its child
| so we first get something called a parse tree and then eventually get a DOM tree
| DOM tree is what we access when we use selector commands in JavaScript
| CSS also goes through a very similar process where we have CSS text and eventually tokeninzation of CSS happens
| and CSS forms something called C S S O M, which is CSS Object Model $wait2s

|| This is what a CSSOM tree looks like, $wait5s
| so we have body and it's styles, then a div and its styles

|| $wait5s
| Now we got CSSOM Tree and DOM Tree, These trees are merged to form render tree
| Render tree has all the information about the styles and dom
| After render tree, layout happens, so it takes information about positions from render tree and lays out the elements
| so if I have two divs with position relative, it will calculate and place the second div in its correct position
| then a paint happens where the elements get colors, background-colors and shadows.

|| This is an important point $wait5s
| so parsing halts when it comes across link, style and script tags.
| Which means... if I have link rel=stylesheet in head, the parser is going to stop there, fetch the css, wait for response and then continue.
| Same if JavaScript, it will fetch the javascript, execute it and then it will continue.

|| Now, from the point a user hits enter on typing URL, to the formation of render tree and then layout happening and then paint and eventually getting first paint
| This is what our users see
| a blank white page
| White pages are a bad user experience
| they don't tell anything
| is my website loading? did it fail? my laptop crashed? are we in galactic simulation which just stopped working? nobody knows...
| So... we have to do something to get the first paint as quick as possible
| Also, anything is better than white page.. blue screen, pink screen, anything!

|| So we have three challenges to solve,
| Let's see how we can defer CSS first

|| We'll do something called a 'Quick First Paint' hack
| by the way, I named them so there isn't anything of this name

|| Let's do some live coding!

|| So I have a basic HTML Page, I've commented out CSS so its just HTML right now
| Chrome has a performance tab in the devtools so let's run a performance profiler

|| Now this is what we get $wait5s
| on Slow 3G we're getting the first paint on 2seconds
| Also, in performance tab, the purple bar is the layout bar and green bar is of paint, which we saw in the previous slides.
| But, nobody writes websites without CSS
| So lets uncomment CSS file and see the performance again

|| So I've uncommented CSS file in the code. $wait2s

|| And here's the output
| Now, the problem is, for 4 point 1 seconds, user is seeing white screen.

|| and these are the performance results... $wait5s

|| Now the problem is renderer waits for the CSS to load

|| How about rendering the HTML content first and painting on screen and then applying CSS?
| This will let us have a content on screen as soon as possible, and when the CSS loads, we can just apply that CSS.

|| So we use rel=preload property $wait10s
| with rel=preload we have to mention what type of file it is, so we put as=style
| If it is javascript, you can put as="script", there are other values like worker, fetch, font.
| Also, rel=preload will only load the CSS file and will not apply that CSS, so we put an onload handler where we turn rel to stylesheet.
| so the CSS will be applied when it loads, and will not block the HTML parser. $wait5s

|| This is what the output will look like to the user.
| Now we get html content in 2 seconds so the user does not have to wait for 4 seconds to see the website
| Well... this is a win... but we still have one problem
| At the start of the talk, I said we are not going to compromise with the UI
| having a flash of CSS less HTML content seems like a unfair experience for the user

|| Just showing HTML to the user is a bad user experience as well

|| So to solve this, we can do something called Inlining Critical CSS

|| Now, Initially we only want styles header to load so I'll cut paste the styles of header from main.css to the index.html's style tag.  $wait5s

|| So now, we get styles of a header loaded straight from HTML and thus we don't wait for CSS to show initial styles
| Since the user is not going to scroll as soon as he get's on website, we get time to load other styles
| Styles for remaining page will be loaded from main.css file
| In output, now we get our website's header in first 2 seconds. 
| Yehhey! that's a win! you can celebrate here!
| So cool, we solved the CSS problem, now we have to fix JavaScript

|| As I said script, blocks the HTML parser
| but there are ways to avoid this parse blocking scripts
| We can use async and defer
| Now you can see slides, this is script loads and executes with different attributes $wait5s
| As you can see, the usual script tag pauses the HTML parsing, downloads the script, and executes the code
| This is the reason why we put script at the end of the body, so that we won't block the rendering process.
| async will download the script while parsing and will execute it whenever it can. So it will execute as soon as it is downloaded.
| defer on the other hand will download the script while HTML is parsing, just like async but it will wait for parsing to end to execute the script
| Writing script at the end of body, and writing it anywhere with defer tag almost has same effect
| the difference would be, scripts with defer will be executed earlier since they will be downloaded while HTML is parsing
| while the scripts without defer will start downloading after HTML is parsed completely.

|| Hushhh! that was a lot right?
| Whatever I talked till now, was very much with respect to the vanilla setup, Though there are ways to implement these things in frameworks as well
| In frameworks, most of the rendering depends on the JavaScript provided by the bundles
| If I have 100 routes on a website, the build of frameworks will have JavaScript required to render all 100 routes
| This means a huge JavaScript file right? and we have to fetch this in first load so that's even bad.
| To solve this we can use something called code-splitting.

|| So rather than having one giant bundle, we divide files as per routes. So when I'm on home page, I will only have to load the home-chunk.
| This helps to avoid loading unneccesary code of /contacts or /about route when I'm on a home page.
| But now the problem is, we lose smoothness of going from one route to the other since we have to download chunk before we show content on screen.
| We can solve this using predective prefetching
| I'm not sure about any company which uses it in production but what predictive prefetching tells you to do is,
| If I'm on a home page and click search bar, then we can load /search route chunk already 
| so that untill the user is done typing and hits enter, search chunk be already loaded.
| When I get on search page, we preload item page chunk, when on item page, preload checkout page chunk.
| So we predict which route the user may visit next, and we load the chunk already.
| DEV.to does something very cool, it's not exactly a code-splitting, but they load articles when user hover over them
| There's a timegap between you hovering over the article and you actually clicking the article, they use this timegap to fetch the content.

|| Awesome! now we're done with CSS and JavaScript, Finally let's talk about images!

|| We have a new native loading=lazy attribute in HTML now! 
| Rather than loading images in the initial load, loading=lazy will load images when the images become visible on viewport.
| This saves the extra bandwidth of the user
| Though the problem is, it has a really bad browser support since it is new.

|| Currently we have Chrome and Edge supporting the native loading attribute. $wait5s

|| There is a polyfill though, the polyfill is 3kb and it is worth using it if you have a image-heavy website. 
| by the way, the polyfill also makes it work on IE9 so it's kind of cool  

|| Next up we have Progressive JPEGs!

|| Default JPEGs load from top to bottom which gives a bad user experience $wait2s.
| Progressive JPEGs on the other hand start as blurred image and go on getting clearer.
| Twitter and Facebook currently use Progressive JPEGs in production for the images in the post.
| to get Progressive JPEG, if you're using photoshop, you can check the progressive checkbox while saving image.
| Otherwiser there are online JPEG to Progressive JPEG convertors which you can use.
| Progressive JPEGs have a disadvantage though, 
| Even though visually to the user they seem like they are loading fast, they actually longer than normal JPEGs
| In the worst case, they take 3 times longer to load than usual JPEGs

|| and finally we have CDNs, Content Delievery Networks
|| a lot of CDNs provide an API which lets you change the parameters of image from URL 
| so you can dynamically set the width and only ask for image of the size that you need.
| So if you have a header image which is needed to be 1000px wide in desktop, it is likely to take lesser width in mobiles.
| In this case, you can read the parent's clientWidth property and set that width in image URL to get image of exact size. $wait5s

|| and yes we're done! by the way I'm working on a cool project called visconf
| VisConf lets you create talks from transcript and slides and generates an animated version of talk.
| You can check it out on github. $wait5s

|| and that is it! Thank you! If you have any questions you can drop on my twitter.