---
title: d3
layout: post
postTitle: Cursor Press 
categories: event
---

## カーソルキーイベント

<div class="row">
  <div class="col-sm-6 col-md-6">
    <div id="svg01"></div>
    <button id="Rotate" class="btn btn-info">Rotate</button>
  </div>
  <div class="col-sm-6 col-md-6">
    <h3>
    <ul>
      <li>ドラッグできる円とできない円の制御</li>
      <li>ドラッグ開始時の属性設定</li>
      <li>ドラッグ中の制御</li>
      <li>ドラッグ終了時の属性設定</li>
    </ul>
    </h3>
  </div>
</div>
{% highlight javascript %}
  var radius = 25;　// 円の半径
  var height = 500;　// 画面の高さ
  var width = 500;  // 画面の幅

  // 円のデータ
  var circles = [
    {"cx":100,"cy":100 ,"r":radius,"class":"draggable","fill":"yellow"},
    {"cx":200,"cy":200,"r":radius,"class":"undraggable","fill":"red"},
    {"cx":300,"cy":300 ,"r":radius,"class":"draggable","fill":"lime"},
    {"cx":400,"cy":400 ,"r":radius,"class":"draggable","fill":"aqua"}
  ];

  // svg空間作成 
  var svg01 =  d3.select("#svg01")
                 .append("svg:svg")
                 .attr("height", 500)
                 .attr("width", 500)
                 .style("background","#111");

  /** add circles */
  var circle = svg01.selectAll("circle")
                .data(circles)
                .enter()
                .append("circle");
  // 属性             
  var circleAttributes = circle
       .attr("cx", function (d) { return d.cx; })
       .attr("cy", function (d) { return d.cy; })
       .attr("r", function (d) { return d.r; })
       .style("fill", function(d) { return d.fill;})
       .attr("class", function(d) { return d.class;});
  
  // ドラッグ時の挙動
  var drag = d3.behavior.drag()
        //ドラッグ開始時の処理
       .on("dragstart", function(){
            d3.select(this).attr("opacity",0.4)
          }) 
       　//ドラッグ中の処理
       .on("drag", dragmove)
        //ドラッグ終了時の処理
       .on("dragend", function(){ 
            d3.select(this).attr("opacity",1)
          });

  // クラスがdraggableのときdragを呼び出す 
  d3.selectAll(".draggable").call(drag);

  // ドラッグ中の制御　画面をはみ出さない
  function dragmove(d){
    d3.select(this)
      .attr("cx", 
        d.x = Math.max(radius, Math.min(width - radius, d3.event.x)))
      .attr("cy", 
        d.y = Math.max(radius, Math.min(height - radius, d3.event.y)));
  };              

{% endhighlight %}    

<script src="//code.jquery.com/jquery-1.11.3.js"></script>
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script src="{{site.url}}/js/d3draws.js"></script>
<script src="https://cdn.rawgit.com/google/code-prettify/master/loader/run_prettify.js?skin=sons-of-obsidian"></script>

<script>
var $window = $(window)
  // make code pretty
  $('pre').addClass('prettyprint');
  $('pre').css({"background":"#111",
                 "font-size":"1.05em",
                    "border":"0px"}
                );
  $('code').css({"font-size":"1.05em","color":"#f00"});
  $('canvas').css({"background":"#fff"});
  
  var sphereRadius = 500;　// 天球の半径
  var height = 500;　// 画面の高さ
  var width = 700;  // 画面の幅
  var pi2 = Math.PI * 2;
  var pi = Math.PI;
  var aDegree = Math.PI / 180;
  
  function Point3d(x, y, z, label, r){
    this.x = x;
    this.y = y;
    this.z = z;
    this.label = label;
    this.r = r;
  };
  
  // 点のデータ
  points0 = [];
  points = [];

  var theta = -pi/2;
  for (var i = 0; i <= 23; i++) {
    var x = sphereRadius * Math.cos(i*aDegree*15);
    var y = sphereRadius * Math.sin(i*aDegree*15);
    var x_ = x * Math.cos(theta) + y * Math.sin(theta);
    var y_ = -x * Math.sin(theta) + y * Math.cos(theta);

    points0.push(new Point3d(x_,y_,0,i,4));
  };
  
  theta = aDegree *45;
  var count = points0.length;
  for (var i = 0; i < count; i++) {
    var x_ = points0[i].x * Math.cos(theta) - points0[i].z * Math.sin(theta);
    var y_ = points0[i].y;
    var z_ = points0[i].x * Math.sin(theta) + points0[i].z * Math.cos(theta);
    points0.push(new Point3d(x_,y_,z_,i+count,4));
  };

  for (var i = 0; i < points0.length; i++) {
    if (points0[i].y >=0){
      points.push(points0[i]);
    }
  };


  // svg空間作成 
  var svg01 =  d3.select("#svg01")
                 .append("svg:svg")
                 .attr("height", height)
                 .attr("width", width)
                 .style("background","#111");

  // scale 
  var xScale = d3.scale.linear()
                       .domain([-width/2, width/2])
                       .range([0, width]);
  var yScale = d3.scale.linear()
                       .domain([-height/2, height/2])
                       .range([height, 0]);
               

function draw(){

  d3.selectAll("circle").remove();
  d3.selectAll("text").remove();

  /** add circles */
  var circle = svg01.selectAll("circle")
                .data(points)
                .enter()
                .append("circle");
  // 属性             
  var circleAttributes = circle
       .attr("cx", function (d) { 
          var x = 60 * d.x / d.y;
          //console.log(d.x);
          return xScale(d.x); 
        })
       .attr("cy", function (d) { return yScale(d.z); })
       .attr("r", function (d) { return d.r; })
       .style("fill", function(d) { return "#fff";});

  svg01.selectAll("text")
   .data(points)
   .enter()
   .append("text")
   .attr("x", function(d) { 
      var x = 60 * d.x / d.y;
      return xScale(d.x);} ) // x座標の位置
   .attr("y", function(d) { return yScale(d.z -30);}) // y座標の位置
   //.attr("text-anchor", function(d){ return d.anchor}) // x,y座標に対してアンカー指定 
   .text(function(d) {return d.label;})  // 文字列の設定
   .attr("font-family", "sans-serif") // font属性
   .attr("font-size", "20px") // fontｻｲｽﾞ
   .style("fill","#fff"); 
}

  draw();

  var step = 0
  function rotation(rad){
      points = [];
      step += rad;
      for (var i = 0; i < points0.length; i++) {
        var x = points0[i].x * Math.cos(step) + points0[i].y * Math.sin(step); 
        var y = -points0[i].x * Math.sin(step) + points0[i].y * Math.cos(step);
        var z = points0[i].z;
        if (y >=0){
          points.push( new Point3d(x,y,z,i,4));
        }

      };


  }

  var keyPressed = {};

  d3.select('body')  
  .on('keydown', function() {
    keyPressed[d3.event.keyIdentifier] = true;
  })
  .on('keyup', function() {
    keyPressed[d3.event.keyIdentifier] = false;
  });

  var rad = aDegree * 1; 
  var keyEvent = function() {

  if (keyPressed['Left']) {
    rotation(-rad);
  }
  if (keyPressed['Up']) {
    //triangle.y = isInBounds(y - triangle._speed, 'height');
  }
  if (keyPressed['Right']) {
    rotation(rad);
  }
  if (keyPressed['Down']) {
    //triangle.y = isInBounds(y + triangle._speed, 'height');
  }
  draw();
};

d3.timer(keyEvent);              
</script>