---
title: d3
layout: post
postTitle: Draw Vector Arrow
categories: SVG Shapes
---

<div class="row">
  <div class="col-sm-6">
    <div id="svg01"></div>
  </div>
  <div class="col-sm-6">
    <h4>始点、角度、長さ、色を設定してください</h4>
    <div class="btn-group-vertical">
      <span class="label">始点　ｘ　座標</span>
      <input type="number" class="form-control text-right" data-bind="value:xPos">

      <span class="label">始点　ｙ　座標</span>
      <input type="number" class="form-control text-right" data-bind="value:yPos">

      <span class="label">角度</span>
      <input type="number" class="form-control text-right" data-bind="value:angle">

      <span class="label">長さ</span>
      <input type="number" class="form-control text-right" data-bind="value:length">

      <span class="label">色</span>
      <input type="text" class="form-control text-left" data-bind="value:color">
      <br>
      <button data-bind="click:run" class="btn btn-success">Draw</button>
      <br>
      <button data-bind="click:reset" class="btn btn-warning">Reset</button>
    </div>      
  </div>
</div>

<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>

<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  // ko variables
  this.xPos = ko.observable(0);
  this.yPos = ko.observable(0);
  this.angle = ko.observable(0);
  this.length = ko.observable(100);
  this.color = ko.observable("#f00");

  // Point Object
  function Point(x, y){
    this.x = x;
    this.y = y;
    return this;
  };

  var pi = Math.PI;
  var aDegree = pi/180;

  var endPoint = new Point(0,0);

  var width = 500,
     height = 500;


  var xScale = d3.scale.linear()
                       .domain([-200,200])
                       .range([50,450]);
  
  var yScale = d3.scale.linear()
                       .domain([200,-200])
                       .range([50,450]);                       

  var x0 = y0 = 0;
  /* ベクトル線　描画関数　*/
  function drawVector(svg,x0,y0,angles,length,color,name){

    var vectorData = [];
    var radians = angles * aDegree;
    var radians1 = pi + radians + pi/6;
    var radians2 = pi + radians - pi/6;

    // 終点の座標
    endPoint.x = Math.floor(Math.cos(radians)*length)+x0;
    endPoint.y = Math.floor(Math.sin(radians)*length)+y0;

    vectorData.push(new Point(x0,y0));
    vectorData.push(new Point(endPoint.x,endPoint.y));
    vectorData.push(new Point(
      endPoint.x+Math.floor(Math.cos(radians1)*10),
      endPoint.y+Math.floor(Math.sin(radians1)*10)
      ));
    vectorData.push(new Point(endPoint.x,endPoint.y));
    vectorData.push(new Point(
      endPoint.x+Math.floor(Math.cos(radians2)*10),
      endPoint.y+Math.floor(Math.sin(radians2)*10)
      ));

    var vectorArrow = d3.svg.line()
        .x(function(d) { return xScale(d.x); })
        .y(function(d) { return yScale(d.y); })
        .interpolate("linear");

    svg.append("path")
          .attr("d", vectorArrow(vectorData))
          .attr("stroke", function(){return color})
          .attr("class","vector")
          .attr("stroke-width", 2)
          .attr("fill", "none");   
 
    /* texts */  
 //   var delta = Math.abs(angles) <= 90 ? 10 : -10;  
    var delta = 0;
 
    svg.append("text")
      .attr("class","text")
      .attr("x",function(){
        return xScale(Math.floor(Math.cos(radians)*length/2)+x0)
      })
      .attr("y",function(d){
        return yScale(Math.floor(Math.sin(radians)*length/2)+y0)
      })
      .text(name)
      .attr("stroke","#fff")
      .attr("font-size","16px")
      .style("fill","white");   

   svg.append("text")
      .attr("class","text")
      .attr("x",function(){
        return xScale(Math.floor(Math.cos(radians)*length/2)+x0-3)
      })
      .attr("y",function(d){
        return yScale(Math.floor(Math.sin(radians)*length/2)+y0+10)
      })
      .text("→")
      .attr("stroke","#fff")
      .attr("font-size","16px")
      .style("fill","white");   


  };


/**
  2-dimentional vectors
*/

  var svg01 = d3.select("#svg01")
                .append("svg")
                .attr("height",height)
                .attr("width",width);
 

  /* draw grids */
  var gridsData = [-200,-150,-100,-50,50,100,150,200]; 

  svg01.selectAll(".xgrids")
        .data(gridsData)
        .enter()
        .append("line")
        .attr("x1",function(d){return xScale(d);})
        .attr("y1",function(d){return yScale(200);})
        .attr("x2",function(d){return xScale(d);})
        .attr("y2",function(d){return yScale(-200);})
        .attr("class","xgrids")
        .attr("stroke", function(){return "#666"})
        .attr("stroke-width", 1)
        .attr("opacity",10)
        .attr("fill", "none");   

  svg01.selectAll(".ygrids")
        .data(gridsData)
        .enter()
        .append("line")
        .attr("x1",function(d){return xScale(200);})
        .attr("y1",function(d){return yScale(d);})
        .attr("x2",function(d){return xScale(-200);})
        .attr("y2",function(d){return yScale(d);})
        .attr("class","ygrids")
        .attr("stroke", function(){return "#666"})
        .attr("stroke-width", 1)
        .attr("opacity",10)
        .attr("fill", "none");   
        
  //軸生成
  var xAxis = d3.svg.axis()
                   .scale(xScale)
                   .tickValues([-200,-100,100,200]);
  var yAxis = d3.svg.axis()
                   .scale(yScale)
                   .orient(["left"])
                  .tickValues([-200,-100,0,100,200]);
                  
  typeof(xAxis);
  typeof(yAxis);

  //　軸グループ生成し軸関数を呼ぶ
  var xAxisGroup = svg01.append("g")
          .attr("transform","translate(0,"+ yScale(0)+")")
          .attr("stroke","white")
          .attr("stroke-width","1")
          .style("fill","none")
          .call(xAxis);

  var yAxisGroup = svg01.append("g")
            .attr("transform","translate(" + xScale(0) + ",0)")
            .attr("stroke","white")
            .attr("stroke-width","1")
            .style("fill","none")
            .call(yAxis);


  this.run = function(){
    drawVector(svg01,
              parseInt(this.xPos()),
              parseInt(this.yPos()),
              parseInt(this.angle()),
              parseInt(this.length()),
              this.color(),
              "v");
/*
    drawVector(svg01,
      endPoint.x,
      endPoint.y,
      Math.floor(Math.random()*180),
      Math.floor(Math.random()* 100) ,
      "aqua",
      "b");

    drawVector(svg01,
      endPoint.x,
      endPoint.y,
      Math.floor(Math.random()*180),
      Math.floor(Math.random()* 100) ,
      "gold",
      "c");
*/

  };

  this.reset = function(){

    svg01.selectAll(".vector")
        .remove();
    svg01.selectAll(".text")
        .remove();

  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>
