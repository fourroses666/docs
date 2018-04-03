
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<h3>SimpleGallery - Displaying a gallery on the page</h3>
SimpleGallery - Gallery output on the Evolution CMS page.
<p>Once this question arose, I will show how to draw a conclusion. The input, I hope, is intuitively understandable (:</p>
<h3 class="sub-header">Displaying a gallery on the page</h3>
<p>An important advantage of DocLister is that now you do not need to fool your bikes to output data from any tables: you do not need to invent parameter names (to later get confused in them), pagination, sorting, etc. All the boring work was done by <a href="http://modx.im/profile/Agel_Nash/" rel="nofollow" target="_blank">Agel_Nash</a>, for which he is once again grateful.</p>
<p>For working with arbitrary tables in DocLister there is an onetable controller. All you need to know is the name of the table, the names of its fields, the name of the key field. Everything else is <a href="doclister/index.html" target="_blank"> in the documentation </a>.</p>
<p>Once again, I'll list the field names in the <span class="text-bold">sg_images</span> table:</p>
<ul>
	<li><span class="text-bold">sg_id</span> – id of the picture, <span class="text-bold">key field (idField)</span>;</li>
	<li><span class="text-bold">sg_index</span> – position in the list;</li>
	<li><span class="text-bold">sg_image</span> – link to the image;</li>
	<li><span class="text-bold">sg_title</span> – the name of the image;</li>
	<li><span class="text-bold">sg_description</span> – description of the image;</li>
	<li><span class="text-bold">sg_properties</span> – here in json format information about the width and height of the picture, as well as the size of the file, you can still store something in this field;</li>
	<li><span class="text-bold">sg_add</span> – is an additional field, I do not remember why I needed it anymore;</li>
	<li><span class="text-bold">sg_isactive</span> – checkbox to hide some pictures from the output;</li>
	<li><span class="text-bold">sg_rid</span> – the id of the resource to which the picture belongs (parentField);</li>
	<li><span class="text-bold">sg_createdon</span> – the date the picture was added.</li>
</ul>

<p>Knowing this, to display pictures from the gallery, it is enough to make such a call on the page with the gallery:</p>
<pre class="brush: html;">
[[DocLister?
&controller=`onetable`
&table=`sg_images`
&idField=`sg_id`
&parentField=`sg_rid`
&idType=`parents`
&addWhereList=`sg_isactive=1`
&tpl=`@CODE:[+sg_image+] [+sg_title+]`
&showParent=`-1`
]]
</pre>

<p>Result:</p>
<pre class="brush: html;">
assets/galleries/2/kitty099h.jpg kitty099h
assets/galleries/2/kitty098p.jpg kitty098p
assets/galleries/2/kitty096j.jpg kitty096j
assets/galleries/2/kitty095s.jpg kitty095s
assets/galleries/2/kitty094c.jpg kitty094c
assets/galleries/2/kitty093p.jpg kitty093p
</pre>
<p>If you add [+pages+] to the page, and add &paginate=`pages` &display=`10` to the call, we get pagination.</p>
<p>That is, the output from your table is not much different from the usual output of documents.</p>
<p>To don't have to write each time &controller=`onetable` &table=`sg_images` &idField=`sg_id`, I made the snippet wrapper sgLister. <a href="http://modx.im/profile/Agel_Nash/" rel="nofollow" target="_blank">Agel_Nash</a> this snippet has been modified and now, when it is called, additional processing of the fields takes place, in chunk you can use such placeholders:</p>
<ul>
	<li>[+thumb.sg_image+], [+thumb.width.sg_image+], [+thumb.height.sg_image+] – thumbnail and its dimensions;</li>
	<li>[+e.sg_title+] and [+e.sg_description+]- the values of the fields sg_title and sg_description, which are converted to html-entities (in order not to break randomly with quotation marks or brackets);</li>
	<li>[+properties.property_name+] – picture properties from the field sg_properties.</li>
</ul>
<p>Use field [+thumb.sg_image+] You can, if you add parameters to a call:</p>
<ul>
	<li><span class="text-bold">&thumbSnippet</span> – The name of the snippet, which will return a link to the preview, for example, phpthumb;</li>
	<li><span class="text-bold">&thumbOptions</span> – parameters for creating previews, in the form in which they will accept the specified snippet.</li>
</ul>
<p>In practice, it looks like this:</p>
<pre class="brush: html;">
[[sgLister? 
&thumbSnippet=`phpthumb`
&thumbOptions=`w=150&h=150&zc=1`
&tpl=`@CODE:
&lt;a href="[+sg_image+]"&gt;
	&lt;img src="[+thumb.sg_image+]" class="img-thumbnail" alt="[+e.sg_title+]" title="[+e.sg_description+]"&gt;
&lt;/a&gt;`
]]
</pre>

<h3 class="sub-header">Displaying a list of galleries</h3>
<p>This task does not occur so often, but it still does, so I <a href="http://modx.im/blog/docs/2759.html" rel="nofollow" target="_blank">expanded the controller</a> site_content, to be able to add images from galleries when displaying the list:</p>
<p>The DocLister call looks like this:</p>
<pre class="brush: html;">
[[DocLister? 
&controller=`sg_site_content`
&prepare=`prepareImages`
&sgOrderBy=`sg_id DESC`
&tpl=`@CODE:
&lt;div class="page-header"&gt;
	&lt;h1&gt;&lt;a href="[+url+]"&gt;[+pagetitle+]&lt;/a&gt;&lt;/h1&gt;
&lt;/div&gt;
[+images+]
&lt;div class="clearfix"&gt;&lt;/div&gt;` 
&sgOuterTpl=`@CODE:[+wrapper+]`
&sgRowTpl=`@CODE:
&lt;a href="[+sg_image+]"&gt;
	&lt;img src="[+thumb.sg_image+]" class="img-thumbnail" alt="[+e.sg_title+]" title="[+e.sg_description+]"&gt;
&lt;/a&gt;`
&sgDisplay=`5`
]]
</pre>

<p>That is the same as simple output of documents, but additional parameters have been added:</p>
<ul>
	<li><span class="text-bold">&sgOuterTpl and &sgRowTpl</span> – chunks for displaying images;</li>
	<li><span class="text-bold">&sgOrderBy</span> – options for sorting images;</li>
	<li><span class="text-bold">&sgDisplay</span> – how many pictures to output, all - to display all;</li>
	<li><span class="text-bold">&sgAddWhereList</span> – here you can specify the conditions for selecting images.</li>
</ul>

<p>In order to display these pictures in the chunk of the document (&tpl), you should use the [+images+] placeholder. However, if you just type [+images+], then the images will not be displayed (because images are an array). We need a prepare-snippet for additional processing (let's call it prepareImages):</p>
<pre class="brush: php;">
&lt;?php
if (isset($data['images'])) {
	$wrapper= '';
	foreach ($data['images'] as $image) {
		$ph = $image;
		$ph['thumb.sg_image'] = $modx-&gt;runSnippet(
			'phpthumb',
			array(
				'input' =&gt; $image['sg_image'],
				'options' =&gt; 'w=150&h=150&zc=1'
			)
		);
		//made a thumbs up

		$ph['e.sg_title'] = htmlentities($image['sg_title'], ENT_COMPAT, 'UTF-8', false);
		$ph['e.sg_description'] = htmlentities($image['sg_description'], ENT_COMPAT, 'UTF-8', false);
		//added fields e.sg_title and e.sg_description

		$wrapper .= $_DocLister-&gt;parseChunk($_DocLister-&gt;getCFGDef('sgRowTpl'), $ph);
		//processed the chunk sgRowTpl - for each image
	}
	$data['images'] = $_DocLister-&gt;parseChunk($_DocLister-&gt;getCFGDef('sgOuterTpl'),array('wrapper'=&gt;$wrapper));
	//processed chunk sgOuterTpl
}
return $data;
?&gt;
</pre>

<p>Prepare-snippet can also come in handy for displaying picture properties. This is the width, height and size of the file - they are stored in the sg_properties field as json. Here everything is simple:</p>
<pre class="brush: php;">
&lt;?php
if (isset($data['sg_properties'])) {
    $properties = json_decode($data['sg_properties'],true);
    $data['width'] = $properties['width'];
    $data['height'] = $properties['height'];
    $data['size'] = $properties['size'];
}
return $data;
?&gt;
</pre>

<p>For some time now, the bundle contains the snippet wrapper sgController, which calls DocLister with the required parameters and does not require a separate prepare-snippet:</p>
<pre class="brush: html;">
[[sgController? 
&sgOrderBy=`sg_id DESC`
&thumbSnippet=`phpthumb`
&thumbOptions=`w=150&h=150&zc=1`
&tpl=`@CODE:
&lt;div class="page-header"&gt;
	&lt;h1&gt;&lt;a href="[+url+]"&gt;[+pagetitle+]&lt;/a&gt;&lt;/h1&gt;
&lt;/div&gt;
[+images+]
&lt;div class="clearfix"&gt;&lt;/div&gt;` 
&sgOuterTpl=`@CODE:[+wrapper+]`
&sgRowTpl=`@CODE:
&lt;a href="[+sg_image+]"&gt;
	&lt;img src="[+thumb.sg_image+]" class="img-thumbnail" alt="[+e.sg_title+]" title="[+e.sg_description+]"&gt;
&lt;/a&gt;`
&sgDisplay=`5`
]]
</pre>
<p>In sgRowTpl, you can use all the placeholders provided by the sgLister snippet.</p>
<p class="text-bold"> If there are problems, I suggest first of all to update DocLister <a href="https://github.com/AgexNash" rel="nofollow" target="_blank">from the github</a>.</p>
