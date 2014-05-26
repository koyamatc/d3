---
title: d3
layout: post
postTitle: Ordinal Scale
categories: Scale
---

### range

. domainに指定した序数値を -> range に設定した値に対応させる

 .domain(["白", "赤", "黄色", "青"])  -> .range(["#fff", "#f00", "#ff0", "#00f"]);

 "白" -> "#fff" , "赤" -> "#f00"


<div id="svg"></div>

{% highlight javascript %}
// 色を定義
var colors = d3.scale.ordinal()
               .domain(["白", "赤", "黄色", "青"])
               .range(["#fff", "#f00", "#ff0", "#00f"]);

svg.selectAll(".colors")
   .data(colors.domain()) // domainを読み込む
   .enter()
   .append("rect")
   .attr("class","colors")
   .attr("x", function(d,i){ return 50;}) 
   .attr("y", function(d,i){ return i* 40 + 10;})
   .attr("width", 100)
   .attr("height", 30)
   .style("fill", function(d,i){ return colors(d);});　// domainの色名でrangeの色コードを読む

{% endhighlight%}

### rangePoints

__.rangePoints(interval [, padding])__

<div id="svg01"></div>

{% highlight javascript %}
d3.scale.ordinal()
        .domain([1, 2, 3, 4])
        .rangePoints([0, 300], 0) // return [0, 100, 200, 300] 
        .range();

        .rangePoints([0, 300], 1) // return [37.5, 112.5, 187.5, 262.5]  
        .rangePoints([0, 300], 2) // return [60, 120, 180, 240]  
        .rangePoints([0, 300], 3) // return [75, 125, 175, 225]  
{% endhighlight %}

{% highlight javascript %}

//n: 点の数、 step: 点の間隔 
interval = step * padding / 2 + step * (n - 1) + step * padding /　2
         = step * padding + step * (n - 1)
         = step * (padding + n - 1)

step = interval / (padding + n - 1)

//interval = 300, padding = 2 のとき
300 / (2 + 4 -1) = 60         

{% endhighlight %}

###rangeBands
__.rangeBands(interval[, padding[, outerPadding]])__
<div id="svg02"></div>

{% highlight javascript %}

var o = d3.scale.ordinal()
			  	.domain([1, 2, 3, 4])
				  .rangeBands([0, 600],1,1)
				  .range();

svg02.selectAll(".bands")
     .data(o)
     .enter()
     .append("rect")
     .attr("class","bands")
     .attr("x", function(d){return d})
     .attr("y", 50)
     .attr("width",60)
     .attr("height",30)
     .style("fill","orange");

{% endhighlight %}

<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script>

var width = 200;
var height = 180;

var bgColor = "black"; // 背景色

/** svg空間作成 */
var svg =  d3.select("#svg")
                      .append("svg")
                      .attr("width", width)
                      .attr("height", height)
                      .style("background",bgColor);


var colors = d3.scale.ordinal()
               .domain(["白", "赤",  "黄色", "青"])
               .range(["#fff",   "#f00", "#ff0",  "#00f"]);
 

svg.selectAll(".colors")
   .data(colors.domain())
   .enter()
   .append("rect")
   .attr("class","colors")
   .attr("x", function(d,i){ return 50;}) 
   .attr("y", function(d,i){ return i* 40 + 10;})
   .attr("width", 100)
   .attr("height", 30)
   .style("fill", function(d,i){ return colors(d);});

//* rangePoints */
var points0 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 0)
                     .range();

var points1 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 1)
                     .range();

var points2 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 2)
                     .range();

var points3 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 3)
                     .range();


/** svg空間作成 */
var svg01 =  d3.select("#svg01")
                      .append("svg")
                      .attr("width", 300)
                      .attr("height", 300)
                      .style("background",bgColor);

svg01.selectAll("circle")
   .data(points0)
   .enter()
   .append("circle")
   .attr("cx", function(d,i){ return d;}) 
   .attr("cy", function(d,i){ return 50;})
   .attr("r", 10)
   .style("fill", "lime");

svg01.selectAll(".p1")
   .data(points1)
   .enter()
   .append("circle")
   .attr("class","p1")
   .attr("cx", function(d,i){ return d;}) 
   .attr("cy", function(d,i){ return 100;})
   .attr("r", 10)
   .style("fill", "gold");

svg01.selectAll(".p2")
   .data(points2)
   .enter()
   .append("circle")
   .attr("class","p2")
   .attr("cx", function(d,i){ return d;}) 
   .attr("cy", function(d,i){ return 150;})
   .attr("r", 10)
   .style("fill", "red");

svg01.selectAll(".p3")
   .data(points3)
   .enter()
   .append("circle")
   .attr("class","p3")
   .attr("cx", function(d,i){ return d;}) 
   .attr("cy", function(d,i){ return 200;})
   .attr("r", 10)
   .style("fill", "white");

/* rangeBands */
var points0 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 0)
                     .range();

var points1 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 1)
                     .range();

var points2 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 2)
                     .range();

var points3 = d3.scale.ordinal()
                     .domain([1, 2, 3, 4])
                     .rangePoints([0, 300], 3)
                     .range();


/** svg空間作成 */
var svg02 =  d3.select("#svg02")
                      .append("svg")
                      .attr("width", 600)
                      .attr("height", 150)
                      .style("background",bgColor);

var o = d3.scale.ordinal()
								.domain([1, 2, 3, 4])
								.rangeBands([0, 600],1,1)
								.range();

svg02.selectAll(".bands")
     .data(o)
     .enter()
     .append("rect")
     .attr("class","bands")
     .attr("x", function(d){return d})
     .attr("y", 50)
     .attr("width",60)
     .attr("height",30)
     .style("fill","orange");
     								
</script>