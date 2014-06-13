---
title: d3
layout: post
postTitle: 世界地図の描画 
categories: Geo maps
---

<div id="svg"></div>
<button id="run" class="btn btn-info">move</button>

Natural Earth　の地図データを使い、D3.jsで世界地図を描いてみました。

地図の上でマウスを動かすと、回転します。



<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="http://d3js.org/topojson.v0.min.js"></script>
<script src="http://koyamatch.com/js/underscore.js"></script>
<script type="text/javascript">
  var width = 900,
     height = 700;

  var color = d3.scale.category20c();  

  var svg = d3.select("#svg").append("svg")
                             .attr("width", width)
                             .attr("height", height);

   var g = svg.append("g");
                             
    var projection = d3.geo.orthographic()
                .scale(250) 
              .translate([width / 2, height / 2])
              .clipAngle(90);

    var path = d3.geo.path()
                   .projection(projection);

    var λ = d3.scale.linear()
        .domain([0, width])
        .range([-180, 180]);

    var φ = d3.scale.linear()
        .domain([0, height])
        .range([90, -90]);

  d3.json("{{site.url}}/assets/json/world.geojson", function(error, world) {
    
      g.selectAll("path")
          .data(world.features)
        .enter().append("svg:path")
          .attr("d", path)
          .attr("class",function(d){
            return d.properties.name;
          })
          .style("fill",function(d,i){
            return "#ddd";//color(i % 20);
          });
  
  });
  svg.on("mousemove", function() {
    var p = d3.mouse(this);
    projection.rotate([λ(p[0]), φ(p[1])]);
    svg.selectAll("path").attr("d", path);
  });
 
d3.select("#run").on("click",function(){
  projection.rotate([λ(135), φ(34)]);
    svg.selectAll("path").attr("d", path);
});  


</script>
