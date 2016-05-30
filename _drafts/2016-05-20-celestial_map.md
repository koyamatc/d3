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
<script src="http://d3js.org/d3.v3.js"></script>
<script src="http://d3js.org/queue.v1.min.js"></script>
<script src="http://d3js.org/topojson.v1.min.js"></script>
<script src="https://cdn.jsdelivr.net/lodash/4.12.0/lodash.min.js"></script>
<script type="text/javascript">

  var width = 900,
     height = 900;　// svg の高さと幅

  var color = d3.scale.category20c(); // 色  
  var starsName = [];               　　// 星名の配列
  var sens = 0.25;　                   // ドラッグ時の感度
  var focused;

  var svg = d3.select("#svg").append("svg")
        .attr("width", width)
        .attr("height", height);　  // svg コンテナ追加

  var g = svg.append("g");          // 全要素のグループを追加
  var sphereGroup = g.append("g");  // sphere用グループ追加
  var graticuleGroup =g.append("g");// 経緯度線用グループ追加
  var starsGroup = g.append("g"); 　　// 星用グループ追加
  var projection;　// プロジェクション用
  var path;       // path用

  /** グローブの描画 **/
/*
    // 経緯度線の取得  
    var graticule = d3.geo.graticule();

    // プロジェクションのローテーションとスケール
    var state = {x: 0, y: -45, scale: height / 2};                
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
                  var r = 5 * Math.exp(-0.28 * (mag+2));
                  return Math.max(r, 0.1);
              }); 
   
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
              return selectedGraticules()?0.7:0;
             });

    // topojsonを読み込み　国の描画
    d3.json("{{site.url}}/assets/json/hyg.topojson", function(error, json) {
      starsName = [];
      // 国の情報を取り出す
      starsGroup.selectAll("path")
          .data(json.objects.hyg.features)
        .enter().append("path")
          .attr("d", path)
          .attr("class",function(d,i){
            return "star";
          })
          .attr("id", function(d,i){
            starsName.push(d.properties.name);
            return "star" +i;})
          .style("fill",function(d,i){
            return (selectedClipAngle()==90) ? "#ddd":color(i%20);})
    //      .call(drag)
          .on("mouseover",function(d,i){mouseOver(i)})
          .on("mouseout",function(d,i){mouseOut(i)});
  

     // Append the overlay and set its attributes
     var overlay = svg.append('circle').datum(state)
             .attr('r', height / 2)        
             .attr('transform', function() {
                 return 'translate(' + [width / 2, height / 2] + ')';        })
             .attr('fill-opacity', 0);          

      // Create and configure the zoom behavr
      var zoomBehavior = d3.behavior.zoom()
              .scaleExtent([0.5, 8])
              .on('zoom', zoom);
      // Add event listeners for the zoom gestures to the overlay
      overlay.call(zoomBehavior); 
*/
  //** マウスオーバーの設定 **/
  function mouseOver(id){
    var el = "#country" + id;
    d3.select(el).style("fill","red");
    starsGroup.append("text")
    .text(starsName[id])
    .attr("x",function(){return d3.mouse(this)[0] +0;})
    .attr("y",function(){return d3.mouse(this)[1] -20;})
    .attr("stroke","gold")
    .style("fill","gold")
    .attr("font-size","1em")
    .attr("font-family","sans-serif");
  }

  //** マウスアウトの設定 **//
  function mouseOut(id){
    var el = "#star" + id;
    d3.select(el).style("fill",
      function(){return (selectedClipAngle()==90) ? "#ddd":color(id%20);});
    d3.selectAll("text").remove();
   }
/*
  function zoom(d) {    
      // Compute the projection scale and the constant
      var scale = d3.event.scale,
              dx = d3.event.translate[0],
              dy = d3.event.translate[1];
      // Maps the translation vector to rotation angles...
      d.x = 180 / width * dx;    // Horizontal rotation
      d.y = -180 / height * dy;  // Vertical rotation
     // Update the projection with the new rotation and scale
     projection.rotate([d.x, d.y])
               .scale(scale * d.scale);
    // Recompute the paths and the overlay radius
    svg.selectAll('path').attr('d', path);
    overlay.attr('r', scale * height / 2);          
  }
*/
</script>
