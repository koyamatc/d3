---
title: d3
layout: post
postTitle: Spiral and Circles
categories: Transition demos
---
<div class="row">
  <div class="col-sm-8">
    <div id="svg01"></div>
  </div>
  <div class="col-sm-4">
    <h4>始点、角度、長さ、色を設定してください</h4>
    <div class="btn-group-vertical">
      <span class="label">ｘ の係数</span>
      <input type="number" class="form-control text-right" data-bind="value:xB">

      <span class="label">ｙ　の係数</span>
      <input type="number" class="form-control text-right" data-bind="value:yB">

      <span class="label">角度</span>
      <input type="number" class="form-control text-right" data-bind="value:angle">

      <br>
      <button data-bind="click:run" class="btn btn-success">Draw</button>
      <br>
      <button data-bind="click:reset" class="btn btn-warning">Reset</button>
      <br>
      <button data-bind="click:hide" class="btn btn-success">
        Hide spiral</button>
      <br>
      <button data-bind="click:show" class="btn btn-warning">Show spiral</button>
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
  this.xB = ko.observable(1.12);
  this.yB = ko.observable(1.12);
  this.angle = ko.observable(24);

  // Point Object
  function Point(x, y){
    this.x = x;
    this.y = y;
    return this;
  };

  var width = 700,
     height = 700;

  var pi = Math.PI;
  var aDegree = pi/180; 

  var color = d3.scale.category20();  

  var xScale01 = d3.scale.linear()
                       .domain([-350,350])
                       .range([0,700]);
  var yScale01 = d3.scale.linear()
                       .domain([350,-350])
                       .range([0,700]);
  
  var svg01 = d3.select("#svg01")
      .append("svg")
      .attr("width", width)
      .attr("height", height)
      .style("background","#000");
  

  var line = d3.svg.line()
      .x(function(d) { return xScale01(d.x); })
      .y(function(d) { return yScale01(d.y); })
      .interpolate("linear");

  // データ
 　var pathData01 = [];
 　var circles = [];
 　var rx,ry;
 　var Bx,By;

 　for (var i = 0; i < 100; i++) {
  　circles.push(i);
 　};

  
  //** 初期描画 *//
  draw(this.xB(),this.yB());

  // 再描画
  ko.computed(function() {
      draw(this.xB(),this.yB()); 
  }, this);

  this.run = function() {
    //redraw(this.degrees(),this.selectedLineWidth());    
  }; 
  this.reset = function() {
    draw();    
  }; 
  this.hide = function() {
    //redraw(this.degrees(),this.selectedLineWidth());    
  }; 
  this.show = function() {
    //redraw(this.degrees(),this.selectedLineWidth());    
  }; 

  function draw(Bx,By){
    svg01.selectAll(".spiral").remove();
    svg01.selectAll("circle").remove();
    
    for (i=0;i<=24*pi;i=i+0.1){
  
      rx = Math.pow(Bx,i);
      ry = Math.pow(By,i);
      pathData01.push(new Point(rx*Math.cos(i),ry*Math.sin(i)));

    }

  
    svg01.append("path")
      .attr("stroke", "yellow")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("class","spiral")
      .attr("d", line(pathData01));

    for (var i=0;i<circles.length;i++){
      svg01.append("circle")
        .attr("cx",function(){ return xScale01(0); } )
        .attr("cy",function(){ return yScale01(0); } )
        .attr("r",1)
        .attr("id",function(){ return "c"+i; })
       .style("fill", function(){
          return color(Math.floor(Math.random()*20));
        })

    }     

  };

  // 再描画
  function redraw(l,w){

  };

};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>