---
title: d3
layout: post
postTitle: Mouse Click 
categories: event
---

## マウスのクリックイベント

<div class="row">
  <div class="col-sm-6 col-md-6">
    <span class="label label-info">枠の中をマウスでクリックしてください</span>
    <div id="svg01"></div>
    <button id="reset01" class="btn btn-lg btn-info">Reset</button>
  </div>
  <div class="col-sm-6 col-md-6">
  </div>
</div>
{% highlight javascript %}
  // svg空間作成 
  var svg01 =  d3.select("#svg01")
                 .append("svg:svg")
                 .attr("height", 500)
                 .attr("width", 500)
                 .style("background","#111");
  // variables
  var circles = [];
  var colors = [];
  // color category
  var color20 = d3.scale.category20();

  // accessor function
  var lineFunction = d3.svg.line()
                .x(function(d,i) { return circles[i][0]; })
                .y(function(d,i) { return circles[i][1]; })
                .interpolate("linear");  

  // click event listner作成
  svg01.on("click",function(){
  

    svg01.selectAll("circle").remove();
    svg01.selectAll("path").remove();

    // get mouse position
    var mousePos = d3.mouse(this);

    // store clicked mouse position
    circles.push(mousePos);

    // store color
    colors.push(color20(Math.floor(Math.random()*20)));

    // draw a path 
    svg01.append("path")
       .attr("d", lineFunction(circles))
       .attr("stroke", "grey")
       .attr("stroke-width", 5)
       .attr("fill", "none");          

    // draw circles   
    svg01.selectAll("circle")
      .data(circles)
     .enter()
      .append("circle")
      .attr("cx",function(d,i){return circles[i][0];})
      .attr("cy",function(d,i){return circles[i][1];})
      .attr("r",15)
      .style("fill",function(d,i){return colors[i];});

  }); 

{% endhighlight %}    

<hr>
<div class="row">
  <div class="col-sm-6 col-md-6">
    <span class="label label-info">枠の中をマウスでクリックしてください</span>
    <div id="svg02"></div>
  </div>
  <div class="col-sm-6 col-md-6"></div>
</div>

{% highlight javascript %}
// svg空間作成 
var svg02 =  d3.select("#svg02")
               .append("svg:svg")
                .attr("height", 500)
                 .attr("width", 500)
                 .style("background","#111");
 
// click event listner作成
svg02.on("click",function(){
  
  var mousePos = d3.mouse(this);

  for (var l = 0; l < 4; l++) {

  var color = d3.rgb(Math.floor(Math.random()*128)+120,
                      Math.floor(Math.random()*128)+120,
                      Math.floor(Math.random()*128)+120
                     );

  var circle = svg02.append("circle")
                    .attr("cx",mousePos[0])
                    .attr("cy",mousePos[1])
                    .attr("r",5)
                    .attr("stroke",color)
                    .attr("stroke-width",5)
                    .style("fill","none");
  for (var i = 0; i < 11; i++) {
    circle.transition()
          .delay(300*l + 50 * i)
          .duration(50)
          .attr("r",10 + 10*i)
          .attr("opacity",1 - 0.1*i)
          .ease("circle")
          .transition()
          .delay(2000)
          .remove();
  };

  };


});
{% endhighlight %} 
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

<script>

  // svg空間作成 
  var svg01 =  d3.select("#svg01")
                 .append("svg:svg")
                 .attr("height", 500)
                 .attr("width", 500)
                 .style("background","#111");
  // variables
  var circles = [];
  var colors = [];
  // color category
  var color20 = d3.scale.category20();

  // accessor function
  var lineFunction = d3.svg.line()
                .x(function(d,i) { return circles[i][0]; })
                .y(function(d,i) { return circles[i][1]; })
                .interpolate("linear");  

  // click event listner作成
  svg01.on("click",function(){
  

    svg01.selectAll("circle").remove();
    svg01.selectAll("path").remove();

    // get mouse position
    var mousePos = d3.mouse(this);

    // store clicked mouse position
    circles.push(mousePos);

    // store color
    colors.push(color20(Math.floor(Math.random()*20)));

    // draw a path 
    svg01.append("path")
       .attr("d", lineFunction(circles))
       .attr("stroke", "grey")
       .attr("stroke-width", 5)
       .attr("fill", "none");          

    // draw circles   
    svg01.selectAll("circle")
      .data(circles)
     .enter()
      .append("circle")
      .attr("cx",function(d,i){return circles[i][0];})
      .attr("cy",function(d,i){return circles[i][1];})
      .attr("r",15)
      .style("fill",function(d,i){return colors[i];});

  }); 

  d3.select("#reset01").on("click", function(){
    svg01.selectAll("circle").remove();
    svg01.selectAll("path").remove();

    circles = [];
    colors = [];     
  });

/* 
  ****************************************** */
// svg空間作成 
var svg02 =  d3.select("#svg02")
               .append("svg:svg")
                .attr("height", 500)
                 .attr("width", 500)
                 .style("background","#111");
 
// click event listner作成
svg02.on("click",function(){
  
  var mousePos = d3.mouse(this);

  for (var l = 0; l < 4; l++) {

  var color = d3.rgb(Math.floor(Math.random()*128)+120,
                      Math.floor(Math.random()*128)+120,
                      Math.floor(Math.random()*128)+120
                     );

  var circle = svg02.append("circle")
                    .attr("cx",mousePos[0])
                    .attr("cy",mousePos[1])
                    .attr("r",5)
                    .attr("stroke",color)
                    .attr("stroke-width",5)
                    .style("fill","none");
  for (var i = 0; i < 11; i++) {
    circle.transition()
          .delay(300*l + 50 * i)
          .duration(50)
          .attr("r",10 + 10*i)
          .attr("opacity",1 - 0.1*i)
          .ease("circle")
          .transition()
          .delay(2000)
          .remove();
  };

  };


});

</script>