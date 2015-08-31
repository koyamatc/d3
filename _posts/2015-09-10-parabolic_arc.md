---
title: d3
layout: post
postTitle: Parabolic Arc
categories: SVG Shapes
---

<div class="row">
  <div class="col-sm-6">
    <h3 class="text-white">画面をクリックして、点を３個指定してください</h3>
    <div id="svg01"></div>
  </div>
  <div class="col-sm-6">
    <h4>線分の分割数を選択してください</h4>
    <div class="btn-group-vertical">
      <span class="label">分割数</span>
      <select data-bind="options: separates,
                       value: selectedSeparates,
                       valueAllowUnset: true"></select>
    </div>      
      <hr>
      <br>
      <button data-bind="click:reset" class="btn btn-warning">Reset</button>
  </div>
</div>

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
  separates = [1,7,15];
  self.selectedSeparates = ko.observable(separates[0]);

  // variables
  var basePoints = [];
  
  var sepPoints0 =[];
  var sepPoints1 =[];

  var clickedTime = 0;
  var width = 500,
     height = 500;

   var radiusOfBasePoints = 10;
   var radiusOfSepPoints = 5;  
   var colorOfBasePoints = "gold";
   var colorOfSepPoints = "grey"; 
   var colorOfBaseLines = "#fff"; 


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
 
  // click event listner作成
  svg01.on("click",function(){

    if (clickedTime > 2) {
      return;
    }
 
    svg01.selectAll(".basePoints").remove();
    svg01.selectAll(".sepPoints").remove();
    svg01.selectAll(".baseLines").remove();

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

    // separates points
    setSeparatePoints();
        // draw circles   
    svg01.selectAll(".sepPoints0")
      .data(sepPoints0)
     .enter()
      .append("circle")
      .attr("cx",function(d,i){return sepPoints0.x;})
      .attr("cy",function(d,i){return sepPoints0.y;})
      .attr("r",function(){return radiusOfSepPoints})
      .attr("class","sepPoints")
      .style("fill",function(d,i){return colorOfSepPoints;});

 };
 function setSeparatePoints(){
    var incX,incY = 0;
    incX = (basePoints[0][0] + basePoints[1][0]) / self.selectedSeparates();
    incY = (basePoints[0][1] + basePoints[1][1]) / self.selectedSeparates();
    for (var i=0;i<self.selectedSeparates();i++){
      sepPoints0.push(new Point(basePoints[0][0]+incX*(i+1),basePoints[0][1]+incY*(i+1)))
    }
    console.log(sepPoints0);
 }

  this.reset = function(){

    svg01.selectAll(".basePoints")
        .remove();
    svg01.selectAll(".baseLines")
        .remove();
    svg01.selectAll(".sepPoints")
        .remove();

    clickedTime = 0;
    basePoints = []; 
    sepPoints = [];    
  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>
