##UX layout considerations for BlackBerry 10 Applications.##
This repo contains sample source code with common layout patterns for HTML5 Applications targeting the BlackBerry 10 platform.
UI Layout patterns for HTML5 Applications targeting the BlackBerry 10 Platform
Full source code with comments is available at: https://github.com/anzorb/UI-Layouts

The purpose of this article is to outline the common layout models for HTML5 Applications targeting the BlackBerry 10 platform. Along with the article I’ve provided easy to follow source code that produces the following layout on the BlackBerry Z10 and Q10:

I.	Absolute/Fixed layout
The absolute/fixed layout allows developers to position elements anywhere on the screen using x and y coordinates. This is not uncommon in layouts today, and is quite easy to implement as long as the number of root elements (header,content,footer) remains low. The developer is required to provide exact dimensions and positions of all elements so they don’t overlap and appear to be ‘stacked’. As the number of root elements increases - for example we add a search bar to the root, it’s top value is positioned at (height of header) px, while content top is be positioned at (height of header + height of search bar) px, and so on. 

Source with comments here:  https://github.com/anzorb/UI-Layouts/tree/master/absolute

HTML:

<body>
    <div id=”#App”>
        <header>
             <section id=”search-bar”></section>
        </header>
        <content></content>
        <footer></footer>
   </div>
</body>

CSS:
/* set all sections to absolute */
body,html,.container{
	width:100%;
	height:100%;
	background: #181818;
	margin:0;
}

/* set all sections to absolute */
header,
.content,
footer{
	position: absolute;
}

/* when absolute, we need to explicitly specify the top and bottom coordinates to fill content in */
.content{
	top:240px;
	bottom:120px;
	width:100%;
	background:#181818;
	/* scrolling properties */
	overflow: scroll;
	-webkit-overflow-scrolling: touch;
}

/* common attributes, such as background color and width to stretch to cover the whole screen */
header,
footer{
	background:#bbbbbb;
	width:100%;
}

/* when using absolute, it makes more sense to place the search bar inside of header */
header {
	top:0;
	height: 220px; /* 100 (search bar) + 120 (self) */
}

/* positioned at the bottom of our container */
footer {
	height:120px;
	bottom:0;
}

Pros: 
Cross-platform (works across 100% of mobile and desktop browsers)
Cons: 
Developers are required to provide exact measurements, and number of root elements needs to remain low to avoid manual calculations.
II.	Stack/Flexible layout
Source: http://www.html5rocks.com/en/tutorials/flexbox/quick/
While targeting smartphone applications, developers often need to stack components to take up the available screen height. Header, content and footer sections appear under one another, with the content section flexible and scrollable if its contents overflow the available height. Other elements, such as the search bar can be inserted into the stack, pushing content down and decreasing the height of any flexible regions automatically.
With flex-box, creating a stack layout is easy as it relies on the browser engine to decide how to position and stretch elements to fill the screen. All elements remain relatively positioned which means we are no longer required to provide top or bottom values, and choose which elements have fixed widths and which are to remain flexible.
Please note: At the time of writing this article, the flex-box standard has split into ‘old’ flex-box and ‘new’ flex-box. The new flex-box standard support is nearly non-existent or broken in most browsers.  Although BlackBerry 10 supports both standards - I chose to focus on the old flex-box in this article for wider browser support. The concept remains the same – flex-box allows stack layouts for web applications.
Source with comments here:  https://github.com/anzorb/UI-Layouts/tree/master/box

HTML
<body>
    <div id=”#App”>
        <header> </header>
        <section id=”search-bar”></section>
        <content></content>
        <footer></footer>
   </div>
</body>
CSS

/* set your container's display to -webkit-box */
.container{
	display: -webkit-box;
	-webkit-box-orient: vertical;
	-webkit-box-align: stretch;
}

/* setting contents flexibility to 1, ensures that it stretches to fill available space */
.content{
	-webkit-box-flex: 1;
	width:100%;
	background:#181818;
	overflow: scroll;
	-webkit-overflow-scrolling: touch;
}

/* fixed heights for header and footer */
header,
footer{
	background:#bbbbbb;
	height:120px;
	width:100%;
}
/* OR flexible heights thanks to flex-box
header.flexible{
	height: auto;
}

The -webkit-box-flex: 1; property is the ratio of how much available space is allocated to the element relative to other elements. So if we wanted to have two content sections on top of one another, taking up the same height while remaining flexible and adapting to available screen space, we can add the same property (webkit-box-flex: 1) to our second content section. Since the ratio is 1:1, both will take up the same room, while stretching as available screen estate increases.

The Stack/Flexible layout also simplifies use cases where headers/footers and other elements need to remain flexible. For example in an application that displays news articles the header displays news articles’ titles and may take up anywhere from a single line to 3 lines vertically. Ideally, especially when using gradient we want the header to stretch as to not truncate the text:
Developers can also leverage min-height and max-height to tell the renderer how flexible the header will be. Since header is a child of container, and container is flexible, no other properties are required.
/* this allows header to grow up to 170px, which allows for the use case discussed above */
header{
	max-height:170px;
	line-height:45px;
}

Pros: 
Truly flexible, no exact dimensions are required.
Offers a ‘stack’ layout model to HTML5 developers, not uncommon among Cascades developers.

Cons: 
Confusion over new and old flex-box.
Browser support (not supported in IE and Opera).
*Although a library exists that ports functionality to IE 6-9 and Opera 10 https://github.com/doctyper/flexie

Detecting and adapting to Landscape orientation

Source code here: https://github.com/anzorb/UI-Layouts/blob/master/common/media-queries.css
The BlackBerry Z10 screen is 720px wide, which means that we have 720px of screen height to work with when the device is in landscape. It is often good practice to resize certain elements such as the header and/or footer to allow for more content. The following example demonstrates how to use CSS Media Queries to increase the amount of content visible when in landscape:

Here’s the CSS that makes this possible.

@media screen and (orientation:landscape){
     header
     ,footer{
	height:90px;
     }
}

The Landscape orientation media query is triggered when viewport height is less than viewport width. This means that if the device is in portrait, and we pull up the keyboard, landscape orientation will also get triggered, resulting in the rules above applied even though the device is in portrait. This is because the virtual keyboard causes a decrease in the screen height to a point where screen is a few pixels shorter than it is wide. This may be an issue if for example the footer contains wider buttons in landscape. The user will see the buttons get wider as the virtual keyboard pops-up – not the best User Experience.

One way to tackle this is by adding a width property:

/* detect landscape orientation and device width of 1280px */
@media all and (orientation:landscape) and (width:1280px){
     header
     ,footer{
	height:90px;
     }
}

This restricts our rule to only be in effect when the screen width (or innerWidth) is 1280, in other words, only if device is really in landscape.
A number of core BlackBerry 10 Applications allow the virtual keyboard to overlay the action bar. This allows for greater screen estate, especially if the application relies on messaging a lot (think BBM for example). 
A good practice is to have a minimum height where all elements are displayed, and if height is smaller, begin to hide the elements to allow for more content.
The virtual keyboard is 413px high in portrait and 274px high in landscape.
Remaining space when the keyboard is up:
1280 – 413 = 867px (portrait 1280x720 or 1280x768)
768 – 274 = 494px (landscape 1280x768)
720 – 274 = 446px (landscape 1280x720)
This means we can target 450px as the minimum in landscape and 870px in portrait.
Using CSS Media Queries, let’s hide the header and footer if height is below our set targets.
/* hide header and footer when available height is shorter than target */
@media 
all and (max-height:870px) and (max-width: 768px), /* portrait 1280x720 and 1280x768 */
all and (max-height:450px) and (width: 1280px) /* landscape */
{
    header,footer{
		display:none;
	}
}























CSS Media queries also allow targetting specific devices, by available height and width. The following detects a screen size of 720x720, which will shorten the header and footer heights on a Q10.
/* detect BlackBerry Q10 and make header and footer shorter for more screen estate  */
@media all and (width:720px) and (height: 720px)
{
    header,
    footer{
            height: 90px;
    }     
}

The BlackBerry Z10 offers 768px when in landscape, while the Q10 offers 720px, and it is often easy to target both with one rule, as to provide more content in both cases, without having to maintain two separate rules.
Here’s how the above rule can be combined with one mentioned before:
/* detect landscape orientation and Q10 device */
@media 
all and (orientation:landscape) and (width:1280px),
all and (width:720px) and (height: 720px)
{
     header
     ,footer{
	height:90px;
     }
}

Please note that these CSS queries are designed for the flexible layout model only .There would have to be more rules to make it work with the fixed model, as we are required to provide exact positions changes along with size changes when orientation changes - another pro for the flexible box model.
In conclusion, it’s worth noting that both code quality and performance improve when leaving the layout tasks to the layout engine (CSS), rather than detecting orientation/screen size using JavaScript and applying classes/style. The stack model has been used by Native developers for quite some time and is finally making a debut to HTML5 and provides an intuitive way to create layouts that adapt across devices with different form factors.
Be sure to check out the full source code here (https://github.com/anzorb/UI-Layouts) and save it for when you are working on the layout of your next BlackBerry 10 application.
Sources

QUICK HITS WITH THE FLEXIBLE BOX MODEL by Paul Irish
http://www.html5rocks.com/en/tutorials/flexbox/quick/
Media Queries for Standard Devices
http://css-tricks.com/snippets/css/media-queries-for-standard-devices/


