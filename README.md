# Tizen-App-JinZiQi
###概述
井字棋的棋盘共有3*3个格子。先行标示为绿色的叉（X），后行标示为红色的圈（O）。横向、纵向或斜线上有三个相同棋子的一方获胜。井字棋规则简单，易学易懂，常作为儿童开发智力的游戏。
###算法介绍
在该游戏中主要有三种游戏模式，分别是：
* 双人对战
* 人机对战（人先）
* 人机对战（机器先）

它们的算法分别在Main1.js, Main2.js, Main3.js中，下面介绍Main1.js为例。
####Main1.js
使用Lufylegend框架中的方法设置游戏全屏
<pre>
if(LGlobal.canTouch){
	LGlobal.stageScale = LStageScaleMode.EXACT_FIT;
	LSystem.screen(LStage.FULL_SCREEN);
}
</pre>
游戏事件监听
<pre>
function doScroll() {
	if(window.pageYOffset === 0) {
		window.scrollTo(0, 1);
	}
}
window.onload = function() {
	setTimeout(doScroll, 100);
	init(50,"mylegend",300,420,main,LEvent.INIT);
}
window.onorientationchange = function() {
	setTimeout(doScroll, 100);
};
window.onresize = function() {
	setTimeout(doScroll, 100);
}
</pre>
游戏参数初始化
<pre>
var backLayer,chessLayer,overLayer;
var statusText = new LTextField();
var statusContent="您先请吧……";
var matrix = [
	[0,0,0],
	[0,0,0],
	[0,0,0]
];
var usersTurn = true;
var step = 0;
var title = "井字棋";
var introduction = ""
var infoArr = [title,introduction];
</pre>
游戏初始化
<pre>
function main(){
	gameInit();
	addText();
	addLattice();	
}
function gameInit(){
	initLayer();
	addEvent();
}
function initLayer(){
	backLayer = new LSprite();
	addChild(backLayer);

	chessLayer = new LSprite();
	backLayer.addChild(chessLayer);

	overLayer = new LSprite();
	backLayer.addChild(overLayer);
}
</pre>
添加鼠标点击的事件监听处理方法
<pre>
function addEvent(){
	backLayer.addEventListener(LMouseEvent.MOUSE_DOWN,onDown);
}
function onDown(){
	var mouseX,mouseY;
	mouseX = event.offsetX;
	mouseY = event.offsetY;

	var partX = Math.floor(mouseX/100);
	var partY = Math.floor(mouseY/100);
	if(matrix[partX][partY]==0){
		if(usersTurn==false){
		usersTurn=true;
		matrix[partX][partY]=-1;
		step++;
		update(partX,partY);
		
		if(win(partX,partY)){
			statusContent = "帅呆了，你赢啦！点击屏幕重开游戏。";
			gameover();
			addText();
		}else if(isEnd()){
			statusContent = "平局啦~~点击屏幕重开游戏。";
			gameover();
			addText();
		}
		}
		else{
			usersTurn=false;
		matrix[partX][partY]=1;
		step++;
		update(partX,partY);
		
		if(win(partX,partY)){
			statusContent = "帅呆了，你赢啦！点击屏幕重开游戏。";
			gameover();
			addText();
		}else if(isEnd()){
			statusContent = "平局啦~~点击屏幕重开游戏。";
			gameover();
			addText();
		}
		}
	}
}
</pre>
添加文本标签的方法
<pre>
function addText(){
	statusText.size = 15;	
	statusText.weight = "bold";
	statusText.color = "white";
	statusText.text = statusContent;
	statusText.x = (LGlobal.width-statusText.getWidth())*0.5;
	statusText.y = 393;
	
	overLayer.addChild(statusText);
}
</pre>
添加边的方法
<pre>
function addLattice(){
	backLayer.graphics.drawRect(10,"dimgray",[0,0,300,420],true,"dimgray");
	backLayer.graphics.drawRect(10,"dimgray",[0,0,300,300],true,"lavender");
	for(var i=0;i<3;i++){
		backLayer.graphics.drawLine(3,"dimgray",[100*i,0,100*i,300]);
	}
	for(var i=0;i<3;i++){
		backLayer.graphics.drawLine(3,"dimgray",[0,100*i,300,100*i]);
	}
}
</pre>
更新游戏画面的方法
<pre>
function update(x,y){
	var v = matrix[x][y];
	if(v>0){
		chessLayer.graphics.drawArc(10,"green",[x*100+50,y*100+50,40,0,2*Math.PI]);
	}else if(v<0){
		chessLayer.graphics.drawLine(20,"#CC0000",[100*x+30,100*y+30,100*(x+1)-30,100*(y+1)-30]);
		chessLayer.graphics.drawLine(20,"#CC0000",[100*(x+1)-30,100*y+30,100*x+30,100*(y+1)-30]);
	}
}
</pre>
电脑AI下棋以及游戏结果判断
<pre>
function computerThink(){
	var b = best();
	var x = b.x;
	var y = b.y;
	matrix[x][y]=1;
	step++;
	update(x,y);
	
	if(win(x,y)){
		statusContent = "帅呆了，你赢啦！点击屏幕重开游戏。";
		gameover();
		addText();
	}else if(isEnd()){
		statusContent = "平局啦~~点击屏幕重开游戏。";
		gameover();
		addText();
	}else{
		statusContent = "该你了！！！";
		addText();
	}
}
function isEnd(){
	return step>=9;
}
function win(x,y){
	if(Math.abs(matrix[x][0]+matrix[x][1]+matrix[x][2])==3){
		return true;
	}
	if(Math.abs(matrix[0][y]+matrix[1][y]+matrix[2][y])==3){
		return true;
	}
	if(Math.abs(matrix[0][0]+matrix[1][1]+matrix[2][2])==3){
		return true;
	}
	if(Math.abs(matrix[2][0]+matrix[1][1]+matrix[0][2])==3){
		return true;
	}
	return false;
}
function best(){
	var bestx;
	var besty;
	var bestv=0;
	for(var x=0;x<3;x++){
		for(var y=0;y<3;y++){
			if(matrix[x][y]==0){
				matrix[x][y] = 1;
				step++;
				if(win(x,y)){
					step--;
					matrix[x][y] = 0;	
					return {'x':x,'y':y,'v':1000};
				}else if(isEnd()){
					step--;
					matrix[x][y]=0;	
					return {'x':x,'y':y,'v':0};
				}else{
					var v=worst().v;
					step--;
					matrix[x][y]=0;
					if(bestx==null || v>=bestv){
						bestx=x;
						besty=y;
						bestv=v;
					}
				}
			}
		}
	}
	return {'x':bestx,'y':besty,'v':bestv};
}
function worst(){
	var bestx;
	var besty;
	var bestv = 0;
	for(var x=0;x<3;x++){
		for(var y=0;y<3;y++){
			if(matrix[x][y] == 0){
				matrix[x][y] = -1;
				step++;
				if(win(x,y)){
					step--;
					matrix[x][y] = 0;	
					return {'x':x,'y':y,'v':-1000};
				}else if(isEnd()){
					step--;
					matrix[x][y]=0;	
					return {'x':x,'y':y,'v':0};;
				}else{
					var v=best().v;
					step--;
					matrix[x][y]=0;
					if(bestx==null || v<=bestv){
						bestx=x;
						besty=y;
						bestv=v;
					}
				}
				
			}
		}
	}
	return {'x':bestx,'y':besty,'v':bestv};
}
</pre>
游戏结束处理
<pre>
function gameover(){
	backLayer.removeEventListener(LMouseEvent.MOUSE_DOWN,onDown);
	backLayer.addEventListener(LMouseEvent.MOUSE_DOWN,function(){
		chessLayer.removeAllChild();
		backLayer.removeChild(chessLayer);
		backLayer.removeChild(overLayer);
		removeChild(backLayer);
		matrix = [
			[0,0,0],
			[0,0,0],
			[0,0,0]
		];
		step = 0;
		main();
		statusContent = "您先请吧……";
		addText();
	});
}
</pre>
给手机回退键添加监听
<pre>
window.addEventListener('tizenhwkey', function(e) {
	if(e.keyName == "back") {
		try {
			tizen.application.getCurrentApplication().exit();
		} catch (error) {
			console.error("getCurrentApplication(): " + error.message);
		}
	}
},false);
