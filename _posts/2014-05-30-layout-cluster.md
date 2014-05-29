---
title: d3
layout: post
postTitle: Layout Cluster
categories: Layout
---

<div id="ins01"></div>

####fill属性
<select data-bind="options: fillAttr,
                   value: selectedFillAttr,
                   valueAllowUnset: true"></select>
<button class="btn btn-info" data-bind="click:draw">描　画</button>                   

{% highlight javascript %}
/**
  ApplicationViewModel (knockout)
**/
function AppViewModel() {
  var width = 960,
      height = 700;
  
  // クラスター　レイアウト生成
  var cluster = d3.layout.cluster()
      .size([height, width - 160]);
  
  // 連結線の生成
  var diagonal = d3.svg.diagonal()
      .projection(function(d) { return [d.y, d.x]; });
  
  // svg生成
  var svg = d3.select("#ins01").append("svg")
      .attr("width", width)
      .attr("height", height)
    .append("g")
      .attr("transform", "translate(50,0)");

  // fill 属性　選択項目
  fillAttr = ['green', 'orange', 'none'];
  selectedFillAttr = ko.observable('green');
  
  // 初期描画
  drawCluster();
  
  /*************************************
    click event 
  **************************************/
  // 
  self.draw = function() {
    svg.selectAll(".link").remove();
    svg.selectAll(".node").remove();
    svg.selectAll("g").remove();
    
    drawCluster();

  };
  //**　描画ルーチン ** //
  function drawCluster(){
    d3.json("{{site.url}}/assets/json/mise.json", function(error, root) {
      
      var nodes = cluster.nodes(root),
          links = cluster.links(nodes);

      // 連結線の描画      
      var link = svg.selectAll(".link")
          .data(links)
        .enter().append("path")
          .attr("stroke","white") // line color
          .attr("stroke-width", 3) // line width
         .style("fill",function(){return selectedFillAttr();}) // <<<<
          .attr("class", "link")
          .attr("d", diagonal);

      // ノードの描画
      var node = svg.selectAll(".node")
          .data(nodes)
        .enter().append("g")
          .attr("class", "node")
          .attr("transform", function(d) { return "translate(" + d.y + "," + d.x + ")"; })
      //　ノードに円を描画    
      node.append("circle")
          .attr("r", function(d){return (10 - d.depth * 2) < 4 ? 4 : (10 - d.depth * 2);})
          .attr("stroke","red")
          .attr("stroke-width", function(d){ return (3 - d.depth)===0 ? 1 : (3 - d.depth) })
          .style("fill","lime");
      // ノードにテキストを描画    
      node.append("text")
          .attr("dx", function(d) { return d.children ? -8 : 8; })
          .attr("dy", 3)
          .style("text-anchor", function(d) { return d.children ? "end" : "start"; })
          .text(function(d) { return d.name; })
          .style("fill","yellow");
  });

};

};

// Activates knockout.js
ko.applyBindings(new AppViewModel());
{% endhighlight %}
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>
<script>

/**
  ApplicationViewModel
**/
function AppViewModel() {
var width = 960,
    height = 700;

var cluster = d3.layout.cluster()
    .size([height, width - 160]);

var diagonal = d3.svg.diagonal()
    .projection(function(d) { return [d.y, d.x]; });

var svg = d3.select("#ins01").append("svg")
    .attr("width", width)
    .attr("height", height)
  .append("g")
    .attr("transform", "translate(50,0)");


  fillAttr = ['green', 'orange', 'none'];
  selectedFillAttr = ko.observable('green');

  drawCluster();
  
  /*************************************
    click event 
  **************************************/
  // 
  self.draw = function() {
    svg.selectAll(".link").remove();
    svg.selectAll(".node").remove();
    svg.selectAll("g").remove();
    
    drawCluster();

  };
  function drawCluster(){
    d3.json("{{site.url}}/assets/json/mise.json", function(error, root) {
      var nodes = cluster.nodes(root),
          links = cluster.links(nodes);

      var link = svg.selectAll(".link")
          .data(links)
        .enter().append("path")
          .attr("stroke","white")
          .attr("stroke-width", 3)
         .style("fill",function(){return selectedFillAttr();})
          .attr("class", "link")
          .attr("d", diagonal);

      var node = svg.selectAll(".node")
          .data(nodes)
        .enter().append("g")
          .attr("class", "node")
          .attr("transform", function(d) { return "translate(" + d.y + "," + d.x + ")"; })

      node.append("circle")
          .attr("r", function(d){return (10 - d.depth * 2) < 4 ? 4 : (10 - d.depth * 2);})
          .attr("stroke","red")
          .attr("stroke-width", function(d){ return (3 - d.depth)===0 ? 1 : (3 - d.depth) })
          .style("fill","lime");

      node.append("text")
          .attr("dx", function(d) { return d.children ? -8 : 8; })
          .attr("dy", 3)
          .style("text-anchor", function(d) { return d.children ? "end" : "start"; })
          .text(function(d) { return d.name; })
          .style("fill","yellow");
  });
  
};

};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>