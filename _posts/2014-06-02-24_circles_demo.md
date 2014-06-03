---
title: d3
layout: post
postTitle: 24 circles  
categories: Transition demos
---

<div id="svg03"></div>
<span id="run03" class="btn btn-info">RUN</span>
<span id="reset03" class="btn btn-info">reset</span>

*きれいなコードではありませんが*

{% highlight javascript %}
// 24個の円のデータを用意
var circles03 = [ 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,
                 13,14,15,16,17,18,19,20,21,22,23,24];
// svgコンテナを追加
var svg03 =  d3.select("#svg03")
               .append("svg")
               .attr("width", 500)
               .attr("height", 500);
// 24個の円を原点に追加
svg03.selectAll("circle")
     .data(circles03)
     .enter()
     .append("circle")
     .attr("transform","translate(250,250)")
     .attr("cx", 0)
     .attr("cy", 0)
     .attr("r", 15)
     .style("fill", function(d){ return "white"})
     .attr("id", function(d,i) { return "circle" + i});

// RUNボタンがクリックされた時の処理
d3.select("#run03").on("click", function(){

  var inc = Math.PI * 2 / circles03.length;
  // 円の開閉の処理
  spread0(0,200,"white","red");
  spread1(3000,180,"yellow","lime");
  spread0(6000,150,"red","#0ff");
  spread1(9000,220,"#3399ff","yellow");
  spread(12000,200,"lime","yellow");
  rotation0(15000,24,200);

  function spread(delay,r,color0,color1){
    for (var i = 0; i < circles03.length; i++) {
      var el = d3.select("#svg03 #circle" + i);
      el.transition()
        .delay(delay)
        .duration(2000)
        .attr("cx", function(){
           return Math.sin(i * inc) * r; 
        })
        .attr("cy", function(){
           return Math.cos(i * inc) * r;
        })
        .style("fill", function(){ 
           return (i % 2 == 1) ? color0:color1})
        .ease("bounce");
    }; 
  };

  function spread0(delay,r,color0,color1){
    for (var i = 0; i < circles03.length; i++) {
      var el = d3.select("#svg03 #circle" + i);
      el.transition()
        .delay(delay)
        .duration(2000)
        .attr("cx", function(){
           return (i % 2 == 1) ? 0 : Math.sin(i * inc) * r; 
        })
        .attr("cy", function(){
           return (i % 2 == 1) ? 0 : Math.cos(i * inc) * r;
        })
        .style("fill", function(){ 
           return (i % 2 == 1) ? color0:color1})
        .ease("bounce");
    }; 
  };
  function spread1(delay,r,color0,color1){
    for (var i = 0; i < circles03.length; i++) {
      var el = d3.select("#svg03 #circle" + i);
      el.transition()
        .delay(delay)
        .duration(2000)
        .attr("cx", function(){
           return (i % 2 == 0) ? 0 : Math.sin(i * inc) * r; 
        })
        .attr("cy", function(){
           return (i % 2 == 0) ? 0 : Math.cos(i * inc) * r;
        })
        .style("fill", function(){ 
           return (i % 2 == 1) ? color0:color1})
      .ease("bounce");
    }; 
  };
  // 円のローテーション
  function rotation0(delay,c, r){
    for (var l = 0; l < c; l++ ) {
      for (var i = 0; i < circles03.length; i++) {
        var el = d3.select("#svg03 #circle" + i);
        el.transition()
          .delay(delay + l * 500)
          .duration(500)
          .attr("cx", function(){
             return Math.sin(i * inc * l) * r; 
          })
          .attr("cy", function(){
             return Math.cos(i * inc * l) * r;
          })
          .ease("linear");
      }; 
    
    };
  };

});

d3.select("#reset03").on("click", function(){
  d3.selectAll("#svg03 circle")
     .transition()
     .duration(1000)
     .attr("cx", 0)
     .attr("cy", 0)
     .style("fill","white");
});
{% endhighlight %}
<script src="http://d3js.org/d3.v3.min.js"></script>
<script type="text/javascript">

var circles03 = [ 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,
                 13,14,15,16,17,18,19,20,21,22,23,24];

var svg03 =  d3.select("#svg03")
               .append("svg")
               .attr("width", 500)
               .attr("height", 500);

svg03.selectAll("circle")
     .data(circles03)
     .enter()
     .append("circle")
     .attr("transform","translate(250,250)")
     .attr("cx", 0)
     .attr("cy", 0)
     .attr("r", 15)
     .style("fill", function(d){ return "white"})
     .attr("id", function(d,i) { return "circle" + i});

d3.select("#run03").on("click", function(){

  var inc = Math.PI * 2 / circles03.length;

  spread0(0,200,"white","red");
  spread1(3000,180,"yellow","lime");
  spread0(6000,150,"red","#0ff");
  spread1(9000,220,"#3399ff","yellow");
  spread(12000,200,"lime","yellow");
  rotation0(15000,24,200);

  function spread(delay,r,color0,color1){
    for (var i = 0; i < circles03.length; i++) {
      var el = d3.select("#svg03 #circle" + i);
      el.transition()
        .delay(delay)
        .duration(2000)
        .attr("cx", function(){
           return Math.sin(i * inc) * r; 
        })
        .attr("cy", function(){
           return Math.cos(i * inc) * r;
        })
        .style("fill", function(){ 
           return (i % 2 == 1) ? color0:color1})
        .ease("bounce");
    }; 
  };

  function spread0(delay,r,color0,color1){
    for (var i = 0; i < circles03.length; i++) {
      var el = d3.select("#svg03 #circle" + i);
      el.transition()
        .delay(delay)
        .duration(2000)
        .attr("cx", function(){
           return (i % 2 == 1) ? 0 : Math.sin(i * inc) * r; 
        })
        .attr("cy", function(){
           return (i % 2 == 1) ? 0 : Math.cos(i * inc) * r;
        })
        .style("fill", function(){ 
           return (i % 2 == 1) ? color0:color1})
        .ease("bounce");
    }; 
  };
  function spread1(delay,r,color0,color1){
    for (var i = 0; i < circles03.length; i++) {
      var el = d3.select("#svg03 #circle" + i);
      el.transition()
        .delay(delay)
        .duration(2000)
        .attr("cx", function(){
           return (i % 2 == 0) ? 0 : Math.sin(i * inc) * r; 
        })
        .attr("cy", function(){
           return (i % 2 == 0) ? 0 : Math.cos(i * inc) * r;
        })
        .style("fill", function(){ 
           return (i % 2 == 1) ? color0:color1})
      .ease("bounce");
    }; 
  };
  function rotation0(delay,c, r){
    for (var l = 0; l < c; l++ ) {
      for (var i = 0; i < circles03.length; i++) {
        var el = d3.select("#svg03 #circle" + i);
        el.transition()
          .delay(delay + l * 500)
          .duration(500)
          .attr("cx", function(){
             return Math.sin(i * inc * l) * r; 
          })
          .attr("cy", function(){
             return Math.cos(i * inc * l) * r;
          })
          .ease("linear");
      }; 
    
    };
  };

});

d3.select("#reset03").on("click", function(){
  d3.selectAll("#svg03 circle")
     .transition()
     .duration(1000)
     .attr("cx", 0)
     .attr("cy", 0)
     .style("fill","white");
});

</script>
