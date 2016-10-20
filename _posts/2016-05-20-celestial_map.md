---
title: d3
layout: post
postTitle: 星図
categories: Geo maps
---

<div class="row">
  <div class="col-xs-12">
    <div id="svg"></div>
  </div>
</div>
<div class="row">
    <div class="col-xs-5 col-sm-2 col-md-2">
        <span class="label label-info">Graticules(経緯線)</span>
    </div>
    <div class="col-xs-2">  
        <select data-bind="options: graticules,
                        value: selectedGraticules,
                        valueAllowUnset: true">
        </select>
    </div>
</div>

<script src="http://d3js.org/d3.v3.js"></script>
<script src="http://d3js.org/queue.v1.min.js"></script>
<script src="http://d3js.org/topojson.v1.min.js"></script>
<script src="https://cdn.jsdelivr.net/lodash/4.12.0/lodash.min.js"></script>
<script src="{{site.url}}/js/knockout-3.1.0.js" charset="utf-8"></script>
<script type="text/javascript">
/**
  ApplicationViewModel
**/
function AppViewModel() {

  graticules = [false,true];
  selectedGraticules = ko.observable(true);

  var width = 700,
     height = 700;　// svg の高さと幅

  var starsName = [];               　　// 星名の配列
   var dsosName = []; 

  var svg = d3.select("#svg").append("svg")
        .attr("width", width)
        .attr("height", height);　  // svg コンテナ追加

  var g = svg.append("g");          // 全要素のグループを追加
  var sphereGroup = g.append("g");  // sphere用グループ追加
  var graticuleGroup =g.append("g");// 経緯度線用グループ追加
  var starsGroup = g.append("g"); 　　// 星用グループ追加
  var dsosGroup = g.append("g");
  var projection;　// プロジェクション用
  var path;       // path用
  var overlay;
  var rScale = 1;
  // プロジェクションのローテーションとスケール
  var state = {x: 40, y: -45, scale: height / 2};                
  
  /** グローブの描画 **/
  f_clipAngle = ko.computed(function() {
    // 経緯度線の取得  
    var graticule = d3.geo.graticule();

    // プロジェクションの指定
    projection = d3.geo.stereographic()
              .scale(state.scale) 
              .translate([width / 2, height / 2])
              .clipAngle(90)
              .rotate([state.x, state.y]);


    // パスの指定
    path = d3.geo.path()
             .projection(projection)
             .pointRadius(function(d){
                var mag = _.get(d.properties,'mag');
                if (mag === null) return 0.1; 
                  var r = 2.5 * Math.exp(-0.28 * (mag+2)) * rScale;
                  return Math.max(r, 0.1);
              }); 
   
    // パスの削除               
    g.selectAll("path").remove();
    // 輪郭の描画
    sphereGroup.append("path")
      .datum({type: "Sphere"})
      .attr("class", "sphere")
      .attr("d", path)
      .style("fill","#000");
   
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

     // overlay 
     overlay = sphereGroup.append('circle').datum(state)
             .attr('r', height / 2)        
             .attr('transform', function() {
                 return 'translate(' + [width / 2, height / 2] + ')';        })
             .attr('fill-opacity', 0);          


    // topojsonを読み込み　星の描画
    d3.json("{{site.url}}/assets/json/hyg.topojson", function(error, json) {
      starsName = [];
      // 星の情報を取り出す
      starsGroup.selectAll("path")
          .data(json.objects.hyg.features)
        .enter().append("path")
          .attr("d", path)
          .attr("class", "star")
          .attr("id", function(d,i){
            starsName.push(d.properties.name);
            return "star" +i;})
          .style("fill",function(d,i){
            return ci(d.properties.color);})
           .on("mouseover",function(d,i){mouseOver(i)})
          .on("mouseout",function(d,i){mouseOut(i)});


    // topojsonを読み込み　星の描画
    d3.json("{{site.url}}/assets/json/dso.topojson", function(error, json) {
      dsosName = [];
      // 星の情報を取り出す
 /*
      dsosGroup.selectAll("path")
          .data(json.objects.dso.features)
        .enter().append("path")
          .attr("d", path)
          .attr("class", "dso")
          .attr("id", function(d,i){
            dsosName.push(d.properties.name)
            return "dso" +i;})
          .style("fill",function(d,i){
            return "aqua";})
           .on("mouseover",function(d,i){mouseOver2(i)})
          　.on("mouseout",function(d,i){mouseOut2(i)});
  */
       dsosGroup.selectAll("ellipse")
          .data(json.objects.dso.features)
        .enter().append("ellipse")
          .attr("d", path)
          .attr("class", "dso")
          .attr("id", function(d,i){
            dsosName.push(d.properties.name)
            return "dso" +i;})
          .attr("cx",function(d){
            return d.geometry.coordinates[0]
          })
          .attr("cy",function(d){
            return d.geometry.coordinates[1]
          })
          .attr("rx", function(d){
            return 10;//d.properties.r1;
          })
          .attr("ry", function(d){
            return 10//;d.properties.r2==null?d.properties.r1:d.properties.r2;
          })
          .style("fill",function(d,i){
            return "aqua";})
           .on("mouseover",function(d,i){mouseOver2(i)})
          　.on("mouseout",function(d,i){mouseOut2(i)});
 
      });

      // zoom behaviorを設定
      var zoomBehavior = d3.behavior.zoom()
              .scaleExtent([0.5, 8])
              .on('zoom', zoom);
      // overlayに　zoom behaviorのイベント　リスナーを設定
      overlay.call(zoomBehavior); 
   });
 }, this);

  // ** マウスオーバーの設定 
  function mouseOver(id){
    var el = "#star" + id;
    starsGroup.append("text")
    .text(starsName[id])
    .attr("x",function(){return d3.mouse(this)[0] +0;})
    .attr("y",function(){return d3.mouse(this)[1] -20;})
    .attr("stroke","gold")
    .style("fill","gold")
    .attr("font-size","1em")
    .attr("font-family","sans-serif");
  };

  // マウスアウトの設定 
  function mouseOut(id){
    var el = "#star" + id;
    d3.selectAll("text").remove();
   };

  // ** マウスオーバーの設定 
  function mouseOver2(id){
    var el = "#dso" + id;
    starsGroup.append("text")
    .text(dsosName[id])
    .attr("x",function(){return d3.mouse(this)[0] +0;})
    .attr("y",function(){return d3.mouse(this)[1] -20;})
    .attr("stroke","gold")
    .style("fill","gold")
    .attr("font-size","1em")
    .attr("font-family","sans-serif");
  };
  // マウスアウトの設定 
  function mouseOut(id){
   // var el = "#star" + id;
    d3.selectAll("text").remove();
   };

  // zoom 設定
  function zoom(d) {    
      // プロジェクションの scale と translate　を取得
      var scale = d3.event.scale,
             dx = d3.event.translate[0],
             dy = d3.event.translate[1];
       rScale = scale;      
      // 回転角を計算
      d.x = 180 / width * dx;    // Horizontal rotation
      d.y = -180 / height * dy;  // Vertical rotation
     // 新しい回転とスケールでプロジェクションを更新
     projection.rotate([d.x, d.y])
               .scale(scale * d.scale);
    // path と overlay 半径　再計算
    svg.selectAll('path').attr('d', path);
    overlay.attr('r', scale * height / 2);          
  };

  // color index to color
  function ci(i) {
      
      if (i >= 1.4){
        return "#f00";
      } else if (i >= 0.7) {
        return "#FAA0E9";
      } else if (i >= 0.2) {
        return "#ff0";
      } else if (i < -0.1) {
        return "#9DBCF5";
      } else {
        return "#fff";
      }
     
  }
};

// Activates knockout.js
ko.applyBindings(new AppViewModel());
</script>
