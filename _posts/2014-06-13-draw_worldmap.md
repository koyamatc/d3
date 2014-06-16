---
title: d3
layout: post
postTitle: 世界地図の描画 
categories: Geo maps
---
<div class="row">
  <div class="col-sm-9">
    <div id="svg"></div>
  </div>
  <div class="col-sm-3">
    <span class="label label-info">Clip Angle</span>
    <select data-bind="options: clipAngle,
                      value: selectedClipAngle,
                      valueAllowUnset: true">
    </select>

  </div>
</div>
<button id="run" class="btn btn-info">move</button>

Natural Earth　の地図データを使い、D3.jsで世界地図を描いてみました。

地図の上でマウスを動かすと、回転します。



<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script
<script src="http://d3js.org/queue.v1.min.js"></script>
<script src="http://d3js.org/topojson.v0.min.js"></script>
<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  // knockout select 
  clipAngle = [90,180];
  selectedClipAngle = ko.observable(90);

  var width = 900,
     height = 700,
       sens = 0.25;

  var color = d3.scale.category20c();  

  var svg = d3.select("#svg").append("svg")
        .attr("width", width)
        .attr("height", height);

   var g = svg.append("g");


    var projection;
    var path;

  c_clipAngle = ko.computed(function() {

    projection = d3.geo.orthographic()
              .scale(250) 
              .translate([width / 2, height / 2])
              .clipAngle(selectedClipAngle());
    path = d3.geo.path()
                   .projection(projection); 

    svg.selectAll("path").remove();

    g.append("path")
      .datum({type: "Sphere"})
      .attr("class", "sphere")
      .attr("d", path)
      .style("fill","navy");

    // 経緯度線の描画  
/**    var graticule = d3.geo.graticule();

    g.append("path")
              .datum(graticule)
              .attr("class", "graticule")
              .attr("d", path)
              .attr("stroke","red")
              .attr("stroke-width","1px");
*/
  d3.json("{{site.url}}/assets/json/world.geojson", function(error, world) {
    
      g.selectAll("path")
          .data(world.features)
        .enter().append("path")
          .attr("d", path)
          .attr("class","country")
          .attr("id", function(d,i){return "country" +i /*d.properties.name*/;})
          .style("fill",function(d,i){
            return (selectedClipAngle()==90) ? "#ddd":color(i%20);})
          .call(drag)
          .on("mouseover",function(d,i){mouseOver(i)})
          .on("mouseout",function(d,i){mouseOut(i)});
           
  });

    g.selectAll("path").attr("d", path);
  }, this);
    

  var drag = d3.behavior.drag()
        .origin(function() { 
           var r = projection.rotate();
           return {x: r[0] / sens, y: -r[1] / sens}; })
        .on("drag", function() {
            var rotate = projection.rotate();
            projection.rotate([d3.event.x * sens, -d3.event.y * sens, rotate[2]]);
            svg.selectAll("path.country").attr("d", path);
          }); 

  function transition() {
      d3.transition()
      .duration(2500)
      .tween("rotate", function() {
        var r = d3.interpolate(projection.rotate(), [-p[0], -p[1]]);
        return function(t) {
          projection.rotate(r(t));
          g.selectAll("path").attr("d", path)
        };
      })
  };  

  function mouseOver(id){
    var el = "#country" + id;
    d3.select(el).style("fill","red");
  }
  function mouseOut(id){
    var el = "#country" + id;
    d3.select(el).style("fill",
      function(){return (selectedClipAngle()==90) ? "#ddd":color(id%20);});
  }
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());


</script>
