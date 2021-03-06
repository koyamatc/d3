---
title: d3
layout: post
postTitle: 経緯度線 
categories: Geo maps
---
<div class="row">
  <div class="col-sm-9">
    <div id="svg"></div>
  </div>
  <div class="col-sm-3">
    <div class="row">
      <div class="col-sm-6">
        <span class="label label-info">Clip Angle</span>
      </div>  
      <div class="col-sm-6">
        <select data-bind="options: clipAngle,
                        value: selectedClipAngle,
                        valueAllowUnset: true">
        </select>
      </div>
    </div>  
    <div class="row">
      <div class="col-sm-6">
        <span class="label label-info">Graticules</span>
      </div>
      <div class="col-sm-6">  
        <select data-bind="options: graticules,
                        value: selectedGraticules,
                        valueAllowUnset: true">
        </select>
      </div>
    </div>
    <div class="row">
      <div class="col-sm-6">
        <span class="label label-info">Steps</span>
      </div>
      <div class="col-sm-6">  
        <select data-bind="options: long,
                        value: selectedLong,
                        valueAllowUnset: true">
        </select>
        <select data-bind="options: lang,
                        value: selectedLang,
                        valueAllowUnset: true">
        </select>
      </div>
    </div>

    <div class="row">
      <div class="col-sm-6">
        <span class="label label-info">Country</span>
      </div>
      <div class="col-sm-6">  
        <select data-bind="options: country,
                        value: selectedCountry,
                        valueAllowUnset: true">
        </select>
      </div>
    </div>

  </div>
</div>

{% highlight javascript %}
// 経緯度線用グループ追加
var graticuleGroup =g.append("g");
// 経緯度線の取得  
var graticule = d3.geo.graticule()
       .step([selectedLong(),selectedLang()]);
// 経緯度線の描画
graticuleGroup.append("path")
     .datum(graticule)
     .attr("class", "graticule")
     .attr("d", path)
     .attr("stroke","red")
     .attr("stroke-width","1px")
     .style("fill","none")
     .attr("opacity",function(){
          return selectedGraticules()?1:0;
      });
{% endhighlight %}

### step();
.step([経度線の間隔,緯度線の間隔]);


<script src="http://d3js.org/d3.v3.min.js"></script>
<script src="http://d3js.org/queue.v1.min.js"></script>
<script src="http://d3js.org/topojson.v0.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>
<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  // knockout select 
  clipAngle = [90,180];
  selectedClipAngle = ko.observable(90);
  graticules = [false,true];
  selectedGraticules = ko.observable(true);
  long = [5,10,15,20,30,45,60,80,90];
  selectedLong = ko.observable(30);
  lang = [5,10,15,20,30,45,60,80,90];
  selectedLang = ko.observable(30);
  country =["","Japan"]   
  selectedCountry = ko.observable("");

  var width = 900,
     height = 700;　// svg の高さと幅

  var color = d3.scale.category20c(); // 色  
  var countryName = [];               // 国の配列
  var sens = 0.25;　                   // ドラッグ時の感度
  var focused;

  var svg = d3.select("#svg").append("svg")
        .attr("width", width)
        .attr("height", height);　  // svg コンテナ追加

  var g = svg.append("g");          // 全要素のグループを追加
  var sphereGroup = g.append("g");  // sphere用グループ追加
  var graticuleGroup =g.append("g");// 経緯度線用グループ追加
  var countryGroup = g.append("g"); // 国用グループ追加

  var projection;　// プロジェクション用
  var path;       // path用



  /** グローブの描画 **/
  f_clipAngle = ko.computed(function() {

    // 経緯度線の取得  
    var graticule = d3.geo.graticule()
                    .step([selectedLong(),selectedLang()]);

    // プロジェクションの指定
    projection = d3.geo.orthographic()
              .scale(350) 
              .translate([width / 2, height / 2])
              .clipAngle(selectedClipAngle());
    // パスの指定
    path = d3.geo.path()
                   .projection(projection); 
   
    // パスの削除               
    g.selectAll("path").remove();
    // 輪郭の描画
    sphereGroup.append("path")
      .datum({type: "Sphere"})
      .attr("class", "sphere")
      .attr("d", path)
      .style("fill","navy");
   
    // 経緯度線の描画
    graticuleGroup.append("path")
         .datum(graticule)
         .attr("class", "graticule")
         .attr("d", path)
         .attr("stroke","red")
         .attr("stroke-width","1px")
         .style("fill","none")
         .attr("opacity",function(){
              return selectedGraticules()?1:0;
             });

    // topojsonを読み込み　国の描画
    d3.json("{{site.url}}/assets/json/countries.topojson", function(error, world) {
      countryName = [];

      // 国の情報を取り出す
      var countries = topojson.object(world, world.objects.world).geometries;
      // 国の描画
      countryGroup.selectAll("path")
          .data(countries)
        .enter().append("path")
          .attr("d", path)
          .attr("class","country")
          .attr("id", function(d,i){
            countryName.push(d.properties.name);
            return "country" +i;})
          .style("fill",function(d,i){
            return (selectedClipAngle()==90) ? "#ddd":color(i%20);})
          .call(drag)
          .on("mouseover",function(d,i){mouseOver(i)})
          .on("mouseout",function(d,i){mouseOut(i)});
      // 境界線を描画   
      countryGroup.append("path")
        .datum(topojson.mesh(world, world.objects.world, function(a, b) { return a !== b; }))
        .attr("d", path)
        .attr("class", "boundary")
        .style("fill", "none")
        .attr("stroke", "#777")
        .attr("stroke-dasharray", "2,2")
        .attr("stroke-linejoin", "round"); 
      })
  }, this);

  // ドラッグの設定
  var drag = d3.behavior.drag()
        .origin(function() { 
           var r = projection.rotate();
           return {x: r[0] / sens, y: -r[1] / sens}; })
        .on("drag", function() {
            var rotate = projection.rotate();
            projection.rotate([d3.event.x * sens, -d3.event.y * sens, rotate[2]]);

            g.selectAll("path").attr("d", path);
            g.selectAll("text").attr("d", path)
                    .attr("x",function(d){
                        return d3.mouse(this)[0];
                      })
                    .attr("y",function(d){
                        return d3.mouse(this)[1]-20;
                      });            
          }); 

  //** マウスオーバーの設定 **/
  function mouseOver(id){
    var el = "#country" + id;
    d3.select(el).style("fill","red");
    countryGroup.append("text")
    .text(countryName[id])
    .attr("x",function(){return d3.mouse(this)[0] +0;})
    .attr("y",function(){return d3.mouse(this)[1] -20;})
    .attr("stroke","gold")
    .style("fill","gold")
    .attr("font-size","1em")
    .attr("font-family","sans-serif");
  }
  //** マウスアウトの設定 **//
  function mouseOut(id){
    var el = "#country" + id;
    d3.select(el).style("fill",
      function(){return (selectedClipAngle()==90) ? "#ddd":color(id%20);});
    d3.selectAll("text").remove();
   }
   

  selectCountry = ko.computed(function(){
    selectedCountry();


  },this);


};

// Activates knockout.js
ko.applyBindings(new AppViewModel());
</script>
