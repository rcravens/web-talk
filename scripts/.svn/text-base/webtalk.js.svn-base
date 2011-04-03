var numDelayedElements = 20;
var numClickedElements = 20;

var slides = [];
var slideIndex = 0;
var newHash = "";

$(document).ready(function(){
	/* Set up the 'hash change' event handler */
	$(window).bind('hashchange', function(){
		newHash = window.location.hash.substring(1);
		if(newHash){
			loadSlide(newHash);
		}
	});
	
	/* Read the slideshow JSON data */
	$.getJSON("slideshow.js", function(data){
		slides = data;
		var hash = window.location.hash.substring(1);
		if(hash){
			for(var i=0;i<slides.length;i++){
				if(slides[i]==hash){
					// reload a slide
					slideIndex = i;
					loadSlide(hash);
				}
			}
		} else {
			// load the first slide
			loadSlide(slides[0]);
		}
	});
	
	/* Hook up the next and previous buttons */
	$("#nextSlide").click(function(){
		nextSlide();
		return false;
	});
	$("#prevSlide").click(function(){
		prevSlide();
		return false;
	});

	/* Hook up document level controls */
	$(document).click(function(){nextElement();});
	$(document).keyup(function(event){
		if(event.keyCode==38 || event.keyCode==37){
			// back one slide
			prevSlide();
		}
		else if(event.keyCode==39 || event.keyCode==40){
			// forward one slide
			nextSlide();
		}
		else if(event.keyCode==16){
			// disaable the highlighter
			isHighlightEnabled = false;
		}
		else if(event.keyCode==27){
			// hide the highlighter divs
			$(".highlight-mask").hide();
			$("#dragmask").hide();
		}
	});
	
	/* Highlighter functions */
	$(document).keydown(function(event){
		if(event.keyCode==16){
			// enable the highlighter
			isHighlightEnabled = true;
		}
	});
	var startX, startY;
	var isHighlightEnabled = false;
	var isDragging = false;
	$(document).mousedown(function(event){
		if(isHighlightEnabled){
			startX = event.pageX;
			startY = event.pageY;
			isDragging = true;
			$("#dragmask").css(
				{
					'left'	:	startX,
					'top'	:	startY,
					'width'	:	0,
					'height':	0
				}

			).show();
			// prevent default behavior of text selection
			return false;
		}
	});
	$(document).mouseup(function(event){
		if(isDragging){
			isDragging = false;
			$("#dragmask").hide();
			var screenWidth = $(document).width();
			var screenHeight = $(document).height();
			var topOfHighlight, bottomOfHighlight;
			if(event.pageY>startY){
				topOfHighlight = startY;
				bottomOfHighlight = event.pageY;
			}
			else{
				topOfHighlight = event.pageY;
				bottomOfHighlight = startY;
			}
			var leftOfHighlight, rightOfHighlight;
			if(event.pageX>startX){
				leftOfHighlight = startX;
				rightOfHighlight = event.pageX;
			} else {
				leftOfHighlight = event.pageX;
				rightOfHighlight = startX;
			}
			//position the top mask
			$("#topmask").css(
				{
					'left'	:	0,
					'width'	:	screenWidth,
					'top'	:	0,
					'height':	topOfHighlight
				}
			).show();
			// position the bottom mask
			$("#bottommask").css(
				{
					'left'	:	0,
					'width'	:	screenWidth,
					'top'	:	bottomOfHighlight,
					'height':	screenHeight - bottomOfHighlight
				}
			).show();
			// position the left mask
			$("#leftmask").css(
				{
					'left'	:	0,
					'width'	:	leftOfHighlight,
					'top'	:	topOfHighlight,
					'height':	bottomOfHighlight - topOfHighlight
				}
			).show();
			// position the right mask
			$("#rightmask").css(
				{
					'left'	:	rightOfHighlight,
					'width'	:	screenWidth - rightOfHighlight,
					'top'	:	topOfHighlight,
					'height':	bottomOfHighlight - topOfHighlight
				}
			).show();
		}
	});
	$(document).mousemove(function(event){
		if(isDragging){
			var left, top, width, height;
			if(event.pageX>startX){
				left = startX;
				width = event.pageX - startX;
			}
			else {
				left = event.pageX;
				width = startX - event.pageX;
			}
			if(event.pageY>startY){
				top = startY;
				height = event.pageY - startY;
			}
			else {
				top = event.pageY;
				height = startY - event.pageY;
			}
			$("#dragmask").css(
				{
					'left'	:	left,
					'top'	:	top,
					'width'	:	width,
					'height':	height
				}
			);
		}
	});
	
	/* Hook up the fancy buttons */
	$(".button").live("mouseenter", function(){
			$(this).stop().animate( {backgroundColor: "#acea11"}, 300 );
		}).live("mouseleave", function(){
			$(this).stop().animate( {backgroundColor: "#89ba38"}, 100 );
		});
	
	/* Hook up note overlays to fade on hovers and highlight on clicks*/
	$(".overlay").live("mouseenter", function(){
			$(this).fadeTo('fast', 0.1);;
		}).live("mouseleave", function(){
			$(this).fadeTo('fast', 1.0);
		});
	$(".note").live("click", function(){
		var bimage = $(this).css('background-image');
		var blackImg = "transBlack75.png";
		var redImg = "transRed75.png";
		var isBlack = bimage.search(blackImg);
		if(isBlack!=-1){
			bimage = bimage.replace(blackImg, redImg);
		} else {
			bimage = bimage.replace(redImg, blackImg);
		}
		$(this).css('background-image', bimage);
	});
		
	/* Create the timers for delayed elements */
	for(var i=1;i<numDelayedElements;i++){
		var cssName = ".delay" + i;
		$(cssName).hide(0, function(){
			var elem = $(this);
			var timeout = i*1000;			
			setTimeout(function(){elem.show('1000');}, timeout);
		});
	}

})

/* Navigation functions */
function nextSlide(){
	if(slideIndex<slides.length-1){
		slideIndex++;
		window.location.hash = slides[slideIndex];
	}
};
function prevSlide(){
	if(slideIndex>0){
		slideIndex--;
		window.location.hash = slides[slideIndex];
	}
};
function loadSlide(slide){
	$.get(slide, function(data){
		setTitleAndSubtitle(data);
		$("#content").fadeOut('200', function(){
			$("#content").html(data).fadeIn('200');
			
			// Hide the clickables
			hideClickables();
			
			// Create time delayed
			createTimeDelayed();
			
			// Create any slideshows
			$(".slideshow").each(function(slideIndex){
				createSlideShow($(this));
			});
			
			// Syntax highlight code
			SyntaxHighlighter.highlight();
		});
	});
}

/*
	Copyright (c) 2010 Bob Cravens, http://bobcravens.com/ 
*/

function setTitleAndSubtitle(data){
	var xmlDoc;
	try //Internet Explorer
	{
		xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
		xmlDoc.async="false";
		xmlDoc.loadXML(data);
	}
	catch(e)
	{
		try // Firefox, Mozilla, Opera, etc.
		{
			parser=new DOMParser();
			xmlDoc=parser.parseFromString(data,"text/xml");
		}
		catch(e)
		{
			alert(e.message);
			return;
		}
	}
	var body = xmlDoc.getElementsByTagName("body");
	var title = "";
	var subtitle = "";
	if(body[0]){
		if(body[0].attributes["title"]){
			title = body[0].attributes["title"].value;
		}
		if(body[0].attributes["subtitle"]){
			subtitle = body[0].attributes["subtitle"].value;
		}
	}
	$("#webtalk-title").text(title);
	$("#webtalk-subtitle").text(subtitle);
}

/* Clickable elements */
var elementNumber = 0;
function hideClickables(){
	elementNumber = 0;
	for(var i=0;i<numClickedElements;i++){
		var cssName = ".click" + i;
		$(cssName).fadeTo(0, 0.1);
	}
}
function nextElement(){
	var prevElement = elementNumber - 1;
	if(prevElement >= 0){
		$(".click" + prevElement).removeClass('hilite-red');
	}
	var cssName = ".click" + elementNumber;
	$(cssName).addClass('hilite-red').fadeTo('300', 1.0)
	elementNumber++;
}

/* Time delayed elements */	
function createTimeDelayed(){
	for(var i=1;i<numDelayedElements;i++){
		var cssName = ".delay" + i;
		$(cssName).hide(0, function(){
			var elem = $(this);
			var timeout = i*1000;			
			setTimeout(function(){elem.show('1000');}, timeout);
		});
	}
}

/* Slide show functions */
function showSlideShowImage(slideIndex, imageIndex){
	var widget = $(".slideshow-widget", main);
	var items = $("li", widget);
	var prevSelectedIndex = -1;
	items.each(function(index){
		if($(this).hasClass('selected')){
			prevSelectedIndex = index;
		}
	})		
	items.removeClass('selected');
	var liToSelect = $(items[imageIndex]);
	liToSelect.addClass('selected');
	var title = $("a", liToSelect).attr('title');
	$("#slideshow-title").text(title);
	
	var main = $(".slideshow")[slideIndex];
	var images = $("img", main);
	var imageToFadeOut = $(images[prevSelectedIndex]);
	var imageToFadeIn = $(images[imageIndex]);
	imageToFadeOut.fadeOut('100', function(){
		imageToFadeIn.fadeIn('100');
	});
}
function createSlideShow(elem)
{
	var main = $(elem);
	// Create the widget
	var widget = $("<span class='slideshow-widget'></span>");
	var images = $("img", main);
	var list = $("<ul></ul>");
	images.each(function(index){
		var image = $(this);
		var imageAlt = image.attr('alt');
		var elemText = index + 1;
		var elem = $("<li></li");
		if(index!=0){
			image.css({'display':'none'});
		}
		var link = $("<a href='#' title='" + imageAlt + "'>" + elemText + "</a>");
		link.click(function(){ showSlideShowImage(slideIndex, index); });
		elem.append(link);
		list.append(elem);
	});
	var widgetOffset = 80;
	widget.append(list);
	var title = $("<span id='slideshow-title' class='clear'>title</span>");
	widget.append(title);
	main.prepend(widget);
	main.css({ 'position':'relative' });
	showSlideShowImage(slideIndex, 0);	
}