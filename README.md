moveable-objects
================

var pageContainer, menuContainer, page0, diceCanvas, btnSpin, dice,  savedState ,tilesContainer ,correctdataval ,correctdataval1,correctdata1,correctdata2,Equaldata1,Equaldata2,check;
var numTiles = 10;
var isReset = false;
var isReset2 = false;
var isReset3 = false;
var pageCount =1;
var currentQues = 0;
var audinst = new Audio('audio/imth-1-11-moveable-objectsii-ICON-1.mp3');
var mytile = new Array("0","1","2","3","4","5","6","7","8","9");
var completedAns = [{"tens":"","ones":"","ans1":"","ans2":"","opacity":0.5,"submitImg":"submit.png","submitopacity":0.5},{"tens":"","ones":"","ans1":"","ans2":"","opacity":0.5,"submitImg":"submit.png","submitopacity":0.5},{"tens":"","ones":"","ans1":"","ans2":"","opacity":0.5,"submitImg":"submit.png","submitopacity":0.5},{"tens":"","ones":"","ans1":"","ans2":"","opacity":0.5,"submitopacity":0.5}];
var initialPos;
var isDrag = true;

var imgDetails = [{imgCount:37,imgSrc:"smily.png"},{imgCount:46,imgSrc:"star.png"},{imgCount:38,imgSrc:"ball.png"},{imgCount:42,imgSrc:"football.png"}];

$(document).ready(function(){
    document.onkeydown = function(){
      if(window.event && window.event.keyCode == 9) 
      { // Capture and remap TAB
      window.event.keyCode = 9;
      }
      if(window.event && window.event.keyCode == 9) 
        { 
	    return false; 
	}
}
	
        var table=["<div id=\"blocker\" onclick=\"instruct()\" ></div><div class=\"question\"><div style=\"position:absolute; left:2%; top:6%; opacity:0.6;cursor:pointer;\" ><div><img src=\"images/btInfoOn.png\" width=\"25px\" height=\"27px\" /></div></div><span style=\"text-align:left; font-family:myFontfamily; left:8.5%; font-size:30px;  line-height:1.4em; width:520px; position:absolute; top:50px;\">Show how many objects there are by filling in the place value table and the whole number box.<br/>Drag the correct number tiles into the tens place and the ones place. <br/>Tap the target button to check your work.<br/>Tap the arrows to move to another problem.</span><i style=\"float:right;cursor:pointer; opacity: 1;\" class=\"closeBtn icon icon-emove\" onclick=\"instruct()\" >&#x000D7;</i><div style=\"position:absolute;  left:45%; top:85%;\" onclick=\"AudioState()\"><img src=\"images/audioICON.png\" width=\"50px\" height=\"50px\" /></div></div>"];
        pageContainer = $("<div id=\"interactive-container\" />");
        menuContainer = $("<div id=\"page-container\" />");
	menuContainer1 = $("<div id=\"page-container1\" />");
        footerContainer = $("<div class=\"container\"><div class=\"contentFeedback\"><div class=\"pagebtnCon\"><div class=\"pagebtn activePageBtn\" style=\"left: 492px;\" id=\"page_1_icon\"></div><div class=\"pagebtn\" style=\"left: 512px;\" id=\"page_2_icon\"></div><div class=\"pagebtn\" style=\"left: 532px;\" id=\"page_3_icon\"></div><div class=\"pagebtn\" style=\"left: 552px;\" id=\"page_4_icon\"></div></div><div class=\"btReset\"><div class=\"icoReset\"></div></div><div class=\"btInfo\"><div id=\"icoinf\" onclick=\"openInst()\" class=\"icoInfo\"></div></div><div class=\"btFeedback\"><div class=\"icoFeed\"></div></div><div class=\"score\"><div id=\"contCorrect\">0 <img src=\"images/IconCorrect.png\"></div><div id=\"contIncorrect\">0 <img src=\"images/IconIncorrect.png\"></div></div></div></div></div>");
	page0 = $("<div id=\"page-0\" class=\"page\" />");
        Partimage = $("<div id=\"containerMain\"><div id=\"container0\"></div><div id=\"container1\"></div><div id=\"container2\"></div><div id=\"container3\"></div><div id=\"container4\"></div></div>");
	
	dropDiv = $('<div class="dropArea1Cont"><div class="dropArea1"><table><tr><td id="d1">Tens</td><td id="d2">Ones</td></tr><tr><td class="dropBox "><div class="dropPlace" id="dp1"></div></td><td class="dropBox"><div class="dropPlace" id="dp2"></div></td></tr></table></div></div><div class="dropArea2"><div class="dropArea21"><div class="wDrop wD1"></div><div class="wDrop wD2"></div></div></div>');
		
	tilesContainer = $('<div class="tilesContainer"></div>');
	DragContainer = $('<div id="dragPag"></div>');
	
	nexrpre = $('<div class="nextpre"><div id="next"></div><div id="pre"></div><div id="sumbitBox"></div></div>');
        page0.append(table[0]);
	
	page0.append(footerContainer).append(DragContainer);
	page0.append(Partimage).append(dropDiv);
	page0.append(nexrpre);
        menuContainer.append(page0);
	page0.append(tilesContainer);
	page0.append('<div id="popup"><button type="submit" value="Ok" class="alertOkBtn">OK</button><div id="crtTxt"></div><div id="correct"></div><div id="Incorrect"></div><div id="IncrtTxt"></div><div id="inCorrect"></div></div><div id="blocker1"></div>');
	pageContainer.append(menuContainer1);
	pageContainer.append(menuContainer);
	
        $('body').append(pageContainer);
        
	toggleClassButton($(this),'icoInfoMove');
	$(".icoInfo").on('click',function () {
	    $('.question,#blocker').show();
	    toggleClassButton($(".icoInfo"),'icoInfoMove');
	    $('#icoinf').css('background-image', 'url('+'./images/btInfoOn.png'+')');
	});
	
	$('#next').css({'display':'block'});
	//validation();
	loadProperty(currentQues);
	LoadTiles();
	ApplyDraggables();
	
	

	eventBroker = _({}).extend(require('chaplin/lib/event_broker'));
	eventBroker.publishEvent("#fetch", { type : 'state' }, function(state) {
	if (state) {
		_.each(JSON.parse(state), function(value, key, list) {
		    if(key == "currentQuestion") {
			currentQues = value;
		    }
		    else if (key == "currPage") {
			pageCount = value;
			$(".pagebtn").removeClass("activePageBtn");
			$(".pagebtn").eq((pageCount-1)).addClass("activePageBtn");
		    }
		    else if (key == "pageCount") {
			pageCount = value;
		    }
		    else if (key == "navStatus") {
			$('.pagebtnCon').html(value);
		    }
		    else if (key == "feedBack") {
			$('#sumbitBox').css('opacity',value);
		    }
		    else if (key == "icoreset") {
			$('.icoreset').css('opacity',value);			
		    }
		    else if (key == "ansArr") {
			completedAns = value;
		    }
		});
	}
	loadProperty(currentQues);
	forSaveFetch();
	saveFetchSubmit();
    });
	setTimeout(function(){
		$('body').css({'-webkit-transform':'scale(1)'});  
	  },1000);
            

	$(".icoReset").bind('click',resetfn);
    
	$("#sumbitBox").bind('click',submit);

	$('#next').bind('click',NextFun);
	$('#pre').bind('click',PreFun);

    if (pageCount == 2) $('#next').css({'display':'block'});
    
    audinst.play();

});
 
function loadProperty(Ques){
    $("#container0,#container1,#container2,#container3,#container4").html("");
    var divs=[];
    var element;
    for(var i=0;i<imgDetails[Ques].imgCount;i++,cell++){
	if (i%10==0 ) {
	    if(i!=0) divs.push(element);
	    cell=0;
	    element=$('<table><tr><td></td><td></td><td></td><td></td><td></td></tr><tr><td></td><td></td><td></td><td></td><td></td></tr></table>');
	}
	element.find("td").eq(cell).append('<div class="dragDiv"><img src="./images/'+imgDetails[Ques].imgSrc+'" width="40" height="40"/><div>');
    }
	divs.push(element);
    
    //append table
    for (var i=0;i<divs.length;i++)
    {
	$("#container"+i).append(divs[i]).show();
    }
    
    $(".dropPlace,.wDrop").text("");
    $("#sumbitBox").removeClass("correctImg");
    $("#sumbitBox").removeClass("incorrectImg");
    initialPos = $("#containerMain").html();
    
    loadValues();
    
}    

function forSaveFetch(){
    if(currentQues <3) {
	$('#next').css({'display':'block'});
    }else{
	$('#next').css({'display':'none'});
    }
    
    
    if(currentQues > 0){
	$('#pre').css({'display':'block'});
    }else{
	$('#pre').css({'display':'none'});
    }
    
    
//    $(".dragDiv").draggable({
//	containment:'#containerMain',
//	stack:'.dragDiv',
//	zIndex: 100000,
//	stop:function(){
//	    $(".icoReset").css({'opacity':'1'});
//	    isDrag = false;
//	    CheckDropComplete();
//	}
//    });
//    
    
     $('.dropPlace').find('.tile').draggable({
	containment:'#dragPag',
	stack:'.tile',	
	zIndex: 100000,
	stop:function(event,ui){
	    $(this).remove();
	    CheckDropComplete();
	}
    });
     
     
     $('.wDrop').find('.tile').draggable({
	containment:'#dragPag',
	stack:'.tile',
	zIndex: 100000,
	stop:boterToggle
	//stop:function(event,ui){
	//    $(this).remove();
	//    CheckDropComplete();
	//}
    });  
    
}


function NextFun(){
    $("#page_1_icon").addClass("activePageBtn").addClass("visitedPage");
    
    $('.icoReset').css({'opacity':'0.5'});

    
    if(currentQues <3) {
	currentQues++;
	loadProperty(currentQues);
	
	$('#pre').css({'display':'block'});
	$('#next').css({'display':'block'});
	
    }
    
    pageCount++;
    if (pageCount==4) {
	$('#next').css({'display':'none'});
    }
	$(".pagebtn").removeClass("activePageBtn");
	$(".pagebtn").eq(currentQues).addClass("activePageBtn").addClass("visitedPage");
	
    $('.icoReset').css({'opacity':'0.5'});
    
    //CheckDropComplete();
    loadValues();
}
    
function PreFun(){
        
    $('.icoReset').css({'opacity':'0.5'});

    
    if(currentQues > 0) {
	currentQues--;
	loadProperty(currentQues);
	
	$('#pre').css({'display':'block'});
	$('#next').css({'display':'block'});
    }
	
	if(currentQues > 0) {
	    $('#pre').css({'display':'block'});
	    $('#next').css({'display':'block'});
	}else{
	    $('#pre').css({'display':'none'});
	    $('#next').css({'display':'block'});
	}
	
     pageCount--;
          $(".pagebtn").removeClass("activePageBtn");
          $("#page_"+pageCount+"_icon").addClass("activePageBtn").addClass("visitedPage");
	  
	  
    $('.icoReset').css({'opacity':'0.5'});
    //CheckDropComplete();
    loadValues();
}

function submit()
{
    if ($("#sumbitBox").css("opacity") == "1") {
	    if (currentQues==3) {
		$('#next').css({'display':'none'});
	    }
	    else{
		$('#next').css({'display':'block'});
	    }
	
	var r1 = $(completedAns[currentQues].tens).text() + $(completedAns[currentQues].ones).text();
	var r2 = $(completedAns[currentQues].ans1).text() + $(completedAns[currentQues].ans2).text();
	var r3 = imgDetails[currentQues].imgCount;
	
	if (parseInt(r3) == parseInt(r1) && parseInt(r3) == parseInt(r2))  {
	    $("#sumbitBox").addClass("correctImg");
	    completedAns[currentQues].submitImg = $("#sumbitBox").attr("class");
	}
	else{
	    $("#sumbitBox").addClass("incorrectImg");
	    completedAns[currentQues].submitImg = $("#sumbitBox").attr("class");
	}
    }
	
}
function instruct(){
    try{audinst.currentTime = 0;}catch(e){}
    try{audinst.pause();}catch(e){}
	$('.question,#blocker').hide();
	$('.icoInfo').css({'opacity':'1'});
	try {
		$('#instructions-button-container').unbind('click',openInst);
	} catch(e){}
		$('#instructions-button-container').bind('click',openInst);
}

function openInst(){
    	audinst.play();
	$('.question,#blocker').show();
	$('.icoInfo').css({'opacity':'0.5'});
}

function toggleClassButton(element,className){
	
	var currentButton=element;
	if(!currentButton.hasClass(className)){
		currentButton.addClass(className);
	}else{
		currentButton.removeClass(className);	
	}
}
function LoadTiles(){
    var leftV = 20;
    var topV = 10;
    for(var tile=0; tile<numTiles; tile++)
    {
	$('.tilesContainer').append('<div  id="aa'+tile+'" class="tile">'+mytile[tile]+'</div>');
	leftV = leftV + 84;
	$(".tilesContainer").find('.tile').eq(tile).css({"left":leftV,"top":topV});
	
    }

}

function ApplyDraggables(){
    try{
	$('.tile').draggable("destroy");
    }
    catch(e){}
    
    
    $('.tile').draggable({
	containment:'#dragPag',
	helper:"clone",
	revert:"invalid",
	zIndex: 10000,
    });
    
    
    $(".dropPlace").droppable({
	accept: ".tile",
	drop:function(event,ui){
	    	$(this).html($(ui.helper).clone().removeAttr('style').removeClass('ui-draggable').removeClass('ui-draggable-dragging').css({'z-index':'9999', 'border-radius':'10px', 'background':'#ffffff','border':'2px solid #fff', 'zIndex':'0'}).draggable());
	    $(this).find('.tile ').draggable({
		containment:'#dragPag',
		zIndex: 10000,
		stop: function (){
		    $(this).remove();
		    CheckDropComplete();
		}
	    });
	    CheckDropComplete();
	}
    });
    
    
    $(".wDrop").droppable({
	drop:function(event,ui){
	    $(this).html($(ui.helper).clone().removeAttr('style').removeClass('ui-draggable').removeClass('ui-draggable-dragging').css({'z-index':'9999', 'border-radius':'10px', 'background':'#ffffff','border':'2px solid #fff', 'zIndex':'0','margin-top':'-1px','margin-left':'-1px'}).draggable());
	    $(this).find('.tile').draggable({
		containment:'#dragPag',
		zIndex: 10000,
		stop:boterToggle,
		//stop: function (){
		//    
		//    
		//    $(this).remove();
		//    
		//    CheckDropComplete();
		//    
		//    
		//}
	    });
	    CheckDropComplete();
	    bordeFn($(this));
	}
    });
    
     

}
function resetfn(){
    if($('.icoReset').css('opacity') == 1) {
	$('#sumbitBox').css({'opacity':'0.5'});
	$('.icoReset').removeClass('icoResetMove');
	var to=setTimeout(function(){
	    clearInterval(to);
	    $('.icoReset').addClass('icoResetMove');
            $(".icoReset").css({'opacity':'0.5'});
	},200);
	$("#sumbitBox").removeClass("correctImg");
	$("#sumbitBox").removeClass("incorrectImg");
	
	for(var i=0;i<completedAns.length;i++){
	    completedAns[currentQues].tens = "";
	    completedAns[currentQues].ones = "";
	    completedAns[currentQues].ans1 = "";
	    completedAns[currentQues].ans2 = "";
	    completedAns[currentQues].opacity = 0.5;
	    completedAns[currentQues].submitImg = "submit.png";
	    completedAns[currentQues].submitopacity =0.5;
	}
	loadProperty(currentQues);
	ApplyDraggables();
	loadValues();
    }
}

function AudioState() {
    if (audinst.paused == false) {
	try{audinst.currentTime = 0;}catch(e){}
	try{audinst.pause();}catch(e){}
	audinst.play();
    }
    else{
	audinst.play()
    }
}

eventBroker = _({}).extend(require('chaplin/lib/event_broker'));
eventBroker.subscribeEvent('#doSave', function(state) {
    var state = {};    
    state = {"currentQuestion":currentQues, "pageCount":pageCount,"navStatus":$('.pagebtnCon').html(), "feedBack":$('#sumbitBox').css('opacity'),"icoreset":$('.icoreset').css('opacity'),"ansArr":completedAns}
    
    var message = {
	    type : 'state',
	    data : JSON.stringify(state)
    };
    eventBroker.publishEvent("#save", message);
});


function saveFunction(){
    eventBroker.publishEvent("#doSave");
}

function bordeFn(curT){
     if(curT.children().length <= 0){
	curT.css("border","1px dashed #2f5941");
    }
    else{
	curT.css("border","none");
    }
	
}


function CheckDropComplete() {
    if ($("#dp1").text() != "" && $("#dp2").text() != "" && $(".wD1").text() != "" && $(".wD2").text() != "") {
	$("#sumbitBox").css("opacity","1");
	$("#sumbitBox").removeClass("correctImg");
	$("#sumbitBox").removeClass("incorrectImg");
	completedAns[currentQues].submitopacity = $("#sumbitBox").css("opacity");
    }
    else{
	$("#sumbitBox").css("opacity","0.5");
	$("#sumbitBox").removeClass("correctImg");
	$("#sumbitBox").removeClass("incorrectImg");
	completedAns[currentQues].submitopacity = $("#sumbitBox").css("opacity");
    }
    
    if ($("#dp1").text() != "" || $("#dp2").text() != "" || $(".wD1").text() != "" || $(".wD2").text() != "" ) {
	$(".icoReset").css({'opacity':'1'});
    }
    else{
	if(isDrag) $(".icoReset").css({'opacity':'0.5'});
    }
    
    completedAns[currentQues].tens = $("#dp1").html();
    completedAns[currentQues].ones = $("#dp2").html();
    completedAns[currentQues].ans1 = $(".wD1").html();
    completedAns[currentQues].ans2 = $(".wD2").html();
    completedAns[currentQues].opacity = $(".icoReset").css("opacity");
    completedAns[currentQues].submitImg = $("#sumbitBox").attr("class");
    completedAns[currentQues].submitopacity = $("#sumbitBox").css("opacity");
}


function loadValues() {
    for(var i=0;i<completedAns.length;i++){
	$("#dp1").html(completedAns[currentQues].tens);
	$("#dp2").html(completedAns[currentQues].ones);
	$(".wD1").html(completedAns[currentQues].ans1);
	$(".wD2").html(completedAns[currentQues].ans2);
	$(".icoReset").css("opacity",completedAns[currentQues].opacity);
	$("#sumbitBox").addClass(completedAns[currentQues].submitImg );
	$("#sumbitBox").css("opacity",completedAns[currentQues].submitopacity);
    }
    //ApplyDraggables();
    forSaveFetch();
    naveBorderToggle();
}


function saveFetchSubmit() {
    if(completedAns[currentQues].submitImg != "submit.png"){
	$("#sumbitBox").removeClass("correctImg");
	$("#sumbitBox").removeClass("incorrectImg");
    }
    CheckDropComplete();
}


function boterToggle() {
    if ($(this).parent().children().length>0) {
	$(this).parent().css("border","1px dashed #2f5941");
    }
    $(this).remove();
    CheckDropComplete();
}

function naveBorderToggle() {
    if (completedAns[currentQues].ans1=="") {
	$(".wD1").css("border","1px dashed #2f5941");
    }
    else{
	$(".wD1").css("border","0");
    }
    if (completedAns[currentQues].ans2=="") {
	$(".wD2").css("border","1px dashed #2f5941");
    }
    else{
	$(".wD2").css("border","0");
    }
}

