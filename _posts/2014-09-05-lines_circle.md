---
title: d3
layout: post
postTitle: Lines and Circle
categories: Transition demos
---
<div class="row">
  <div class="col-sm-6">
    <div id="svg01"></div>
  </div>
</div>
<div class="row">
  <div class="col-sm-1">
    <span class="label">角度</span>
    <input type="number" class="form-control text-right" data-bind="value:degrees">
  </div>
  <div class="col-sm-1">
    <span class="label">線幅</span>
   <select data-bind="options: lineWidths,
                      value: selectedLineWidth,
                      valueAllowUnset: true"></select>
  </div>
  <div class="col-sm-4">
    <span class="label">角度、線幅を変更しても描画されないときにクリックしてください</span>
    <button data-bind="click:run" class="btn btn-info">Run</button>
  </div>

</div>

<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>

<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  this.degrees = ko.observable(1);
  lineWidths = [0.1,0.3,0.5,0.8,1,1.5,2];
  this.selectedLineWidth = ko.observable(2);

  // Point Object
  function Point(x, y){
    this.x0 = x;
    this.y0 = y;
    return this;
  };

  var width = 500,
     height = 500;

  var pi = Math.PI;
  var aDegree = pi/180;   

  var scale = d3.scale.linear()
                       .domain([-1.1,1.1])
                       .range([0,500]);
  
  var svg01 = d3.select("#svg01").append("svg")
        .attr("width", width)
        .attr("height", height);
  var group = svg01.append("g");      


  var line = d3.svg.line()
      .x(function(d) { return scale(d.x0); })
      .y(function(d) { return scale(d.y0); })
      .interpolate("linear");

  // データ
  var circleData = [];

  //** 初期描画 *//
  var step = aDegree * 1;
  for (var i = 0; i <= 360; i++) {
    circleData.push(new Point(Math.cos(step*i),Math.sin(step*i)));
  };

    group.selectAll("path").remove();

 
   var shape = group
      .selectAll(".shape")
      .data(circleData)
      .enter()
      .append("path")
      .attr("stroke", "yellow")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("class","shape")
      .attr("d", line(circleData));

  // 再描画
  ko.computed(function() {
    redraw(this.degrees(),this.selectedLineWidth());    
  }, this);

  this.run = function() {
    redraw(this.degrees(),this.selectedLineWidth());    
  }; 

  // 再描画
  function redraw(l,w){

      circleData = [];
      step = aDegree * l;

      for (var m = 0; m <= 360; m++) {
        circleData.push(new Point(Math.cos(step*m),Math.sin(step*m)))
        ;
      };

    group.selectAll(".shape")
     .data(circleData)
     .transition()
     .duration(200)
     .attr("opacity",0)
     .transition()
     .duration(0)
     .ease("linear")
     .attr("stroke", "gold")
      .attr("stroke-width",w)
      .attr("fill", "none")
      .attr("class","shape")
      .attr("opacity",100)
      .attr("d", line(circleData));
  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>