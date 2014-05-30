---
title: d3
layout: post
postTitle: InterpolateRgb
categories: Interpolation
---

<div class="row">
  <div class="col-sm-4">
    <div id="ins01"></div>
  </div>
  <div class="col-sm-8">
    
    <div class="row">
      <div class="col-sm-4">
        <div class="label">開始色</div>
        <select data-bind="options: startColor,
                       value: selectedStartColor,
                       valueAllowUnset: true"></select>
      </div>
      <div class="col-sm-4">
        <div class="label">終了色</div>
        <select data-bind="options: endColor,
                       value: selectedEndColor,
                       valueAllowUnset: true"></select>
      </div>
    </div>
    <div class="row">
      <div class="col-sm-offset-3">
        <button class="btn btn-info" data-bind="click:draw">描  　画</button> 
      </div> 
    </div>
  </div>
</div>

### 色の補完を行います

domain[0..1],range[0..1]の間で補完色が生成されます

__赤から黄色のとき__

color(0.0) = #ff0000

color(0.1) = #ff1a00

color(0.2) = #ff3300

color(0.3) = #ff4d00

color(0.4) = #ff6600

color(0.5) = #ff8000

color(0.6) = #ff9900

color(0.7) = #ffb300

color(0.8) = #ffcc00

color(0.9) = #ffe500

color(1.0) = #ffff00

{% highlight javascript %}
var color = d3.interpolateRgb(selectedStartColor(),selectedEndColor());
var scale = d3.scale.linear()
                    .domain([0,10]); // 初期値　range([0,1])へ変換
svg01.selectAll("rect")
   .data(color)
   .enter()
   .append("rect")
   .attr("x",25)
   .attr("y", function(d,i){ return 30*i;})
   .attr("height",30)
   .attr("width",150)
   .style("fill", function(d,i){ return color(scale(i));});  
{% endhighlight %}
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>
<script>
/**
  ApplicationViewModel
**/
function AppViewModel() {
  var width = 200,
  height = 30 * 11;


  var svg01 = d3.select("#ins01").append("svg")
    .attr("width", width)
    .attr("height", height);

  startColor = ['red','green', 'blue', 'yellow','aqua','lightgray','white','black'];
  endColor   = ['red','green', 'blue', 'yellow','aqua','lightgray','white','black'];
  selectedStartColor = ko.observable('red');
  selectedEndColor   = ko.observable('yellow');

  drawColors();
  
  /*************************************
    click event 
  **************************************/
  // 
  self.draw = function() {
    svg01.selectAll("rect").remove();
    
    drawColors();

  };

  function drawColors(){

    var color = d3.interpolateRgb(selectedStartColor(),selectedEndColor());
    var scale = d3.scale.linear()
                        .domain([0,10]); // 初期値　range([0,1])へ変換
    svg01.selectAll("rect")
       .data(color)
       .enter()
       .append("rect")
       .attr("x",25)
       .attr("y", function(d,i){ return 30*i;})
       .attr("height",30)
       .attr("width",150)
       .style("fill", function(d,i){ return color(scale(i));});  


  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>