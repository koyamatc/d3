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


{% highlight javascript %}
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
     height = 400
     radius = Math.min(width, height) / 2;

  var color = d3.scale.category20();

  var pie = d3.layout.pie()
      .value(function(d) { return d.apples; })
      .sort(null);

  var arc = d3.svg.arc()
      .innerRadius(radius - 100)
      .outerRadius(radius - 20);   
  
  var svg01 = d3.select("#svg01").append("svg")
        .attr("width", width)
        .attr("height", height)
      .append("g")
        .attr("transform", "translate(" + width / 2 + "," + height / 2 + ")");

  pie = [
    {key:'2012',values:[40,40,10,20,50,50,30,70,40,30,40,50]},
    {key:'2013',values:[10,20,30,40,60,60,50,40,30,20,10,30]}
  ];
  
  this.selectedYear = ko.observable('2013');
  var data = _.where(pie,{key:this.selectedYear()});
  console.log(data);

  this.year = ko.computed(function() {
        draw(this.selectedYear());
  }, this);

  
  function draw(s){

    var path = svg01.selectAll("path")
      .data(data)
    .enter().append("path")
      .attr("fill", function(d, i) { return color(i); })
      .attr("d", arc)
      .each(function(d) { this._current = d; }); // store the initial angles

    
  function change() {
    var value = this.value;
    pie.value(function(d) { return d[value]; }); // change the value function
    path = path.data(pie); // compute the new angles
    path.transition().duration(750).attrTween("d", arcTween); // redraw the arcs
  }
  };
  
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());


</script>
