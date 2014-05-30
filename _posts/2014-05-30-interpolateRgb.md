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
  <div class="col-sm-4">
     ####開始色
    <select data-bind="options: startColor,
                   value: selectedStartColor,
                   valueAllowUnset: true"></select>
  </div>
  <div class="col-sm-4">
    <select data-bind="options: endColor,
                   value: selectedEndColor,
                   valueAllowUnset: true"></select>
    <button class="btn btn-info" data-bind="click:draw">描　画</button> 
  </div>
</div>

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

  startColor = ['red','green', 'blue', 'yellow'];
  endColor   = ['red','green', 'blue', 'yellow'];
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
    svg01.selectAll("rect")
       .data(color)
       .enter()
       .append("rect")
       .attr("x",25)
       .attr("y", function(d,i){ return 30*i;})
       .attr("height",30)
       .attr("width",150)
       .style("fill", function(d,i){ return color(0.1* i);});  

  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>