---
title: d3
layout: post
postTitle: Interactive Graph
categories: Transition graph
---

<div id="svg01"></div>

<span class="label">年</span>
   <select data-bind="options: years,
                      value: selectedYear,
                      valueAllowUnset: true"></select>

</div>

<< 年を変更してください >>

{% highlight javascript %}
function AppViewModel() {

  var width = 600,
     height = 400;

  var xScale = d3.scale.linear()
                       .domain([1,12])
                       .range([50,580]);
  
  var yScale = d3.scale.linear()
                       .domain([0,80])
                       .range([360,20]);                       

  var line = d3.svg.line()
      .x(function(d,i) { return xScale(i+1); })
      .y(function(d) { return yScale(d); })
      .interpolate("linear");

  var svg01 = d3.select("#svg01").append("svg")
        .attr("width", width)
        .attr("height", height);

  //軸生成
  var xAxis = d3.svg.axis()
                   .scale(xScale);
  var yAxis = d3.svg.axis()
                   .scale(yScale)
                   .orient(["left"]);
  typeof(xAxis);
  typeof(yAxis);

  //　軸グループ生成し軸関数を呼ぶ
  var xAxisGroup = svg01.append("g")
          .attr("transform","translate(0,"+ yScale(0)+")")
          .attr("stroke","white")
          .attr("stroke-width","1")
          .style("fill","none")
          .call(xAxis);

  var yAxisGroup = svg01.append("g")
            .attr("transform","translate(" + xScale(0.6) + ",0)")
            .attr("stroke","white")
            .attr("stroke-width","1")
            .style("fill","none")
            .call(yAxis);
  // ラインデータ
  var lineData = [
    {year:'2012',values:[40,40,10,20,50,50,30,70,40,30,40,50]},
    {year:'2013',values:[10,20,30,40,60,60,50,40,30,20,10,30]},
    {year:'2014',values:[60,50,70,60,20]}
  ];

  // knockout select 
  years = ['2012','2013','2014'];
  this.selectedYear = ko.observable('2013');

  this.year = ko.computed(function() {
    data = _.where(lineData,{year:this.selectedYear()});
    monthData = _.map(data,function(data){return data.values});
    redraw();    
  }, this);

  draw(); // 初期描画
  
  //** 初期描画 *//
  function draw(){
    svg01.selectAll(".graph").remove();

    svg01.selectAll(".graph")
      .data(monthData)
    .enter().append("path")
      .attr("stroke", "yellow")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("class","graph")
      .attr("d", line);
  };
  // 再描画
  function redraw(){
    svg01.selectAll(".graph")
      .data(monthData)
      .attr("class","graph")
      .transition()
      .duration(1000)
      .ease("linear")
      .attr("stroke", "yellow")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("d", line);
  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());
{% endhighlight %}

<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>
<script src="{{site.url}}/js/underscore.js" charset="utf-8"></script>

<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  var width = 600,
     height = 400;

  var xScale = d3.scale.linear()
                       .domain([1,12])
                       .range([50,580]);
  
  var yScale = d3.scale.linear()
                       .domain([0,80])
                       .range([360,20]);                       

  var line = d3.svg.line()
      .x(function(d,i) { return xScale(i+1); })
      .y(function(d) { return yScale(d); })
      .interpolate("linear");

  var svg01 = d3.select("#svg01").append("svg")
        .attr("width", width)
        .attr("height", height);

  //軸生成
  var xAxis = d3.svg.axis()
                   .scale(xScale);
  var yAxis = d3.svg.axis()
                   .scale(yScale)
                   .orient(["left"]);
  typeof(xAxis);
  typeof(yAxis);

  //　軸グループ生成し軸関数を呼ぶ
  var xAxisGroup = svg01.append("g")
          .attr("transform","translate(0,"+ yScale(0)+")")
          .attr("stroke","white")
          .attr("stroke-width","1")
          .style("fill","none")
          .call(xAxis);

  var yAxisGroup = svg01.append("g")
            .attr("transform","translate(" + xScale(0.6) + ",0)")
            .attr("stroke","white")
            .attr("stroke-width","1")
            .style("fill","none")
            .call(yAxis);
  // ラインデータ
  var lineData = [
    {year:'2012',values:[40,40,10,20,50,50,30,70,40,30,40,50]},
    {year:'2013',values:[10,20,30,40,60,60,50,40,30,20,10,30]},
    {year:'2014',values:[60,50,70,60,20]}
  ];

  // knockout select 
  years = ['2012','2013','2014'];
  this.selectedYear = ko.observable('2013');

  this.year = ko.computed(function() {
    data = _.where(lineData,{year:this.selectedYear()});
    monthData = _.map(data,function(data){return data.values});
    redraw();    
  }, this);

  draw(); // 初期描画
  
  //** 初期描画 *//
  function draw(){
    svg01.selectAll(".graph").remove();

    svg01.selectAll(".graph")
      .data(monthData)
    .enter().append("path")
      .attr("stroke", "yellow")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("class","graph")
      .attr("d", line);
  };
  // 再描画
  function redraw(){
    svg01.selectAll(".graph")
      .data(monthData)
      .attr("class","graph")
      .transition()
      .duration(1000)
      .ease("linear")
      .attr("stroke", "yellow")
      .attr("stroke-width",3)
      .attr("fill", "none")
      .attr("d", line);
  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());

</script>
