<!DOCTYPE html>
<html> 
  <head>
    <title>Blob Game</title> 
  </head>
  <body>
	<!--This draws the canvas on the webpage -->
    <canvas id="mycanvas"></canvas> 
  </body>
 
  <!-- Include the processing.js library -->
  <!-- See https://khanacademy.zendesk.com/hc/en-us/articles/202260404-What-parts-of-ProcessingJS-does-Khan-Academy-support- for differences -->
  <script src="https://cdn.jsdelivr.net/processing.js/1.4.8/processing.min.js"></script> 
  <script>
  var programCode = function(processingInstance) {
    with (processingInstance) {
      size(400, 400); 
      frameRate(60);

var spot = function(x,y,col,is,cx,cy,hp){
    this.x = x;
    this.y = y;
    this.cx = cx;
    this.cy = cy;
    this.co = col;
    this.is = is;
    this.hp = hp;
    this.mhp = hp;
    this.ra = 20;
    this.sh = 1;
    if(hp===null){this.hp = 25;
    this.mhp = 25;}
    //stroke(this.co);
    //fill(this.co,80);
    //strokeWeight(4);
    //rect(this.x,this.y,10,10);
};
var coins = 100;
var plrs = 2;
var kp = false;
var dmg = 0.0125;
var hurtAmp = 2;
    var BhurtAmp = 1;
    var RhurtAmp = 1;
    var GhurtAmp = 1;
    var YhurtAmp = 1;
var healthAmp = 1;
var winP = 50;
var winPs = [0,0,0,0];
var mp = false;
var critical = false;
var Rcritical = false;
var Gcritical = false;
var Ycritical = false;
var prec = 100;
var Rprec = 100;
var Gprec = 100;
var Yprec = 100;
var gamemode = 1;
var lvl = 1;
var plr = [150,250,500,8,2,2];
var lvlWorth = [100,150,200,250,400,600, 700, 1000, 1500, 3000, 1700, 1440];
//for presets: corex, corey, corehp, pointercount, hpMult, damageMult
var lvlPresets = [
    [[250,150,500,8,1,1]],
    [[250,150,750,8,1,1.25]],
    [[250,150,1000,8,2,2.25]],
    [[250,150,1000,24,1,1.25]],
    [[250,150,1500,16,3,3.75]], //boss..?
    [[200,150,1000,10,1,1.25],[250,250,1000,10,1,1.25]],
    [[200,150,1500,10,1,1.25],[250,250,1500,10,1,1.25]],
    [[200,150,1800,10,1,2.25],[250,250,1800,10,1,2.25]],
    [[200,150,1600,14,1,4.25],[250,250,1600,14,1,4.25]],
    [[200,150,2000,20,4,6.25],[250,250,2000,20,4,6.25]], //boss
    [[250,150,15000,20,0.05,6.25]], // fun level
    [[150,150,1500,10,1,1.25],[250,250,1500,10,0.5,1.25],[250,150,1500,10,1,1.25]],
    [[150,150,2000,10,1,1.25],[250,250,2000,10,0.6,1.25],[250,150,1800,10,1,2.25]],
    [[150,150,2500,12,1.5,1.25],[250,250,1500,20,0.7,1.25],[250,150,2100,10,1,3.25]],
    [[150,150,2500,14,2,1.25],[250,250,1900,25,0.8,1.25],[250,150,2400,10,1,4.25]],
    [[150,150,3000,18,2.5,1.25],[250,250,2500,35,0.9,1.25],[250,150,2700,10,1,6.25]],
];
var lvlScreen = 0;
var shieldDefault = 1;
var ls = [1,1,1,1];
textFont(createFont("monospace"));

var squares = [[new spot(150,250,color(255,0,255),true,150,250,500*healthAmp)],[new spot(250,150,color(255,0,255),true,250,150,500*healthAmp)],[new spot(150,150,color(255,0,255),true,150,150,500*healthAmp)],[new spot(250,250,color(255,0,255),true,250,250,500*healthAmp)]];
if(plrs === 3){squares[0][0].x = 150;squares[0][0].y = 250;squares[1][0].x = 250;squares[1][0].y = 250;squares[2][0].x = 200;squares[2][0].y = 150;}
if(plrs===4){
    squares[0][0].x = 125;squares[0][0].y = 275;
    squares[1][0].x = 275;squares[1][0].y = 125;
    squares[2][0].x = 125;squares[2][0].y = 125;
    squares[3][0].x = 275;squares[3][0].y = 275;
}

spot.prototype.display = function(){
this.sh = shieldDefault;    
if(dist(this.x,this.y,this.cx,this.cy) <= constrain(200-ls[1],75,400)/2*((100-Rprec)/100+1) && this.co === color(255, 0, 0) &&this.is !== true ||

dist(this.x,this.y,this.cx,this.cy) <= constrain(200-ls[0],75,400)/2*((100-prec)/100+1) && this.co === color(0, 0, 100) &&this.is !== true ||

dist(this.x,this.y,this.cx,this.cy) <= constrain(200-ls[2],75,400)/2*((100-Gprec)/100+1) && this.co === color(0, 255, 0) &&this.is !== true||

dist(this.x,this.y,this.cx,this.cy) <= constrain(200-ls[3],75,400)/2*((100-Yprec)/100+1) && this.co === color(255, 255, 0) &&this.is !== true){this.sh = shieldDefault*3; }
    if(critical&&this.co === color(0,0,100)||Rcritical&&this.co === color(255,0,0)||Gcritical&&this.co===color(0,255,0)||Ycritical&&this.co===color(255,255,0)){this.sh+=2*shieldDefault;}
    if(!this.is && prec <= 80&& prec > 50 && this.co !== color(0, 0, 100) &&  dist(this.x,this.y, squares[0][0].x, squares[0][0].y) <= constrain(200-ls[0],75,400)/2){this.hp -= 0.1;}
    if(!this.is && Rprec <= 80&& Rprec > 50 && this.co !== color(255, 0, 0) &&  dist(this.x,this.y, squares[1][0].x, squares[1][0].y) <= constrain(200-ls[1],75,400)/2){this.hp -= 0.1;}
    if(!this.is && Gprec <= 80&& Gprec > 50 && this.co !== color(0, 255, 0) &&  dist(this.x,this.y, squares[2][0].x, squares[2][0].y) <= constrain(200-ls[2],75,400)/2){this.hp -= 0.1;}
    if(!this.is && Yprec <= 80&& Yprec > 50 && this.co !== color(255, 255, 0) &&  dist(this.x,this.y, squares[3][0].x, squares[3][0].y) <= constrain(200-ls[2],75,400)/2){this.hp -= 0.1;}
    
    if(!this.is && prec <= 0 && this.co === color(0, 0, 100)){this.hp -= dmg*10+1;}
    if(!this.is && Rprec <= 0 && this.co === color(255, 0, 0)){this.hp -= dmg*10+1;}
    if(!this.is && Gprec <= 0 && this.co === color(0, 255, 0)){this.hp -= dmg*10+1;}
    if(!this.is && Yprec <= 0 && this.co === color(255, 255, 0)){this.hp -= dmg*10+1;}
    
    if(this.is!==true){this.hp -= dmg/this.sh;}else{if(this.hp*3 < this.mhp){this.hp += this.mhp*this.sh/50000;}}
    
    if(!this.is &&critical&& prec > 0&&this.co!==color(0, 0, 100)){this.hp -= ((100-prec)*5*hurtAmp)/1000/this.sh;}else if(!this.is &&Rcritical&& Rprec > 0&&this.co!==color(255,0,0)){this.hp -= ((100-Rprec)*5*hurtAmp)/1000/this.sh;}else if(!this.is &&Gcritical&& Gprec > 0&&this.co!==color(0, 255, 0)){this.hp -= ((100-Gprec)*5*hurtAmp)/1000/this.sh;}else if(!this.is &&Ycritical&& Yprec > 0&&this.co!==color(255, 255, 0)){this.hp -= ((100-Yprec)*5*hurtAmp)/1000/this.sh;}
    
    if(this.sh!==shieldDefault &&this.hp < this.mhp){this.hp += this.mhp*this.sh/3000;}
    if(dist(this.x,this.y,this.cx,this.cy)>= 190){this.hp-=abs(dmg)*100+1;}
    if(dist(this.x,this.y,200,200)>= 170){this.hp-=abs(dmg)*100+1;}
    stroke(this.co);
    fill(this.co,80);
    strokeWeight(this.hp/this.mhp * 4 +constrain(this.hp-25,0,100)/50);
    rect(this.x,this.y,10,10);
    if(this.hp <= 0){this.co = color(255,255,255);}
    if(this.co !== color(0,0,100) && mp && dist(this.x+5,this.y+5,mouseX,mouseY) <= 15&& this.is!==true){this.hp -= this.mhp/4;}else if(this.co !== color(0,0,100) && this.is === true &&mp && dist(this.x+5,this.y+5,mouseX,mouseY) <= 15){this.hp-=2.5;}
    
};
var inmat = function(a){
    return constrain(ceil((a/2-1)/2),2,18);
};
var mhps = [500,500,500,500];
var mpr = false;
frameRate(max);
//var squares[0] = [new spot(150,250,color(255,0,255),true,150,250,500*healthAmp)];
//var squares[1] = [new spot(250,150,color(255,0,255),true,250,150,500*healthAmp)];
var pointSpeed = 10;
var areas = [2,2,2,2];
var pointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
var Rpointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
var Gpointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
var Ypointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
var keys = {};
var shop = [25,100,200,300,400,1000];

var reset = function(){
    ls = [0,0,0,0];
     pointSpeed = 10;
     areas = [2,2,2,2];
     pointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
     Rpointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
     Gpointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
     Ypointers = [[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180],[180,180]];
     dmg = 0.0125;
     hurtAmp = 2;
         BhurtAmp = 1;
         RhurtAmp = 1;
         GhurtAmp = 1;
         YhurtAmp = 1;
     healthAmp = 1;
     winP = 50;
     winPs = [0,0,0,0];
     mp = false;
     critical = false;
     Rcritical = false;
     Gcritical = false;
     Ycritical = false;
     prec = 100;
     Rprec = 100;
     Gprec = 100;
     Yprec = 100;
     squares = [[new spot(150,250,color(255,0,255),true,150,250,500*healthAmp)],[new spot(250,150,color(255,0,255),true,250,150,500*healthAmp)],[new spot(150,150,color(255,0,255),true,150,150,500*healthAmp)],[new spot(250,250,color(255,0,255),true,250,250,500*healthAmp)]];
if(plrs === 3){squares[0][0].x = 150;squares[0][0].y = 250;squares[1][0].x = 250;squares[1][0].y = 250;squares[2][0].x = 200;squares[2][0].y = 150;}
if(plrs===4){
    squares[0][0].x = 125;squares[0][0].y = 275;
    squares[1][0].x = 275;squares[1][0].y = 125;
    squares[2][0].x = 125;squares[2][0].y = 125;
    squares[3][0].x = 275;squares[3][0].y = 275;
    
}
};


draw = function() {
    mousePressed = function(){
        mp = true;mpr = true;
    };
    mouseReleased = function(){
        mpr = false;
    };
    keyPressed = function(){kp = true;keys[key.code]=true;};
    keyReleased = function(){kp = false;keys[key.code]=false;};
    switch(gamemode){
        case 1:
            background(245);
            stroke(0);
            fill(255);
            strokeWeight(5);
            rect(-5,100,410,100);
            rect(25,220,100,50,5);
            if(mp&&mouseX>=25&&mouseX<=125&&mouseY>=220&&mouseY<=270){gamemode = 2;}
            rect(25,280,100,50,5);
            if(mp&&mouseX>=25&&mouseX<=125&&mouseY>=280&&mouseY<=330){gamemode = 6;}
            rect(135,220,135,50,5);
            if(mp&&mouseX>=135&&mouseX<=235&&mouseY>=220&&mouseY<=270){gamemode = 7;reset();}
            fill(0, 0, 100,100);
            stroke(0,0,100,200);
            ellipse(335,300,100,100);
            fill(255, 0, 255,100);
            stroke(0, 0, 100);
            rect(330,295,10,10);
            fill(0);
            textSize(30);
            text("Play",40,255);
            text("Shop",40,315);
            text("Sandbox",140,255);
            textSize(50);
            text("THE", 150,100);
            textSize(70);
            text("BLOB GAME", 10,175);
            stroke(255, 0, 0);
            line(8,120,110,120);
            line(7,121,8,180);
            stroke(0, 0, 255);
            line(392,180,291,180);
            line(393,121,393,179);
            stroke(0, 255, 0);
            line(50,180,250,180);
            stroke(255, 255, 0);
            line(150,120,350,120);
            break;
        case 2:
            background(245);
            stroke(0);
            fill(255);
            strokeWeight(5);
            rect(-5,-5,410,100);
            rect(-5,280,410,125);
            fill(200);
            rect(-5,95,200,100);
            if(mouseX<=200&&mouseY>=95&&mouseY<=200&&mp){gamemode = 1;}
            if(mp&&mouseY>=280){
                pointSpeed = 10;
		shieldDefault = 1;
                plrs = (lvlPresets[lvl-1].length)+1;
                mhps = [plr[2]*plr[4],lvlPresets[lvl-1][0][2]*lvlPresets[lvl-1][0][4]];
                
                pointers = []; for(var a = 0; a < plr[3]; a++){pointers[a] = [180,180];}
                
                
                if(plrs>=3){
                    mhps[2] = lvlPresets[lvl-1][1][2]*lvlPresets[lvl-1][1][4];
                Gpointers = []; for(var a = 0; a < lvlPresets[lvl-1][1][3]; a++){Gpointers[a] = [180,180];} 

                }
                if(plrs>=4){
                    mhps[3] = lvlPresets[lvl-1][2][2]*lvlPresets[lvl-1][2][4];
                Ypointers = []; for(var a = 0; a < lvlPresets[lvl-1][2][3]; a++){Ypointers[a] = [180,180];}
                }
                healthAmp = lvlPresets[lvl-1][0][4];
                if(plrs>=2){
                Rpointers = []; for(var a = 0; a < lvlPresets[lvl-1][0][3]; a++){Rpointers[a] = [180,180];}
                RhurtAmp = lvlPresets[lvl-1][0][5];
                }if(plrs>=3){
                GhurtAmp = lvlPresets[lvl-1][1][5];
                }if(plrs>=4){
                YhurtAmp = lvlPresets[lvl-1][2][5];
                }
                BhurtAmp = plr[5];
                
                squares = [[new spot(plr[0],plr[1],color(255,0,255),true,plr[0],plr[1],plr[2]*plr[4])],[new spot(lvlPresets[lvl-1][0][0],lvlPresets[lvl-1][0][1],color(255,0,255),true,lvlPresets[lvl-1][0][0],lvlPresets[lvl-1][0][1],lvlPresets[lvl-1][0][2]*lvlPresets[lvl-1][0][4])]];
                if(plrs>=3){
                    squares[2] = [new spot(lvlPresets[lvl-1][1][0],lvlPresets[lvl-1][1][1],color(255,0,255),true,lvlPresets[lvl-1][1][0],lvlPresets[lvl-1][1][1],lvlPresets[lvl-1][1][2]*lvlPresets[lvl-1][1][4])];
                }else{
                    squares[2] = [new spot(150,150,color(255,0,255),true,150,150,500)];
                }
                if(plrs>=4){
                    squares[3] = [new spot(lvlPresets[lvl-1][2][0],lvlPresets[lvl-1][2][1],color(255,0,255),true,lvlPresets[lvl-1][2][0],lvlPresets[lvl-1][2][1],lvlPresets[lvl-1][2][2]*lvlPresets[lvl-1][0][4])];
                }else{
                    squares[3] = [new spot(250,250,color(255,0,255),true,250,250,500)];
                }
                gamemode = 3;
            }
            fill(150,0,0);
            textSize(70);
            text("Exit",10,170);
            fill(0);
            textSize(105);
            text("Play",80,370);
            textSize(30);
            text("Entering Level " + lvl, 30,40);
            fill(158, 158, 3);
            text(coins + " Coins", 10,80);
            fill(150,0,0);
            text((lvlPresets[lvl-1].length) + " Enemie(s)", 170, 80);
            
            break;
        case 3:
            background(190);
            critical = false; Rcritical = false;Gcritical = false;Ycritical = false;
            if(squares[0][0]!==0){if(prec<=50){critical = true;}}
            if(squares[1][0]!==0){if(Rprec<=50){Rcritical = true;}}
            if(squares[2][0]!==0){if(Gprec<=50){Gcritical = true;}}
            if(squares[3][0]!==0){if(Yprec<=50){Ycritical = true;}}
            if(mpr){fill(255, 0, 0,128);stroke(255, 0, 0,200);ellipse(mouseX,mouseY,30,30);}
            ls[0]=1;
            for(var a =0; a<squares[0].length;a++){
                        if(squares[0][a]!==0){
        
                if(squares[0][a]!==squares[0][a-1]&&a>0){
                    ls[0]++;
                }
                        }
            }
            if(mp && keys[101] &&dist(mouseX,mouseY,250,150)>=50){squares[0][squares[0].length] = new spot(round(mouseX/10)*10,round(mouseY/10)*10,color(0,0,100),false,150,250,constrain(ls[0]/2+(100-ls[0]/4),10,250)*healthAmp);}
            if(squares[0][0] !== 0 &&plrs>=1){strokeWeight(ls[0]/15);fill(128, 97, 244, ls[0]);stroke(115, 16, 173,ls[0]+12);ellipse(squares[0][0].x+5,squares[0][0].y+5,constrain(200-ls[0],75,400)*((100-prec)/100+1),constrain(200-ls[0],75,400)*((100-prec)/100+1));}
            if(squares[1][0] !== 0&&plrs>=2){strokeWeight(ls[1]/15);fill(255, 99, 182,ls[1]);stroke(230, 16, 70,ls[1]);ellipse(squares[1][0].x+5,squares[1][0].y+5,constrain(200-ls[1],75,400)*((100-Rprec)/100+1),constrain(200-ls[1],75,400)*((100-Rprec)/100+1));}
            if(squares[2][0] !== 0&&plrs>=3){strokeWeight(ls[2]/15);fill(99, 255, 128,ls[2]);stroke(16, 230, 20,ls[2]);ellipse(squares[2][0].x+5,squares[2][0].y+5,constrain(200-ls[2],75,400)*((100-Rprec)/100+1),constrain(200-ls[2],75,400)*((100-Rprec)/100+1));}
            if(squares[3][0] !== 0&&plrs>=3){strokeWeight(ls[3]/15);fill(245, 255, 99,ls[3]);stroke(185, 194, 15, ls[3]);ellipse(squares[3][0].x+5,squares[3][0].y+5,constrain(200-ls[3],75,400)*((100-Yprec)/100+1),constrain(200-ls[3],75,400)*((100-Yprec)/100+1));}
            ls[0]=1;
            if(plrs>=1){
                var losq = squares[0].length;
                for(var a =squares[0].length; a>0;a--){
                    if(squares[0][a] === 0){losq = a;}
                }
                for(var a =0; a<squares[0].length;a++){
                            if(squares[0][a]!==0){
            
                    squares[0][a].display();
                    if(squares[0][a]!==squares[0][a-1]&&a>0){
                        ls[0]++;
                    }
                            }
                }
                var oS = squares[0].length;
                var f = false;
                for(var b = 0; b<squares[0].length;b++){
                    if(!f){
                            if(squares[0][b]!==0){if(squares[0][b].co === color(255,255,255)){squares[0][b] = 0;}
                    for(var g = 0; g<pointers.length;g++){
                        if(dist(pointers[g][0],0,squares[0][b].x,0)<=10&&dist(0,pointers[g][1],0,squares[0][b].y)<=10&&dist(pointers[g][0],pointers[g][1],squares[0][0].x,squares[0][0].y)<=areas[0]*10){
                            if(pointers[g][0]!==squares[0][0].x||pointers[g][1]!==squares[0][0].y){
                            squares[0][losq] = new spot(pointers[g][0],pointers[g][1],color(0,0,100),false,150,250,100*healthAmp);f=true; 
                            }
                            pointers[g] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    pointers[g] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    pointers[g] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    pointers[g] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    pointers[g] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
            
                    }
                    }
                fill(0,0,255,80);
            //    rect(pointer[0],pointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<pointers.length; h++){
                        var dir = floor(random(4));
                        if(dir===0&&pointers[h][0]-(squares[0][0].x+areas[0]*10)<=0){
                            pointers[h][0]+=10;
                        }else if(dir === 1&&pointers[h][0]-(squares[0][0].x-areas[0]*10)>=0){
                            pointers[h][0]-=10;
                        }else if(dir===2&&pointers[h][1]-(squares[0][0].y+areas[0]*10)<=0){
                            pointers[h][1]+=10;
                        }else if(dir === 3&&pointers[h][1]-(squares[0][0].y-areas[0]*10)>=0){
                            pointers[h][1]-=10;
                        }
                    }
                }
            fill(0, 238, 255);stroke(17, 65, 133);strokeWeight(5);        
                for(var h = 0; h<pointers.length;h++){
                    rect(pointers[h][0],pointers[h][1],10,10);        
                }
            }
            var mat = function(a){
                return ceil((a*2+1)*4);
            };
            areas[0] = constrain(inmat(ls[0]),2,Infinity);
        
            //red
            if(plrs >=2){
                ls[1] = 1;
                var rosq = squares[1].length;
                for(var a =squares[1].length; a>0;a--){
                    if(squares[1][a] === 0){rosq = a;}
                }
                for(var a =0; a<squares[1].length;a++){
                            if(squares[1][a]!==0){
            
                    squares[1][a].display();
                    if(squares[1][a]!==squares[1][a-1]&&a>0){
                        ls[1]++;
                    }
                            }
                }
                var roS = squares[1].length;
                //while(squares[1].length === roS){
                for(var b = 0; b<squares[1].length;b++){
                    if(squares[1][b]!==0){
                    
                    for(var c = 0; c < squares[0].length;c++){
                        if(dist(squares[1][b].x,squares[1][b].y,squares[0][c].x,squares[0][c].y)<=squares[1][b].ra){
                            squares[0][c].hp -= random(0,15)*RhurtAmp/squares[0][c].sh;
                            squares[1][b].hp -= random(0,15)*BhurtAmp/squares[1][b].sh;
                        }
                    }
                    for(var h = 0; h<Rpointers.length; h++){
                        if(dist(Rpointers[h][0],0,squares[1][b].x,0)<=10&&dist(0,Rpointers[h][1],0,squares[1][b].y)<=10&&dist(Rpointers[h][0],Rpointers[h][1],squares[1][0].x,squares[1][0].y)<=areas[1]*10){
                            if(Rpointers[h][0]!==squares[1][0].x||Rpointers[h][1]!==squares[1][0].y){
                            squares[1][rosq] = new spot(Rpointers[h][0],Rpointers[h][1],color(255,0,0),false,250,150,100*healthAmp); 
                            }
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    Rpointers[h] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    Rpointers[h] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    Rpointers[h] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    Rpointers[h] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
                    if(squares[1][b].co===color(255,255,255)){squares[1][b] = 0;}
                    }
            //}
                fill(255,0,0,80);
                //rect(Rpointer[0],Rpointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<Rpointers.length;h++){
                        var dir = floor(random(4));
                        if(dir===0&&Rpointers[h][0]-(squares[1][0].x+areas[1]*10)<=0){
                            Rpointers[h][0]+=10;
                        }else if(dir === 1&&Rpointers[h][0]-(squares[1][0].x-areas[1]*10)>=0){
                            Rpointers[h][0]-=10;
                        }else if(dir===2&&Rpointers[h][1]-(squares[1][0].y+areas[1]*10)<=0){
                            Rpointers[h][1]+=10;
                        }else if(dir === 3&&Rpointers[h][1]-(squares[1][0].y-areas[1]*10)>=0){
                            Rpointers[h][1]-=10;
                        }
                    }
                }
                fill(255, 162, 0);stroke(237, 68, 31);strokeWeight(5);
                for(var h = 0; h<Rpointers.length;h++){
                        rect(Rpointers[h][0],Rpointers[h][1],10,10);
                }
            }
                var mat = function(a){
                return ceil((((a*2)+1))*4);
            };
            areas[1] = constrain(inmat(ls[1]),2,Infinity);
            ls[2] = 1;
            if(plrs>=3){
                var gosq = squares[2].length;
                for(var a =squares[2].length; a>0;a--){
                    if(squares[2][a] === 0){gosq = a;}
                }
                for(var b = 0; b<squares[2].length;b++){
                    if(squares[2][b]!==0){
                    ls[2]++;
                    
                    for(var c = 0; c < squares[0].length;c++){
                    if(dist(squares[2][b].x,squares[2][b].y,squares[0][c].x,squares[0][c].y)<=squares[2][b].ra){
                        squares[0][c].hp -= random(0,15)*GhurtAmp/squares[0][c].sh;
                        squares[2][b].hp -= random(0,15)*BhurtAmp/squares[2][b].sh;
                    }
                    }
                    for(c = 0; c < squares[1].length;c++){
                    if(dist(squares[2][b].x,squares[2][b].y,squares[1][c].x,squares[1][c].y)<=squares[2][b].ra){
                        squares[1][c].hp -= random(0,15)*GhurtAmp/squares[1][c].sh;
                        squares[2][b].hp -= random(0,15)*RhurtAmp/squares[2][b].sh;
                    }
                    }
                    
                    for(var h = 0; h<Gpointers.length; h++){
                        if(dist(Gpointers[h][0],0,squares[2][b].x,0)<=10&&dist(0,Gpointers[h][1],0,squares[2][b].y)<=10&&dist(Gpointers[h][0],Gpointers[h][1],squares[2][0].x,squares[2][0].y)<=areas[2]*10){
                            if(Gpointers[h][0]!==squares[2][0].x||Gpointers[h][1]!==squares[2][0].y){
                            squares[2][gosq] = new spot(Gpointers[h][0],Gpointers[h][1],color(0,255,0),false,250,150,100*healthAmp); 
                            }
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    Gpointers[h] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    Gpointers[h] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    Gpointers[h] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    Gpointers[h] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
                    squares[2][b].display();
                    if(squares[2][b].co===color(255,255,255)){squares[2][b] = 0;}
                    }
            //}
                fill(255,0,0,80);
                //rect(Rpointer[0],Rpointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<Gpointers.length;h++){
                        var dir = floor(random(4));
                        if(dir===0&&Gpointers[h][0]-(squares[2][0].x+areas[2]*10)<=0){
                            Gpointers[h][0]+=10;
                        }else if(dir === 1&&Gpointers[h][0]-(squares[2][0].x-areas[2]*10)>=0){
                            Gpointers[h][0]-=10;
                        }else if(dir===2&&Gpointers[h][1]-(squares[2][0].y+areas[2]*10)<=0){
                            Gpointers[h][1]+=10;
                        }else if(dir === 3&&Gpointers[h][1]-(squares[2][0].y-areas[2]*10)>=0){
                            Gpointers[h][1]-=10;
                        }
                    }
                }
                fill(255, 162, 0);stroke(179, 255, 0);strokeWeight(5);
                for(var h = 0; h<Gpointers.length;h++){
                        rect(Gpointers[h][0],Gpointers[h][1],10,10);
                }
            }
                var mat = function(a){
                return ceil((((a*2)+1))*4);
            };
            areas[2] = constrain(inmat(ls[2]),2,Infinity);
            
            if(plrs>=4){
                ls[3] = 1;
                var yosq = squares[3].length;  
                for(var a =squares[3].length; a>0;a--){
                    if(squares[3][a] === 0){yosq = a;}
                }
                for(var b = 0; b<squares[3].length;b++){
                    if(squares[3][b]!==0){
                    ls[3]++;
                    
                    for(var c = 0; c < squares[0].length;c++){
                    if(dist(squares[3][b].x,squares[3][b].y,squares[0][c].x,squares[0][c].y)<=squares[3][b].ra){
                        squares[0][c].hp -= random(0,15)*YhurtAmp/squares[0][c].sh;
                        squares[3][b].hp -= random(0,15)*BhurtAmp/squares[3][b].sh;
                    }
                    }
                    for(c = 0; c < squares[1].length;c++){
                    if(dist(squares[3][b].x,squares[3][b].y,squares[1][c].x,squares[1][c].y)<=squares[3][b].ra){
                        squares[1][c].hp -= random(0,15)*YhurtAmp/squares[1][c].sh;
                        squares[3][b].hp -= random(0,15)*RhurtAmp/squares[3][b].sh;
                    }
                    }
                    for(c = 0; c < squares[2].length;c++){
                    if(dist(squares[3][b].x,squares[3][b].y,squares[2][c].x,squares[2][c].y)<=squares[3][b].ra){
                        squares[2][c].hp -= random(0,15)*YhurtAmp/squares[2][c].sh;
                        squares[3][b].hp -= random(0,15)*GhurtAmp/squares[3][b].sh;
                    }
                    }
                    for(var h = 0; h<Ypointers.length; h++){
                        if(dist(Ypointers[h][0],0,squares[3][b].x,0)<=10&&dist(0,Ypointers[h][1],0,squares[3][b].y)<=10&&dist(Ypointers[h][0],Ypointers[h][1],squares[3][0].x,squares[3][0].y)<=areas[3]*10){
                            if(Ypointers[h][0]!==squares[3][0].x||Ypointers[h][1]!==squares[3][0].y){
                            squares[3][yosq] = new spot(Ypointers[h][0],Ypointers[h][1],color(255,255,0),false,250,150,100*healthAmp); 
                            }
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    Ypointers[h] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    Ypointers[h] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    Ypointers[h] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    Ypointers[h] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
                    squares[3][b].display();
                    if(squares[3][b].co===color(255,255,255)){squares[3][b] = 0;}
                    }
            //}
                fill(255,0,0,80);
                //rect(Rpointer[0],Rpointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<Ypointers.length;h++){
                        var dir = floor(random(4));
                        if(dir===0&&Ypointers[h][0]-(squares[3][0].x+areas[3]*10)<=0){
                            Ypointers[h][0]+=10;
                        }else if(dir === 1&&Ypointers[h][0]-(squares[3][0].x-areas[3]*10)>=0){
                            Ypointers[h][0]-=10;
                        }else if(dir===2&&Ypointers[h][1]-(squares[3][0].y+areas[3]*10)<=0){
                            Ypointers[h][1]+=10;
                        }else if(dir === 3&&Ypointers[h][1]-(squares[3][0].y-areas[3]*10)>=0){
                            Ypointers[h][1]-=10;
                        }
                    }
                }
                fill(255, 162, 0);stroke(179, 255, 0);strokeWeight(5);
                for(var h = 0; h<Ypointers.length;h++){
                        rect(Ypointers[h][0],Ypointers[h][1],10,10);
                }
            }
                var mat = function(a){
                return ceil((((a*2)+1))*4);
            };
            areas[3] = constrain(inmat(ls[3]),2,Infinity);
            
            fill(10, 0, 145);
            textSize(30);
            text("Population: " + ls[0],10,30);
            if(squares[0][0]!==0){
                prec = round(squares[0][0].hp/squares[0][0].mhp*1000)/10;
                if(squares[0][0]===0){prec = 0;}
                text(prec + "%",300,30);
            }else{text("Dead...",300,30);prec = 0;}
            fill(255,0,10);
            text("Population: "+ls[1],130,395);
            if(squares[1][0]!==0){
                Rprec = round(squares[1][0].hp/squares[1][0].mhp*1000)/10;
                if(squares[1][0]===0){Rprec = 0;}
                Gprec = round(squares[2][0].hp/squares[2][0].mhp*1000)/10;
                if(squares[2][0]===0){Gprec = 0;}
                Yprec = round(squares[3][0].hp/squares[3][0].mhp*1000)/10;
                if(squares[3][0]===0){Yprec = 0;}
                text(Rprec + "%",10,395);
            }else{text("Dead...",10,395);Rprec = 0;}
            if(plrs === 2){
                winP = 3*(prec/(prec+Rprec))+(ls[0]/(ls[0]+ls[1]));
                winP/=4;
                winP = round(winP*1000)/10;
                winPs[0] = winP;
                winPs[1] = round(1000-winPs[0]*10)/10;
            }else if(plrs === 1){
                winPs[0] = 100;
                winPs[1] = 0;
            }else if(plrs === 3){
                winPs[0] = round((3*(prec/(prec+Rprec+Gprec))+(ls[0]/(ls[0]+ls[1]+ls[2])))/4*1000)/10;
                winPs[1] = round((3*(Rprec/(prec+Rprec+Gprec))+(ls[1]/(ls[0]+ls[1]+ls[2])))/4*1000)/10;
                winPs[2] = round((3*(Gprec/(prec+Rprec+Gprec))+(ls[2]/(ls[0]+ls[1]+ls[2])))/4*1000)/10;
            }else{
                winPs[0] = round((3*(prec/(prec+Rprec+Gprec+Yprec))+(ls[0]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
                winPs[1] = round((3*(Rprec/(prec+Rprec+Gprec+Yprec))+(ls[1]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
                winPs[2] = round((3*(Gprec/(prec+Rprec+Gprec+Yprec))+(ls[2]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
                winPs[3] = round((3*(Yprec/(prec+Rprec+Gprec+Yprec))+(ls[3]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
            }
            fill(0,0,100);
            textSize(20);
            text(winPs[0] + "% in blue's favor", 10,60);
            fill(255, 0, 0);
            textSize(20);
            text(winPs[1] + "% in red's favor", 10,365);
            if(plrs>=3){
                textSize(13);
                fill(0,100,0);
                text(winPs[2] + "% in green's favor",10,80);
                text(Gprec+"%",330,80);
                text("Population: " + ls[2],200,80);
            }
            if(plrs>=4){
                textSize(13);
                fill(99, 99, 0);
                text(winPs[3] + "% in yellow's favor",10,95);
                text(Yprec+"%",330,95);
                text("Population: " + ls[3],200,95);
            }
            fill(87, 87, 87);
            stroke(64, 64, 64);
            strokeWeight(5);
            rect(10,100,50,225,5);
            fill(255,255,0);
            stroke(200,200,0);
            rect(20,110,30,205,2);
            fill(0,255,0);
            stroke(0,200,0);
            rect(20,110+205*(winPs[1]/100)+205*(winPs[0]/100),30,205*(winPs[2]/100),2);
            fill(255, 0, 0);
            stroke(200, 0, 0);
            rect(20,110+205*(winPs[0]/100),30,205*(winPs[1]/100),2);
            fill(0, 0, 150);
            stroke(0, 0, 100);
            rect(20,110,30,205*(winPs[0]/100),2);
            if(winPs[0]>50){fill(0, 0, 150);stroke(0, 0, 100);}else
            if(winPs[1]>50){fill(255, 0, 0);stroke(200, 0, 0);}else
            if(winPs[2]>50){fill(0, 255, 0);stroke(0, 200, 0);}else
            if(winPs[3]>50){fill(255, 255, 0);stroke(200, 200, 0);}else
            {stroke(100, 100, 100);fill(150, 150, 150);}
            if(plrs >= 2){
                rect(15,205*(winPs[0]/100)+105,40,10,1);
            }if(plrs>=3){
                rect(15,205*(winPs[0]/100)+205*(winPs[1]/100)+105,40,10,1);
            }if(plrs>=4){
                rect(15,205*(winPs[0]/100)+205*(winPs[1]/100)+105+205*(winPs[2]/100),40,10,1);
            }
            if(winPs[0]>=92.5){gamemode = 4;coins+=lvlWorth[lvl-1];}
            if(winPs[0]<=7.5){gamemode = 5;}
             ls[0] = 0;
             break;
        case 4:
            background(245);
            fill(255);
            stroke(0,0,100);
            strokeWeight(5);
            rect(-7,100,210,310);
            rect(202,100,200,310);
            fill(0,0,100);
            textSize(80);
            text("WIN!",110,70);
            textSize(40);
            text("Main Menu",50,200,180,200);
            text("Next Level (" + (lvl+1) + ")",240,200,180,200);
            if(mp){
                if(mouseX<=200&&mouseY>=100){lvl+=1;gamemode = 1;}
                if(mouseX>=200&&mouseY>=100){lvl+=1;gamemode = 2;}
            }
            break;
        case 5:
            background(245);
            fill(255);
            stroke(100,0,0);
            strokeWeight(5);
            rect(-7,100,210,310);
            rect(202,100,200,310);
            fill(200,0,0);
            textSize(80);
            text("FAIL!",110,70);
            textSize(40);
            text("Main Menu",50,200,180,200);
            text("Retry",240,200,180,200);
            if(mp){
                if(mouseX<=200&&mouseY>=100){coins+=25;gamemode = 1;}
                if(mouseX>=200&&mouseY>=100){coins+=25;gamemode = 2;}
            }
            break;
        case 6:
            background(245);
            fill(255);
            stroke(0,0,100);
            strokeWeight(5);
            rect(-5,10,410,175);
            rect(10,200,100,50,4);
            if(mp&&mouseX>=10&&mouseX<=110&&mouseY>=200&&mouseY<=250&&coins>=shop[0]){coins-=shop[0];plr[2] += 125; shop[0] *= 1.5; shop[0] = round(shop[0]);}
            rect(120,200,210,50,4);
            if(mp&&mouseX>=120&&mouseX<=330&&mouseY>=200&&mouseY<=250&&coins>=shop[1]){coins-=shop[1];plr[3] += 3; shop[1] *= 3; shop[1] = round(shop[1]);}
            rect(10,260,100,50,4);
            if(mp&&mouseX>=10&&mouseX<=110&&mouseY>=260&&mouseY<=310&&coins>=shop[3]){coins-=shop[3];plr[2] *= 2; shop[3] = pow(shop[3],1.5); shop[3] = round(shop[3]);}
            rect(120,260,100,50,4);
            if(mp&&mouseX>=120&&mouseX<=220&&mouseY>=260&&mouseY<=310&&coins>=shop[4]){coins-=shop[4];plr[5] += 1; shop[4] *= 4; shop[4] = round(shop[4]);}
            rect(230,260,100,50,4);
            if(mp&&mouseX>=230&&mouseX<=330&&mouseY>=260&&mouseY<=310&&coins>=shop[5]){coins-=shop[5];plr[5] *= 2; shop[5] *= 5; shop[5] = round(shop[5]);}
            
            fill(230);
            rect(250,340,155,105,3);
            if(mpr&&mouseX>=250&&mouseY>=340){gamemode = 1;}
            fill(0,0,200);
            textSize(75);
            text("SHOP", 110, 80);
            textSize(11);
            text("+25% Core Health: " + shop[0] + " Coins",22,207,100,50);
            text("+3 Pointers: " + shop[1] + " Coins",147,222,200,50);
            text("*2 HP Mult: " + shop[3] + " Coins",22,272,100,50);
            text("+1 DMG Mult: " + shop[4] + " Coins",127,272,100,50);
            text("*2 DMG Mult: " + shop[5] + " Coins",237,273,100,50);
            
            textSize(60);
            fill(0,0,255);
            text("Exit", 255,400);
            textSize(40);
            fill(200,200,0);
            text(coins + " Coins",110,150);
            break;
        case 7:
            background(190);
            critical = false; Rcritical = false;Gcritical = false;Ycritical = false;
            if(squares[0][0]!==0){if(prec<=50){critical = true;}}
            if(squares[1][0]!==0){if(Rprec<=50){Rcritical = true;}}
            if(squares[2][0]!==0){if(Gprec<=50){Gcritical = true;}}
            if(squares[3][0]!==0){if(Yprec<=50){Ycritical = true;}}
            if(mpr){fill(255, 0, 0,128);stroke(255, 0, 0,200);ellipse(mouseX,mouseY,30,30);}
            ls[0]=0;
            for(var a =0; a<squares[0].length;a++){
                        if(squares[0][a]!==0){
        
                if(squares[0][a]!==squares[0][a-1]&&a>0){
                    ls[0]++;
                }
                        }
            }
            if(mp && keys[101] &&dist(mouseX,mouseY,250,150)>=50){squares[0][squares[0].length] = new spot(round(mouseX/10)*10,round(mouseY/10)*10,color(0,0,100),false,150,250,constrain(ls[0]/2+(100-ls[0]/4),10,250)*healthAmp);}
            if(squares[0][0] !== 0 &&plrs>=1){strokeWeight(ls[0]/15);fill(128, 97, 244, ls[0]);stroke(115, 16, 173,ls[0]+12);ellipse(squares[0][0].x+5,squares[0][0].y+5,constrain(200-ls[0],75,400)*((100-prec)/100+1),constrain(200-ls[0],75,400)*((100-prec)/100+1));}
            if(squares[1][0] !== 0&&plrs>=2){strokeWeight(ls[1]/15);fill(255, 99, 182,ls[1]);stroke(230, 16, 70,ls[1]);ellipse(squares[1][0].x+5,squares[1][0].y+5,constrain(200-ls[1],75,400)*((100-Rprec)/100+1),constrain(200-ls[1],75,400)*((100-Rprec)/100+1));}
            if(squares[2][0] !== 0&&plrs>=3){strokeWeight(ls[2]/15);fill(99, 255, 128,ls[2]);stroke(16, 230, 20,ls[2]);ellipse(squares[2][0].x+5,squares[2][0].y+5,constrain(200-ls[2],75,400)*((100-Rprec)/100+1),constrain(200-ls[2],75,400)*((100-Rprec)/100+1));}
            if(squares[3][0] !== 0&&plrs>=3){strokeWeight(ls[3]/15);fill(245, 255, 99,ls[3]);stroke(185, 194, 15, ls[3]);ellipse(squares[3][0].x+5,squares[3][0].y+5,constrain(200-ls[3],75,400)*((100-Yprec)/100+1),constrain(200-ls[3],75,400)*((100-Yprec)/100+1));}
            ls[0]=0;
            if(plrs>=1){
                var losq = squares[0].length;
                for(var a =squares[0].length; a>0;a--){
                    if(squares[0][a] === 0){losq = a;}
                }
                for(var a =0; a<squares[0].length;a++){
                            if(squares[0][a]!==0){
            
                    squares[0][a].display();
                    if(squares[0][a]!==squares[0][a-1]&&a>0){
                        ls[0]++;
                    }
                            }
                }
                var oS = squares[0].length;
                var f = false;
                for(var b = 0; b<squares[0].length;b++){
                    if(!f){
                            if(squares[0][b]!==0){if(squares[0][b].co === color(255,255,255)){squares[0][b] = 0;}
                    for(var g = 0; g<pointers.length;g++){
                        if(dist(pointers[g][0],0,squares[0][b].x,0)<=10&&dist(0,pointers[g][1],0,squares[0][b].y)<=10&&dist(pointers[g][0],pointers[g][1],squares[0][0].x,squares[0][0].y)<=areas[0]*10){
                            if(pointers[g][0]!==squares[0][0].x||pointers[g][1]!==squares[0][0].y){
                            squares[0][losq] = new spot(pointers[g][0],pointers[g][1],color(0,0,100),false,150,250,100*healthAmp);f=true; 
                            }
                            pointers[g] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    pointers[g] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    pointers[g] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    pointers[g] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    pointers[g] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
            
                    }
                    }
                fill(0,0,255,80);
            //    rect(pointer[0],pointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<pointers.length; h++){
                        var dir = floor(random(4));
                        if(dir===0&&pointers[h][0]-(squares[0][0].x+areas[0]*10)<=0){
                            pointers[h][0]+=10;
                        }else if(dir === 1&&pointers[h][0]-(squares[0][0].x-areas[0]*10)>=0){
                            pointers[h][0]-=10;
                        }else if(dir===2&&pointers[h][1]-(squares[0][0].y+areas[0]*10)<=0){
                            pointers[h][1]+=10;
                        }else if(dir === 3&&pointers[h][1]-(squares[0][0].y-areas[0]*10)>=0){
                            pointers[h][1]-=10;
                        }
                    }
                }
            fill(0, 238, 255);stroke(17, 65, 133);strokeWeight(5);        
                for(var h = 0; h<pointers.length;h++){
                    rect(pointers[h][0],pointers[h][1],10,10);        
                }
            }
            var mat = function(a){
                return ceil((a*2+1)*4);
            };
            areas[0] = constrain(inmat(ls[0]),2,Infinity);
        
            //red
            if(plrs >=2){
                ls[1] = 0;
                var rosq = squares[1].length;
                for(var a =squares[1].length; a>0;a--){
                    if(squares[1][a] === 0){rosq = a;}
                }
                for(var a =0; a<squares[1].length;a++){
                            if(squares[1][a]!==0){
            
                    squares[1][a].display();
                    if(squares[1][a]!==squares[1][a-1]&&a>0){
                        ls[1]++;
                    }
                            }
                }
                var roS = squares[1].length;
                //while(squares[1].length === roS){
                for(var b = 0; b<squares[1].length;b++){
                    if(squares[1][b]!==0){
                    
                    for(var c = 0; c < squares[0].length;c++){
                        if(dist(squares[1][b].x,squares[1][b].y,squares[0][c].x,squares[0][c].y)<=squares[1][b].ra){
                            squares[0][c].hp -= random(0,15)*RhurtAmp/squares[0][c].sh;
                            squares[1][b].hp -= random(0,15)*BhurtAmp/squares[1][b].sh;
                        }
                    }
                    for(var h = 0; h<Rpointers.length; h++){
                        if(dist(Rpointers[h][0],0,squares[1][b].x,0)<=10&&dist(0,Rpointers[h][1],0,squares[1][b].y)<=10&&dist(Rpointers[h][0],Rpointers[h][1],squares[1][0].x,squares[1][0].y)<=areas[1]*10){
                            if(Rpointers[h][0]!==squares[1][0].x||Rpointers[h][1]!==squares[1][0].y){
                            squares[1][rosq] = new spot(Rpointers[h][0],Rpointers[h][1],color(255,0,0),false,250,150,100*healthAmp); 
                            }
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    Rpointers[h] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    Rpointers[h] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    Rpointers[h] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    Rpointers[h] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
                    if(squares[1][b].co===color(255,255,255)){squares[1][b] = 0;}
                    }
            //}
                fill(255,0,0,80);
                //rect(Rpointer[0],Rpointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<Rpointers.length;h++){
                        var dir = floor(random(4));
                        if(dir===0&&Rpointers[h][0]-(squares[1][0].x+areas[1]*10)<=0){
                            Rpointers[h][0]+=10;
                        }else if(dir === 1&&Rpointers[h][0]-(squares[1][0].x-areas[1]*10)>=0){
                            Rpointers[h][0]-=10;
                        }else if(dir===2&&Rpointers[h][1]-(squares[1][0].y+areas[1]*10)<=0){
                            Rpointers[h][1]+=10;
                        }else if(dir === 3&&Rpointers[h][1]-(squares[1][0].y-areas[1]*10)>=0){
                            Rpointers[h][1]-=10;
                        }
                    }
                }
                fill(255, 162, 0);stroke(237, 68, 31);strokeWeight(5);
                for(var h = 0; h<Rpointers.length;h++){
                        rect(Rpointers[h][0],Rpointers[h][1],10,10);
                }
            }
                var mat = function(a){
                return ceil((((a*2)+1))*4);
            };
            areas[1] = constrain(inmat(ls[1]),2,Infinity);
            ls[2] = 0;
            if(plrs>=3){
                var gosq = squares[2].length;
                for(var a =squares[2].length; a>0;a--){
                    if(squares[2][a] === 0){gosq = a;}
                }
                for(var b = 0; b<squares[2].length;b++){
                    if(squares[2][b]!==0){
                    ls[2]++;
                    
                    for(var c = 0; c < squares[0].length;c++){
                    if(dist(squares[2][b].x,squares[2][b].y,squares[0][c].x,squares[0][c].y)<=squares[2][b].ra){
                        squares[0][c].hp -= random(0,15)*GhurtAmp/squares[0][c].sh;
                        squares[2][b].hp -= random(0,15)*BhurtAmp/squares[2][b].sh;
                    }
                    }
                    for(c = 0; c < squares[1].length;c++){
                    if(dist(squares[2][b].x,squares[2][b].y,squares[1][c].x,squares[1][c].y)<=squares[2][b].ra){
                        squares[1][c].hp -= random(0,15)*GhurtAmp/squares[1][c].sh;
                        squares[2][b].hp -= random(0,15)*RhurtAmp/squares[2][b].sh;
                    }
                    }
                    
                    for(var h = 0; h<Gpointers.length; h++){
                        if(dist(Gpointers[h][0],0,squares[2][b].x,0)<=10&&dist(0,Gpointers[h][1],0,squares[2][b].y)<=10&&dist(Gpointers[h][0],Gpointers[h][1],squares[2][0].x,squares[2][0].y)<=areas[2]*10){
                            if(Gpointers[h][0]!==squares[2][0].x||Gpointers[h][1]!==squares[2][0].y){
                            squares[2][gosq] = new spot(Gpointers[h][0],Gpointers[h][1],color(0,255,0),false,250,150,100*healthAmp); 
                            }
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    Gpointers[h] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    Gpointers[h] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    Gpointers[h] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    Gpointers[h] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
                    squares[2][b].display();
                    if(squares[2][b].co===color(255,255,255)){squares[2][b] = 0;}
                    }
            //}
                fill(255,0,0,80);
                //rect(Rpointer[0],Rpointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<Gpointers.length;h++){
                        var dir = floor(random(4));
                        if(dir===0&&Gpointers[h][0]-(squares[2][0].x+areas[2]*10)<=0){
                            Gpointers[h][0]+=10;
                        }else if(dir === 1&&Gpointers[h][0]-(squares[2][0].x-areas[2]*10)>=0){
                            Gpointers[h][0]-=10;
                        }else if(dir===2&&Gpointers[h][1]-(squares[2][0].y+areas[2]*10)<=0){
                            Gpointers[h][1]+=10;
                        }else if(dir === 3&&Gpointers[h][1]-(squares[2][0].y-areas[2]*10)>=0){
                            Gpointers[h][1]-=10;
                        }
                    }
                }
                fill(255, 162, 0);stroke(179, 255, 0);strokeWeight(5);
                for(var h = 0; h<Gpointers.length;h++){
                        rect(Gpointers[h][0],Gpointers[h][1],10,10);
                }
            }
                var mat = function(a){
                return ceil((((a*2)+1))*4);
            };
            areas[2] = constrain(inmat(ls[2]),2,Infinity);
            
            if(plrs>=4){
                ls[3] = 0;
                var yosq = squares[3].length;  
                for(var a =squares[3].length; a>0;a--){
                    if(squares[3][a] === 0){yosq = a;}
                }
                for(var b = 0; b<squares[3].length;b++){
                    if(squares[3][b]!==0){
                    ls[3]++;
                    
                    for(var c = 0; c < squares[0].length;c++){
                    if(dist(squares[3][b].x,squares[3][b].y,squares[0][c].x,squares[0][c].y)<=squares[3][b].ra){
                        squares[0][c].hp -= random(0,15)*YhurtAmp/squares[0][c].sh;
                        squares[3][b].hp -= random(0,15)*BhurtAmp/squares[3][b].sh;
                    }
                    }
                    for(c = 0; c < squares[1].length;c++){
                    if(dist(squares[3][b].x,squares[3][b].y,squares[1][c].x,squares[1][c].y)<=squares[3][b].ra){
                        squares[1][c].hp -= random(0,15)*YhurtAmp/squares[1][c].sh;
                        squares[3][b].hp -= random(0,15)*RhurtAmp/squares[3][b].sh;
                    }
                    }
                    for(c = 0; c < squares[2].length;c++){
                    if(dist(squares[3][b].x,squares[3][b].y,squares[2][c].x,squares[2][c].y)<=squares[3][b].ra){
                        squares[2][c].hp -= random(0,15)*YhurtAmp/squares[2][c].sh;
                        squares[3][b].hp -= random(0,15)*GhurtAmp/squares[3][b].sh;
                    }
                    }
                    for(var h = 0; h<Ypointers.length; h++){
                        if(dist(Ypointers[h][0],0,squares[3][b].x,0)<=10&&dist(0,Ypointers[h][1],0,squares[3][b].y)<=10&&dist(Ypointers[h][0],Ypointers[h][1],squares[3][0].x,squares[3][0].y)<=areas[3]*10){
                            if(Ypointers[h][0]!==squares[3][0].x||Ypointers[h][1]!==squares[3][0].y){
                            squares[3][yosq] = new spot(Ypointers[h][0],Ypointers[h][1],color(255,255,0),false,250,150,100*healthAmp); 
                            }
                            var an = round(random(1,4));
                            switch(an){
                                case 1:
                                    Ypointers[h] = [0-round(random(0,80))*10,400+round(random(0,80))*10];
                                    break;
                                case 2:
                                    Ypointers[h] = [400+round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 3:
                                    Ypointers[h] = [0-round(random(0,80))*10,0-round(random(0,80))*10];
                                    break;
                                case 4:
                                    Ypointers[h] = [400+round(random(0,80))*10,400+round(random(0,80))*10];
                            }
                
                        }
                    }
                    squares[3][b].display();
                    if(squares[3][b].co===color(255,255,255)){squares[3][b] = 0;}
                    }
            //}
                fill(255,0,0,80);
                //rect(Rpointer[0],Rpointer[1],10,10);
                }
                for(var d = 0; d < pointSpeed; d++){
                    for(var h = 0; h<Ypointers.length;h++){
                        var dir = floor(random(4));
                        if(dir===0&&Ypointers[h][0]-(squares[3][0].x+areas[3]*10)<=0){
                            Ypointers[h][0]+=10;
                        }else if(dir === 1&&Ypointers[h][0]-(squares[3][0].x-areas[3]*10)>=0){
                            Ypointers[h][0]-=10;
                        }else if(dir===2&&Ypointers[h][1]-(squares[3][0].y+areas[3]*10)<=0){
                            Ypointers[h][1]+=10;
                        }else if(dir === 3&&Ypointers[h][1]-(squares[3][0].y-areas[3]*10)>=0){
                            Ypointers[h][1]-=10;
                        }
                    }
                }
                fill(255, 162, 0);stroke(179, 255, 0);strokeWeight(5);
                for(var h = 0; h<Ypointers.length;h++){
                        rect(Ypointers[h][0],Ypointers[h][1],10,10);
                }
            }
                var mat = function(a){
                return ceil((((a*2)+1))*4);
            };
            areas[3] = constrain(inmat(ls[3]),2,Infinity);
            
            fill(10, 0, 145);
            textSize(30);
            text("Population: " + ls[0],10,30);
            if(squares[0][0]!==0){
                prec = round(squares[0][0].hp*1000/squares[0][0].mhp)/10;
                if(squares[0][0]===0){prec = 0;}
                text(round(squares[0][0].hp*1000/squares[0][0].mhp)/10 + "%",300,30);
            }else{text("Dead...",300,30);prec = 0;}
            fill(255,0,10);
            text("Population: "+ls[1],130,395);
            if(squares[1][0]!==0){
                Rprec = round(squares[1][0].hp*1000/squares[1][0].mhp)/10;
                if(squares[1][0]===0){Rprec = 0;}
                Gprec = round(squares[2][0].hp*1000/squares[2][0].mhp)/10;
                if(squares[2][0]===0){Gprec = 0;}
                Yprec = round(squares[3][0].hp*1000/squares[3][0].mhp)/10;
                if(squares[3][0]===0){Yprec = 0;}
                text(round(squares[1][0].hp*1000/squares[0][0].mhp)/10 + "%",10,395);
            }else{text("Dead...",10,395);Rprec = 0;}
            if(plrs === 2){
                winP = 3*(prec/(prec+Rprec))+(ls[0]/(ls[0]+ls[1]));
                winP/=4;
                winP = round(winP*1000)/10;
                winPs[0] = winP;
                winPs[1] = round(1000-winPs[0]*10)/10;
            }else if(plrs === 1){
                winPs[0] = 100;
                winPs[1] = 0;
            }else if(plrs === 3){
                winPs[0] = round((3*(prec/(prec+Rprec+Gprec))+(ls[0]/(ls[0]+ls[1]+ls[2])))/4*1000)/10;
                winPs[1] = round((3*(Rprec/(prec+Rprec+Gprec))+(ls[1]/(ls[0]+ls[1]+ls[2])))/4*1000)/10;
                winPs[2] = round((3*(Gprec/(prec+Rprec+Gprec))+(ls[2]/(ls[0]+ls[1]+ls[2])))/4*1000)/10;
            }else{
                winPs[0] = round((3*(prec/(prec+Rprec+Gprec+Yprec))+(ls[0]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
                winPs[1] = round((3*(Rprec/(prec+Rprec+Gprec+Yprec))+(ls[1]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
                winPs[2] = round((3*(Gprec/(prec+Rprec+Gprec+Yprec))+(ls[2]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
                winPs[3] = round((3*(Yprec/(prec+Rprec+Gprec+Yprec))+(ls[3]/(ls[0]+ls[1]+ls[2]+ls[3])))/4*1000)/10;
            }
            fill(0,0,100);
            textSize(20);
            text(winPs[0] + "% in blue's favor", 10,60);
            fill(255, 0, 0);
            textSize(20);
            text(winPs[1] + "% in red's favor", 10,365);
            if(plrs>=3){
                textSize(13);
                fill(0,100,0);
                text(winPs[2] + "% in green's favor",10,80);
                text(Gprec+"%",330,80);
                text("Population: " + ls[2],200,80);
            }
            if(plrs>=4){
                textSize(13);
                fill(99, 99, 0);
                text(winPs[3] + "% in yellow's favor",10,95);
                text(Yprec+"%",330,95);
                text("Population: " + ls[3],200,95);
            }
            fill(87, 87, 87);
            stroke(64, 64, 64);
            strokeWeight(5);
            rect(10,100,50,225,5);
            fill(255,255,0);
            stroke(200,200,0);
            rect(20,110,30,205,2);
            fill(0,255,0);
            stroke(0,200,0);
            rect(20,110+205*(winPs[1]/100)+205*(winPs[0]/100),30,205*(winPs[2]/100),2);
            fill(255, 0, 0);
            stroke(200, 0, 0);
            rect(20,110+205*(winPs[0]/100),30,205*(winPs[1]/100),2);
            fill(0, 0, 150);
            stroke(0, 0, 100);
            rect(20,110,30,205*(winPs[0]/100),2);
            if(winPs[0]>50){fill(0, 0, 150);stroke(0, 0, 100);}else
            if(winPs[1]>50){fill(255, 0, 0);stroke(200, 0, 0);}else
            if(winPs[2]>50){fill(0, 255, 0);stroke(0, 200, 0);}else
            if(winPs[3]>50){fill(255, 255, 0);stroke(200, 200, 0);}else
            {stroke(100, 100, 100);fill(150, 150, 150);}
            if(plrs >= 2){
                rect(15,205*(winPs[0]/100)+105,40,10,1);
            }if(plrs>=3){
                rect(15,205*(winPs[0]/100)+205*(winPs[1]/100)+105,40,10,1);
            }if(plrs>=4){
                rect(15,205*(winPs[0]/100)+205*(winPs[1]/100)+105+205*(winPs[2]/100),40,10,1);
            }
            if(keys[101]){gamemode = 8;}
            if(keys[92]){gamemode = 1;}
             ls[0] = 0;
            break;
        case 8:
            background(245);
            stroke(99, 0, 0);
            fill(255,0,0);
            rect(350,-5,55,55);
            fill(0);
                textSize(20);
            text("Exit", 352.5,32);
        
            stroke(0);
            
            fill(0);
            textSize(20);
            text("Players",50,45);
            
            fill(255);
            strokeWeight(2);
            
            
            
            fill(130);
            rect(175, 50, 40,25, 4);
            rect(220, 50, 40,25, 4);
            fill(180);
            stroke(140);
            rect(50,50,75,25,3);
            fill(0);
            textSize(20);
            text(plrs,82,70);
            triangle(195,52.5, 180,72.5, 210,72.5);
            triangle(240,72.5, 225,52.5, 255,52.5);
            if(mouseX >= 350 && mouseY <= 50 && mp){gamemode = 7; reset();}
            if(mouseX >= 175 && mouseX <= 215 && mouseY >= 50 && mouseY <= 75 && mp && plrs < 4){plrs += 1;}
            if(mouseX >= 220 && mouseX <= 260 && mouseY >= 50 && mouseY <= 75&&mp && plrs > 1){plrs -= 1;}
        
            fill(0);
            text("Default Shield",50,195);
            fill(130);
            stroke(0);
            rect(175, 200, 40,25, 4);
            rect(220, 200, 40,25, 4);
            fill(180);
            stroke(140);
            rect(50,200,75,25,3);
            fill(0);
            textSize(20);
            text(shieldDefault,62,220);
            triangle(195,202.5, 180,222.5, 210,222.5);
            triangle(240,222.5, 225,202.5, 255,202.5);
            if(mouseX >= 175 && mouseX <= 215 && mouseY >= 200 && mouseY <= 225 && mp){shieldDefault += 0.1;}
            if(mouseX >= 220 && mouseX <= 260 && mouseY >= 200 && mouseY <= 225&&mp){shieldDefault -= 0.1;}
        
            fill(0);
            text("Health Amp",50,245);
            fill(130);
            stroke(0);
            rect(175, 250, 40,25, 4);
            rect(220, 250, 40,25, 4);
            fill(180);
            stroke(140);
            rect(50,250,75,25,3);
            fill(0);
            textSize(20);
            text(healthAmp,62,270);
            triangle(195,252.5, 180,272.5, 210,272.5);
            triangle(240,272.5, 225,252.5, 255,252.5);
            if(mouseX >= 175 && mouseX <= 215 && mouseY >= 250 && mouseY <= 275 && mp){healthAmp += 0.1;}
            if(mouseX >= 220 && mouseX <= 260 && mouseY >= 250 && mouseY <= 275&&mp){healthAmp -= 0.1;}
        
            fill(0);
            text("Natural Damage",50,345);
            fill(130);
            stroke(0);
            rect(175, 350, 40,25, 4);
            rect(220, 350, 40,25, 4);
            fill(180);
            stroke(140);
            rect(50,350,75,25,3);
            fill(0);
            textSize(20);
            text(dmg,62,370);
            triangle(195,352.5, 180,372.5, 210,372.5);
            triangle(240,372.5, 225,352.5, 255,352.5);
            if(mouseX >= 175 && mouseX <= 215 && mouseY >= 350 && mouseY <= 375 && mp){dmg += 0.001;}
            if(mouseX >= 220 && mouseX <= 260 && mouseY >= 350 && mouseY <= 375&&mp){dmg -= 0.001;}
        
            fill(0);
            text("Damage Amp",50,295);
            fill(130);
            stroke(0);
            rect(175, 300, 40,25, 4);
            rect(220, 300, 40,25, 4);
            fill(180);
            stroke(140);
            rect(50,300,75,25,3);
            fill(0);
            textSize(20);
            text(hurtAmp,62,320);
            triangle(195,302.5, 180,322.5, 210,322.5);
            triangle(240,322.5, 225,302.5, 255,302.5);
            if(mouseX >= 175 && mouseX <= 215 && mouseY >= 300 && mouseY <= 325 && mp){hurtAmp += 0.1;}
            if(mouseX >= 220 && mouseX <= 260 && mouseY >= 300 && mouseY <= 325&&mp){hurtAmp -= 0.1;}

            break;
    }
    mp = false;
};


   }};
  // Get the canvas that ProcessingJS will use
  var canvas = document.getElementById("mycanvas"); 
  // Pass the function to ProcessingJS constructor
  var processingInstance = new Processing(canvas, programCode); 
  </script>
</html>
