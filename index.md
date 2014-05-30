---
title: d3
layout: default
---

#Data-Driven Documents

- - -

####D3.jsはデータに基づくドキュメントを操作するためのJavaScriptライブラリです。
####HTML、SVG、CSSを使いデータに命を吹き込みます。

###index

<div id="row">
	<div class="col-sm-4">
		<h3><span class="label label-info">d3の基本</span></h3>
		<ol class="post-list">
 			{% for post in site.categories.basics %}
   				<li><a href="{{ post.url }}">{{ post.postTitle }}</a></li>
 			{% endfor %}
		</ol>			

	</div>
	<div class="col-sm-4">
		<h4>Interpolation</h4>
		<ul class="post-list">
 			{% for post in site.categories.interpolation %}
   				<li><a href="{{ post.url }}">{{ post.postTitle }}</a></li>
 			{% endfor %}
		</ul>			

		<h4>Scales</h4>
		<ul class="post-list">
 			{% for post in site.categories.scale %}
   				<li><a href="{{ post.url }}">{{ post.postTitle }}</a></li>
 			{% endfor %}
		</ul>			
		<h4>Layouts</h4>
		<ul class="post-list">
 			{% for post in site.categories.layout %}
   				<li><a href="{{ post.url }}">{{ post.postTitle }}</a></li>
 			{% endfor %}
		</ul>			
	</div>
	<div class="col-sm-4">
		<h4>Shapes</h4>
		<ul class="post-list">
 			{% for post in site.categories.shapes %}
   				<li><a href="{{ post.url }}">{{ post.postTitle }}</a></li>
 			{% endfor %}
		</ul>			
	</div>

</div>