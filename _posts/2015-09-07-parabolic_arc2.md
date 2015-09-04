---
title: d3
layout: post
postTitle: Parabolic Arc2
categories: SVG Shapes
---

<div class="row">
  <div class="col-sm-6">
    <h4 class="text-white">ポリゴンの辺の数を指定して描画ボタンを押してください</h4>
    <h4 class="text-white">描画された点をドラッグしてください</h4>
    <div id="svg01"></div>
  </div>
  <div class="col-sm-6">
    <div class="btn-group-vertical">
      <h4>ポリゴンの頂点の数を選択してください</h4>
      <span class="label">頂点の数</span>
      <select data-bind="options: polygon,
                       value: selectedPolygon,
                       valueAllowUnset: true"></select>
      <hr>
      <button 
        data-bind="click:draw" 
        id="drawBtn" 
        class="btn btn-info"
      >
        描画 （ draw ）
      </button>
      <br>
      <button data-bind="click:reset" class="btn btn-danger">Reset</button>
      <br>
      <button 
        data-bind="click:control" 
        id="controlBtn" 
        class="btn btn-warning"
      >
        Hide Control Line
      </button>
      <br>
      <button 
        data-bind="click:net" 
        id="netBtn" 
        class="btn btn-warning"
      >
        Hide Net
      </button>
    </div>
  </div>
</div>

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_SVG"></script>
<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>

<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  // Point Object
  function Point(x, y){
    this.x = x;
    this.y = y;
    return this;
  };

  // ko variables
  var self = this;
  // 頂点
  polygon = [3,4,5,6,7,8,9,10,11,12];
  self.selectedPolygon = ko.observable(polygon[0]);

  // variables
  var vertex　= []; // ポリゴンの頂点座標
  var controlPoints = []; // 制御点の座標
    
  var Q =[]; // point between A and B
  var R =[]; // point between B and C
  var P =[]; // touching point

  var width = 450,
     height = 450;

  // constants     
  var radiusOfControlPoints = 5;
  var radiusOfSepPoints = 2;  
  var colorOfControlPoints = "gold";
  var colorOfSepPoints = "grey"; 
  var colorOfControlLines = "#fff";
  var colorOfSepLines = "#ff0"; 
  var colorOfParabola = "lime"; 

  // svg 追加 
  var svg01 = d3.select("#svg01")
                .append("svg")
                .attr("height",height)
                .attr("width",width)
                .style("background","#111");

  // accessor function
  var polyLine = d3.svg.line()
                .x(function(d) { return d.x; })
                .y(function(d) { return d.y; })
                .interpolate("linear");  
  var parabola = d3.svg.line()
                .x(function(d) { return d.x; })
                .y(function(d) { return d.y; })
                .interpolate("linear");  
 
 /* ポリゴンの頂点座標を計算 */
 function getPolygon(sides){

    var pi = Math.PI;  
    var x,y;
    var radius = 200;
    var step = 2*pi / sides

    for (var i = 0; i < sides; i++) {

      x = radius * Math.cos(step*i)+width/2;
      y = radius * Math.sin(step*i)+height/2;

      vertex.push(new Point(x,y));

    };
 
 }; 

 /* 制御点の座標を計算 */
 function getControlPoints(sides){

  var Mx,My;
  var j;
  var t=0.5;
    for (var i=0; i<sides; i++){
      j = i+1;
      if (j == sides){ j = 0;};
      Mx = (1-t)*vertex[i].x + t*vertex[j].x;
      My = (1-t)*vertex[i].y + t*vertex[j].y;
      controlPoints.push(new Point(Mx,My));
      controlPoints.push(new Point(vertex[j].x,vertex[j].y));
    }
 };

 /* draw Net  */
 function drawNet(sides){
    Q=[];
    R=[];
    P=[];
    for (var i=0;i<controlPoints.length;i=i+2){
      getQR(i) 
    }

   // draw lines QR
    svg01.selectAll(".net")
      .data(Q)
     .enter()
      .append("line")
      .attr("x1",function(d){return d.x;})
      .attr("y1",function(d){return d.y;})
      .attr("x2",function(d,i){return R[i].x;})
      .attr("y2",function(d,i){return R[i].y;})
      .attr("class","net")
      .attr("id",function(d,i){return "net" + i;})
      .attr("stroke",function(){return colorOfSepLines;})
      .attr("opacity",1);
 
 }

 function getQR(i){

    var Ax,Ay,Bx,By,Cx,Cy,Qx,Qy,Rx,Ry,Px,Py;
    var j=i+2;
    if (j==controlPoints.length){j=0;};

    Ax = controlPoints[i].x;
    Ay = controlPoints[i].y;
    Bx = controlPoints[i+1].x;
    By = controlPoints[i+1].y;
    Cx = controlPoints[j].x;
    Cy = controlPoints[j].y;

    for (var t=0;t<=1;t=t+0.05){
      Qx = (1-t)*Ax + t*Bx;
      Qy = (1-t)*Ay + t*By;
      Q.push(new Point(Qx,Qy));
      Rx = (1-t)*Bx + t*Cx;
      Ry = (1-t)*By + t*Cy;
      R.push(new Point(Rx,Ry));
      Px = (1-t)*Qx + t*Rx;
      Py = (1-t)*Qy + t*Ry;
      P.push(new Point(Px,Py));
    };
 };

 //　初期描画処理
 this.draw=function(){

    vertex =[];
    controlPoints = [];

    svg01.selectAll(".controlLines").remove();
    svg01.selectAll(".controlPoints").remove();

    // ポリゴンの頂点座標を計算
    getPolygon(self.selectedPolygon());

    // 制御点の座標を計算
    getControlPoints(self.selectedPolygon());


    // draw net
    drawNet(self.selectedPolygon());  

    // draw parabola
    svg01.append("path")
       .attr("d", function(){return parabola(P) + "Z"})
       .attr("stroke", function(){return colorOfParabola})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","parabola")
       .attr("opacity",1);
/*
    svg01.selectAll(".controlPoints")
        .remove();
*/
    // draw controlLines
    svg01.append("path")
       .attr("d", function(d) { 
          return polyLine(controlPoints) + "Z"; }) //<- Z コマンドで線を閉じる
       .attr("stroke", function(){return colorOfControlLines})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","controlLines")
       .attr("opacity",1);

    // draw control Points   
    svg01.selectAll(".controlPoints")
      .data(controlPoints)
     .enter()
      .append("circle")
      .attr("cx",function(d){return d.x;})
      .attr("cy",function(d){return d.y;})
      .attr("r",function(){return radiusOfControlPoints})
      .attr("class","controlPoints")
      .attr("id", function(d,i){
        return i;
      })
      .style("fill",function(d,i){return colorOfControlPoints;})
      .call(drag);

 };

  //　
 function redraw(){

    // draw net
    Q=[];
    R=[];
    P=[];
    for (var i=0;i<controlPoints.length;i=i+2){
      getQR(i) 
    }

    // draw Q and R
    for (var i= 0; i<Q.length; i++){
    // draw lines QR
      var elNm = "#net" + i;
      var el = svg01.select(elNm);
    
      el.transition()
      .duration(0)
      .attr("x1",function(){return Q[i].x;})
      .attr("y1",function(){return Q[i].y;})
      .attr("x2",function(){return R[i].x;})
      .attr("y2",function(){return R[i].y;})
      .attr("class","net")
      .attr("stroke",function(){return colorOfSepLines;});
    }  

    // draw parabola
    svg01.selectAll(".parabola")
       .transition()
       .duration(0) 
       .attr("d", function(){return parabola(P) + "Z"})
       .attr("stroke", function(){return colorOfParabola})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","parabola");
 
    // draw controlLines
    svg01.selectAll(".controlLines")
       .transition()
       .duration(0) 
       .attr("d", function(d) { 
          return polyLine(controlPoints) + "Z"; }) //<- Z コマンドで線を閉じる
       .attr("stroke", function(){return colorOfControlLines})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","controlLines");
 };

  this.reset = function(){

    svg01.selectAll(".controlPoints")
        .remove();
    svg01.selectAll(".controlLines")
        .remove();
    svg01.selectAll(".net")
        .remove();
    svg01.selectAll(".parabola")
        .remove();

    controlPoints = []; 
    Q = [];
    R = [];
    P = [];

    $("#controlBtn").html("Hide Control Line")          
                  .removeClass("btn-success")
                  .addClass("btn-warning");          
    $("#netBtn").html("Hide Net")          
                  .removeClass("btn-success")
                  .addClass("btn-warning");          
            

  };
  
  this.control = function(){
    if (P.length==0){return};

    var opacity = svg01.select(".controlLines")
                            .attr("opacity");
                            
    if (opacity==0){
      $("#controlBtn").html("Hide Control Line")          
                    .removeClass("btn-success")
                    .addClass("btn-warning");          
    } else {
      $("#controlBtn").html("Show Control Line")
                    .removeClass("btn-warning")
                    .addClass("btn-success");          
    };                        
    svg01.select(".controlLines")
            .attr("opacity",function(){
              return opacity==0?1:0;
            })
  }
  this.net = function(){
    if (P.length==0){return};

    var opacity = svg01.selectAll(".net")
                            .attr("opacity");
                            
    if (opacity==0){
      $("#netBtn").html("Hide Net")          
                    .removeClass("btn-success")
                    .addClass("btn-warning");          
    } else {
      $("#netBtn").html("Show Net")
                    .removeClass("btn-warning")
                    .addClass("btn-success");          
    };                        
    svg01.selectAll(".net")
            .attr("opacity",function(){
              return opacity==0?1:0;
            })
  }

  // ドラッグ時の挙動
  var drag = d3.behavior.drag()
        //ドラッグ開始時の処理
       .on("dragstart", function(){
            d3.select(this).attr("opacity",0.4)
          }) 
       　//ドラッグ中の処理
       .on("drag", dragmove)
        //ドラッグ終了時の処理
       .on("dragend", function(){ 
            d3.select(this).attr("opacity",1)
  });

  // ドラッグ中の制御　画面をはみ出さない
  function dragmove(d){
    var cPoint = d3.select(this);
    var i = cPoint.attr("id");
    cPoint
      .attr("cx", 
        d.x = Math.max(radiusOfControlPoints, Math.min(width - radiusOfControlPoints, d3.event.x)))
      .attr("cy", 
        d.y = Math.max(radiusOfControlPoints, Math.min(height - radiusOfControlPoints, d3.event.y)));
 
    controlPoints[i][0] = cPoint.attr("cx");
    controlPoints[i][1] = cPoint.attr("cy"); 

    redraw(); 

  };              


};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>
