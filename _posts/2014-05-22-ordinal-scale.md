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


###d3.jsの組み込み配色

<div id="svg03"></div>

1. category10
2. category20
3. category20b
4. category20c

####<使い方>

{% highlight javascript %}

// category10の配色を指定
var color10 = d3.scale.category10();
// 色の指定
color10(2)　// #2ca02c

// カスタム配色設定 
var custom = d3.scale.ordinal()
               .domain([0,1,2,3,4])
               .range(["#E6FF36","#6CA1FF","#25E885","#E89C25","#FF2929"]);
custom(1); // 色の指定                    

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


/**  
    Categorical Colors 
                          **/
var width = height = 640;

var bgColor = "black"; // 背景色

/** svg空間作成 */
var svg03 =  d3.select("#svg03")
                      .append("svg")
                      .attr("width", width)
                      .attr("height", height)
                      .style("background",bgColor);


var color10 = d3.scale.category10();
var color20 = d3.scale.category20();
var color20b = d3.scale.category20b();
var color20c = d3.scale.category20c();
var custom = d3.scale.ordinal()
               .domain([0,1,2,3,4])
               .range(["#E6FF36",
                       "#6CA1FF",
                       "#25E885",
                       "#E89C25",
                       "#FF2929"
                     ]);
 
var c10 = [];
var c20 = [];
var head = ["category10","category20","category20b","category20c","custom"]

for (var i = 0; i < 10; i++) {
  c10.push(i);
};
for (var i = 0; i < 20; i++) {
  c20.push(i);
};

svg03.selectAll(".c10")
   .data(c10)
   .enter()
   .append("rect")
   .attr("x", function(d,i){ return 10;})
   .attr("y", function(d,i){ return i * 30 + 30; })
   .attr("width",100)
   .attr("height", 28)
   .attr("class","c10")
   .style("fill", function(d,i){return color10(i)});

svg03.selectAll(".c20")
   .data(c20)
   .enter()
   .append("rect")
   .attr("x", function(d,i){ return 130;})
   .attr("y", function(d,i){ return i * 30 + 30; })
   .attr("width",100)
   .attr("height", 28)
   .attr("class","c20")
   .style("fill", function(d,i){return color20(i)});

svg03.selectAll(".c20b")
   .data(c20)
   .enter()
   .append("rect")
   .attr("x", function(d,i){ return 250;})
   .attr("y", function(d,i){ return i * 30 + 30; })
   .attr("width",100)
   .attr("height", 28)
   .attr("class","c20b")
   .style("fill", function(d,i){return color20b(i)});

svg03.selectAll(".c20c")
   .data(c20)
   .enter()
   .append("rect")
   .attr("x", function(d,i){ return 370;})
   .attr("y", function(d,i){ return i * 30 + 30; })
   .attr("width",100)
   .attr("height", 28)
   .attr("class","c20c")
   .style("fill", function(d,i){return color20c(i)});

svg03.selectAll(".cust")
   .data(custom.range())
   .enter()
   .append("rect")
   .attr("x", function(d,i){ return 490;})
   .attr("y", function(d,i){ return i * 30 + 30; })
   .attr("width",100)
   .attr("height", 28)
   .attr("class","cust")
   .style("fill", function(d,i){return custom(i)});

svg03.selectAll(".head")
   .data(head)
   .enter()
   .append("text")
   .attr("x",function(d,i){ return i*120 + 10; })
   .attr("y", 20)
   .text(function(d){ return d;})
   .attr("font-family", "sans-serif")
   .attr("font-size", "16px")
   .attr("font-weight","bold")
   .attr("class","head")
   .style("fill", "red");


svg03.selectAll(".c10t")
   .data(color10.domain())
   .enter()
   .append("text")
   .attr("x", function(){ return 20; })
   .attr("y", function(d,i){ return i * 30 + 50; })
   .text(function(d,i){ return color10(i);})
   .attr("font-family", "sans-serif")
   .attr("font-size", "16px")
   .attr("class","c10t")
   .style("fill", "#ccc");
  
svg03.selectAll(".c20t")
   .data(color20.domain())
   .enter()
   .append("text")
   .attr("x", function(){ return 140; })
   .attr("y", function(d,i){ return i * 30 + 50; })
   .text(function(d,i){ return color20(i);})
   .attr("font-family", "sans-serif")
   .attr("font-size", "16px")
   .attr("class","c20t")
   .style("fill", "#ccc");

svg03.selectAll(".c20bt")
   .data(color20b.domain())
   .enter()
   .append("text")
   .attr("x", function(){ return 260; })
   .attr("y", function(d,i){ return i * 30 + 50; })
   .text(function(d,i){ return color20b(i);})
   .attr("font-family", "sans-serif")
   .attr("font-size", "16px")
   .attr("class","c20bt")
   .style("fill", "#ccc");

svg03.selectAll(".c20ct")
   .data(color20c.domain())
   .enter()
   .append("text")
   .attr("x", function(){ return 380; })
   .attr("y", function(d,i){ return i * 30 + 50; })
   .text(function(d,i){ return color20c(i);})
   .attr("font-family", "sans-serif")
   .attr("font-size", "16px")
   .attr("class","c20ct")
   .style("fill", "#ccc");

svg03.selectAll(".custt")
   .data(custom.domain())
   .enter()
   .append("text")
   .attr("x", function(){ return 500; })
   .attr("y", function(d,i){ return i * 30 + 50; })
   .text(function(d,i){ return custom(i);})
   .attr("font-family", "sans-serif")
   .attr("font-size", "16px")
   .attr("class","custt")
   .style("fill", "#ccc");

     								
</script>