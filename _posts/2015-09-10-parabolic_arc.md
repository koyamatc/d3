---
title: d3
layout: post
postTitle: Parabolic Arc
categories: SVG Shapes
---

<div class="row">
  <div class="col-sm-6">
    <h3 class="text-white">画面をクリックして、点を３個指定してください</h3>
    <h3 class="text-white">描画された点をドラッグしてください</h3>
    <div id="svg01"></div>
  </div>
  <div class="col-sm-6">
    <h4>線分の分割率を選択してください</h4>
    <div class="btn-group-vertical">
      <span class="label">分割率</span>
      <select data-bind="options: separates,
                       value: selectedSeparates,
                       valueAllowUnset: true"></select>
      <hr>
      <button data-bind="click:reset" class="btn btn-danger">Reset</button>
      <br>
      <button 
        data-bind="click:parabola" 
        id="parabola" 
        class="btn btn-warning"
      >
        Hide Parabola
      </button>
      <br>
      <button 
        data-bind="click:net" 
        id="net" 
        class="btn btn-warning"
      >
        Hide Net
      </button>
    </div>
    <br>
    <img src="{{site.url}}/images/parabolic_arc.png"
          width="450" height="450" 
    >
    <br> 
    点Q, R, P の座標は以下の式で求められる
    $$Q=(1-t)A + tB$$
    $$R=(1-t)B + tC$$
    $$P=(1-t)Q + tR$$     
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
  separates = [0.02,0.05,0.1];
  self.selectedSeparates = ko.observable(separates[2]);

  // variables
  var basePoints = []; // point A,B and C
  
  var Q =[]; // point between A and B
  var R =[]; // point between B and C
  var P =[]; // touching point

  var clickedTime = 0;
  var width = 450,
     height = 450;

  // constants     
  var radiusOfBasePoints = 10;
  var radiusOfSepPoints = 2;  
  var colorOfBasePoints = "gold";
  var colorOfSepPoints = "grey"; 
  var colorOfBaseLines = "#fff";
  var colorOfSepLines = "#ff0"; 
  var colorOfParabola = "lime"; 


  // svg 追加 
  var svg01 = d3.select("#svg01")
                .append("svg")
                .attr("height",height)
                .attr("width",width)
                .style("background","#111");

  // accessor function
  var lineFunction = d3.svg.line()
                .x(function(d,i) { return basePoints[i][0]; })
                .y(function(d,i) { return basePoints[i][1]; })
                .interpolate("linear");  
  var parabola = d3.svg.line()
                .x(function(d) { return d.x; })
                .y(function(d) { return d.y; })
                .interpolate("linear");  
 
  // click event listner作成
  svg01.on("click",function(){

    if (clickedTime > 2) {
      return;
    };
 
    svg01.selectAll(".baseLines").remove();
    svg01.selectAll(".parabola").remove();

    // get mouse position
    var mousePos = d3.mouse(this);

    // store clicked mouse position
    basePoints.push(mousePos);

    // draw circles   
    svg01.selectAll(".basePoints")
      .data(basePoints)
     .enter()
      .append("circle")
      .attr("cx",function(d,i){return basePoints[i][0];})
      .attr("cy",function(d,i){return basePoints[i][1];})
      .attr("r",function(){return radiusOfBasePoints})
      .attr("class","basePoints")
      .style("fill",function(d,i){return colorOfBasePoints;});

    clickedTime++;  

    if (clickedTime == 3) {
      draw();
    }
 
  }); 

 //　
 function draw(){
    // draw baseLines
    svg01.append("path")
       .attr("d", lineFunction(basePoints))
       .attr("stroke", function(){return colorOfBaseLines})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","baseLines");

    // separates points　Q　and R
    getQR();

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

    // draw parabola
    svg01.append("path")
       .attr("d", parabola(P))
       .attr("stroke", function(){return colorOfParabola})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","parabola")
       .attr("opacity",1);


    svg01.selectAll(".basePoints")
        .remove();

    svg01.selectAll(".basePoints")
      .data(basePoints)
     .enter()
      .append("circle")
      .attr("cx",function(d,i){return basePoints[i][0];})
      .attr("cy",function(d,i){return basePoints[i][1];})
      .attr("r",function(){return radiusOfBasePoints})
      .attr("class","basePoints")
      .attr("id", function(d,i){
        return i;
      })
      .style("fill",function(d,i){return colorOfBasePoints;})
      .call(drag);

 };
  //　
 function redraw(){
    // draw baseLines
    svg01.selectAll(".baseLines")
       .transition()
       .duration(0) 
       .attr("d", lineFunction(basePoints))
       .attr("stroke", function(){return colorOfBaseLines})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","baseLines");

    // separates points　Q　and R
    getQR();
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
       .attr("d", parabola(P))
       .attr("stroke", function(){return colorOfParabola})
       .attr("stroke-width", 3)
       .attr("fill", "none")
       .attr("class","parabola");
 };
 function getQR(){
    Q=[];
    R=[];
    P=[];
    var Ax,Ay,Bx,By,Cx,Cy,Qx,Qy,Rx,Ry,Px,Py;
    Ax = basePoints[0][0];
    Ay = basePoints[0][1];
    Bx = basePoints[1][0];
    By = basePoints[1][1];
    Cx = basePoints[2][0];
    Cy = basePoints[2][1];
    for (var t=0;t<=1;t=t+self.selectedSeparates()){
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

  this.reset = function(){

    svg01.selectAll(".basePoints")
        .remove();
    svg01.selectAll(".baseLines")
        .remove();
    svg01.selectAll(".net")
        .remove();
    svg01.selectAll(".parabola")
        .remove();

    clickedTime = 0;
    basePoints = []; 
    Q = [];
    R = [];
    P = [];
  };
  
  this.parabola = function(){
    if (P.length==0){return};

    var opacity = svg01.select(".parabola")
                            .attr("opacity");
                            
    if (opacity==0){
      $("#parabola").html("Hide Parabola")          
                    .removeClass("btn-success")
                    .addClass("btn-warning");          
    } else {
      $("#parabola").html("Show Parabola")
                    .removeClass("btn-warning")
                    .addClass("btn-success");          
    };                        
    svg01.select(".parabola")
            .attr("opacity",function(){
              return opacity==0?1:0;
            })
  }
  this.net = function(){
    if (P.length==0){return};

    var opacity = svg01.selectAll(".net")
                            .attr("opacity");
                            
    if (opacity==0){
      $("#net").html("Hide Net")          
                    .removeClass("btn-success")
                    .addClass("btn-warning");          
    } else {
      $("#net").html("Show Net")
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

  // クラスがdraggableのときdragを呼び出す 
//  svg01.selectAll(".basePoints").call(drag);

  // ドラッグ中の制御　画面をはみ出さない
  function dragmove(d){
    var bPoint = d3.select(this);
    var i = bPoint.attr("id");
    bPoint
      .attr("cx", 
        d.x = Math.max(radiusOfBasePoints, Math.min(width - radiusOfBasePoints, d3.event.x)))
      .attr("cy", 
        d.y = Math.max(radiusOfBasePoints, Math.min(height - radiusOfBasePoints, d3.event.y)));
 
    basePoints[i][0] = bPoint.attr("cx");
    basePoints[i][1] = bPoint.attr("cy"); 

    redraw(); 

  };              


};



// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>
