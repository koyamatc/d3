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
  </div>
  <div class="col-sm-6 col-md-6">
  </div>
</div>
<div class="row">
  <div class="col-md-6">
    <div id="angles"></div>
  </div>
</div>

<p>仮想の球面上に描いた点を、カーソルキー（矢印キー）を使い</p>
<p>視線を移動して見回します</p>
<p>実際は球体を回転させて視野を変更しています</p>
{% highlight javascript %}
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
  var scrollTop = 500; 
  $window.scrollTop(scrollTop);
  
  var sphereRadius = 400;　// 天球の半径
  var height = 500;　// 画面の高さ
  var width = 700;  // 画面の幅
  var pi2 = Math.PI * 2;
  var pi = Math.PI;
  var aDegree = Math.PI / 180;
  var thetaX = 0;
  var thetaY = 0;
  var thetaZ = 0;
   
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

  points0.push(new Point3d(0,0,sphereRadius,"Z",10));
  points0.push(new Point3d(0,0,-sphereRadius,"Z-",10));

  for (var i = 0; i < points0.length; i++) {
    if (isInBound(points0[i].x,points0[i].y,points0[i].z)){
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
          var x = 350 * d.x / d.y;
          //console.log(d.x);
          return xScale(x); 
        })
       .attr("cy", function (d) { return yScale(d.z); })
       .attr("r", function (d) { return d.r; })
       .style("fill", function(d) { return "#fff";});

  svg01.selectAll("text")
   .data(points)
   .enter()
   .append("text")
   .attr("x", function(d) { 
      var x = 350 * d.x / d.y;
      return xScale(x);} ) // x座標の位置
   .attr("y", function(d) { return yScale(d.z -30);}) // y座標の位置
   //.attr("text-anchor", function(d){ return d.anchor}) // x,y座標に対してアンカー指定 
   .text(function(d) {return d.label;})  // 文字列の設定
   .attr("font-family", "sans-serif") // font属性
   .attr("font-size", "20px") // fontｻｲｽﾞ
   .style("fill","#fff"); 

   //console.log(points.length);

   var angles = "RA= " + Math.floor(thetaZ/aDegree) 
             + "  λ= " + Math.floor(thetaX/aDegree);
   $("#angles").html(angles); 
}

  draw();

  // スクロール
  function rotation(){
      
      points = [];

      var count = points0.length;

      for (var i = 0; i < count; i++) {

          var x = points0[i].x;
          var y = points0[i].y;
          var z = points0[i].z;
          
          var x0 = x * Math.cos(thetaZ) + y * Math.sin(thetaZ); 
          var y0 = -x * Math.sin(thetaZ) + y * Math.cos(thetaZ);
          var z0 = z;

          var x1 = x0 * Math.cos(thetaY) - z0 * Math.sin(thetaY); 
          var y1 = y0;
          var z1 = -x0 * Math.sin(thetaY) + z0 * Math.cos(thetaY);

          var x2 = x1;
          var y2 = y1 * Math.cos(thetaX) + z1 * Math.sin(thetaX); 
          var z2 = -y1 * Math.sin(thetaX) + z1 * Math.cos(thetaX);

/*
          x2 *= -1;
          y2 *= -1;
          z2 *= -1;
 */         
          if ( isInBound( x2, y2, z2) ){
            points.push( new Point3d( x2, y2, z2, points0[i].label, points0[i].r ));
          }
      };
      
      //console.log("x=" + thetaX + " y=" + thetaY + " z=" + thetaZ);
      //  console.log(points);
  }

  function isInBound( x, y, z ){

    if ( y >= 0 ) { return true} 
      else { return false}
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
    thetaZ -= rad;
    rotation();
  }
  if (keyPressed['Up']) {
    $window.scrollTop(scrollTop);
    thetaX += rad;
    rotation();
  }
  if (keyPressed['Right']) {
    thetaZ += rad;
    rotation();
  }
  if (keyPressed['Down']) {
    $window.scrollTop(scrollTop);
    thetaX -= rad;
    rotation();
  }
  draw();
};

d3.timer(keyEvent);              
</script>