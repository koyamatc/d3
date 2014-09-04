---
title: d3
layout: post
postTitle: Circle Transformes
categories: Transition demos
---

<div id="svg01"></div>

<button id="start" class="btn btn-info">Start</button>

<script src="http://d3js.org/d3.v3.min.js"></script>

<script type="text/javascript">
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
  d3.select("#start").on("click",  function(){

    var delay = 300;
    var ii = 0;

    for (var l = 1; l < 60; l++) {

      redraw(l);
    };


  });

  function redraw(l){
      circleData = [];
      step = aDegree * l;

      for (var m = 0; m <= 360; m++) {
        circleData.push(new Point(Math.cos(step*m),Math.sin(step*m)));
      };

    group.selectAll(".shape")
     .data(circleData)
     .transition()
     .duration(1000)
     .attr("opacity",0)
     .transition()
     .duration(0)
     .ease("linear")
     .attr("stroke", "white")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("class","shape")
      .attr("opacity",100)
      .attr("d", line(circleData));

    };

</script>
