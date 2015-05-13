---
title: d3
layout: post
postTitle: Mouse Drag 
categories: event
---

## マウスのドラッグイベント

<div class="row">
  <div class="col-sm-6 col-md-6">
    <h3>
    <span class="label label-info　label-lg">
      枠の中の円をマウスでドラッグしてください</span>
    </h3>
    <div id="svg01"></div>
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

<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

<script>
  
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
</script>