### Add Tags When Posting a Job

Apr 18, 2009

this tutorial is very similar to adding the salary range, except for some few differences. but here's how you can add tagging when posting jobs.


<img alt="tags-post" class="aligncenter size-full wp-image-185" height="334" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/tags-post.png" title="tags-post" width="443"/>

<img alt="tags-details" class="aligncenter size-full wp-image-187" height="350" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/tags-details.png" title="tags-details" width="419"/>

<img alt="tags-browser" class="aligncenter size-full wp-image-186" height="74" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/tags-browser.png" title="tags-browser" width="349"/>

<img alt="tags-loop" class="aligncenter size-full wp-image-188" height="136" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/tags-loop.png" title="tags-loop" width="386"/>



HERE IT GOES!

to begin, on your jobberbase “jobs” table, run this sql script

<code>
<pre lang="php">
ALTER TABLE `jobs` ADD `tags` VARCHAR( 255 ) NULL 
</pre>
</code>

in __/\_includes/translations.ini__, add anywhere

<code>
<pre lang="php">
[jobtags]
tags_with = "Jobs tagged with"
no_tags_with = "The are currently no jobs tagged with"
</pre>
</code>

in __/\_includes/translations.ini__, add under [publish]

<code>
<pre lang="php">
tags ="Tags"
tags_warning = "Separate each tags by comma or space"
</pre>
</code>

on /index.php, below:

<code>
<pre lang="php">
// per city
case 'jobs-in':
	require_once 'page_city.php';
	$flag = 1;
	break;
</pre>
</code>
	
add the following:

<code>
<pre lang="php">
// per tags
case 'job-tags':
	require_once 'page_tags.php';
	$flag = 1;
	break;
</pre>
</code>

open up __/page\_job.php__ add this on top, before "$job_flag = false;"

<code>
<pre lang="php">
$tags_array = array();
</pre>
</code>

and on the same page, before the line "$smarty-&gt;assign('job', $info);"

<code>
<pre lang="php">
// ######### get job's tags, if any #########
if (!empty($info['tags']))
{
	// filter string from other separator characters aside from space (" ")
	$spchars = array(",",".",";",":");
	$tags_string = str_replace($spchars," ", trim($info['tags']));
	
	// explode string to an array
	$tags_array = explode(" ", trim($tags_string));
	$smarty-&gt;assign('tags', $tags_array);
}
// ######### get job's tags, if any #########
</pre>
</code>
	
now create a new file on the root folder called __page\_tags.php__ and add the following code:


<pre lang="php">
<?php
	if ($extra == 'full-time')
	{
		$type_id = JOBTYPE_FULLTIME;
	}
	else if ($extra == 'part-time')
	{
		$type_id = JOBTYPE_PARTTIME;
	}
	else if ($extra == 'freelance')
	{
		$type_id = JOBTYPE_FREELANCE;
	}
	else
	{
		$type_id = false;
	}
	
	$tag_key = $id;
	
	if ($tag_key)
	{		
		$jobCount = $job->GetJobsCountForTag($tag_key);
		$smarty-&gt;assign('jobs_count', $jobCount);
	}
	else
	{
		redirect_to(BASE_URL . 'page-unavailable/');
		exit;
	}
	
	$paginatorLink = BASE_URL . "job-tags/$tag_key";
	
	if (isset($extra))
		$paginatorLink .= "/$extra";
		
	$paginator = new Paginator($jobCount, JOBS_PER_PAGE, @$_REQUEST['p']);
	$paginator-&gt;setLink($paginatorLink);
	$paginator-&gt;paginate();
	
	$firstLimit = $paginator-&gt;getFirstLimit();
	$lastLimit = $paginator-&gt;getLastLimit();
	
	$the_jobs = array();
	$the_jobs = $job-&gt;GetJobsByTagKey($id, $firstLimit, JOBS_PER_PAGE, 1);
	$smarty-&gt;assign("pages",$paginator-&gt;pages_link);

	$smarty-&gt;assign('jobs', $the_jobs);
	$smarty-&gt;assign('tag_key', $id);

	$smarty-&gt;assign('seo_title', 'Jobs tagged with "' . $id .'"');
	$smarty-&gt;assign('seo_desc', '');
	$smarty-&gt;assign('seo_keys', '');
	
	$template = 'tag-jobs-header.tpl';
?&gt;
</pre>



on __/\_templates/publish-write.tpl__, under

<code>
<pre lang="php">
<!-- #textile-suggestions -->


</pre>
</code>

add beneath it:

<code>
<pre lang="php">
<!-- Job Tags -->
<tr>
	<td class="publish-label" valign="top">{$translations.publish.tags}:</td>
	<td><input id="tags" name="tags" size="50" tabindex="7" type="text" value="{if $job.tags}{$job.tags}{else}{$smarty.post.tags}{/if}"/>
<br/>
<div class="suggestion">
	{$translations.publish.tags_warning}
</div>
	</td>
</tr>
<!-- /Job Tags -->
</pre>
</code>

on __/\_includes/class.Job.php__, add on the vars list

<code>
<pre lang="php">
var $mTags = false;
</pre>
</code>

under the first SELECT statement after this line:

<code>
<pre lang="php">
a.is_temp AS is_temp, a.is_active AS is_active, a.spotlight AS spotlight,
</pre>
</code>

add this line:

<code>
<pre lang="php">
a.tags as tags, 
</pre>
</code>

on the $this-&gt; list after the SELECT statement, add after "$this-&gt;mIsSpotlight = $row['spotlight'];"

<code>
<pre lang="php">
$this-&gt;mTags = $row['tags'];
</pre>
</code>

add on __public function GetInfo()__ and __public function GetBasicInfo()__ and __public function GetBasicInfoAdmin()__, before the line ‘is_spotlight’ =&gt; $this-&gt;mIsSpotlight);

<code>
<pre lang="php">
'tags' =&gt; $this-&gt;mTags,
</pre>
</code>

on public function Create($params), on the INSERT INTO jobs block, add after INSERT INTO jobs (type_id, category_id, title, description, company, city_id, url, apply,:

<code>
<pre lang="php">
tags, 
</pre>
</code>

and on the same block, add before the line NOW(), 1, 0, 0, “‘ . $this-&gt;GenerateAuthCode() . ‘”,

<code>
<pre lang="php">
"' . $params['tags'] . '",
</pre>
</code>

to allow editing, on public function Edit($params), on the UPDATE jobs SET block, add after __apply = "' . $params\['apply'\] . '",__:


and add at the bottom of the page before the last }

<code>
<pre lang="php">
//############## tagging ######################
// Get jobs count by tag
public function GetJobsCountForTag($tag_key)
{
	global $db;
	
	$sql = 'SELECT COUNT(id) AS total FROM jobs WHERE is_temp = 0 AND is_active = 1 AND tags LIKE "%' . $tag_key . '%"';

	$result = $db-&gt;query($sql);
	
	$row = $result-&gt;fetch_assoc();
	
	return $row['total'];
}

// Get all jobs by tag
public function GetJobsByTagKey($tag_key = false, $firstLimit = false, $lastLimit=false, $random)
{
	global $db;
	
	$jobs = array();
	
	if ($random == 1)
	{
		$order = ' ORDER BY RAND() ';
	}
	else
	{
		$order = ' ORDER BY created_on DESC ';
	}

	if ($firstLimit &gt;= 0 &amp;&amp; $lastLimit &gt;= 0)
	{
		$sql_limit = 'LIMIT ' . $firstLimit .', ' . $lastLimit;
	}
	else
	{
			$sql_limit = '';        
	}

	$sql = 'SELECT id FROM jobs 
			WHERE is_temp = 0 
			AND is_active = 1 
			AND tags LIKE "%' . $tag_key . '%"
			' . $order . ' ' . $sql_limit;
	
	$result = $db-&gt;query($sql);
	
	while ($row = $result-&gt;fetch_assoc())
	{
		$current_job = new Job($row['id']);
		$jobs[] = $current_job-&gt;GetInfo();
	}
	
	return $jobs;
}
//############## tagging ######################
</pre>
</code>

now on page_write.php on the root folder, find the blocks (there are 2) which starts with $data = array( and add the following before the line ‘apply_online’ =&gt; $apply_online);

<code>
<pre lang="php">
'tags' =&gt; $tags,
</pre>
</code>

then on __/\_includes/job-details.tpl__, before:

<code>
<pre lang="php">
<div id="job-description">
{$job.description} 
</div></pre>
</code>

add the following:

<code>
<pre lang="php">
<!-- Tagging -->
<p>
<span class="fading">{$translations.publish.tags}:</span>
{if count($tags) &gt; 0}
{foreach item=tag from=$tags}
	{if $tag != NULL}
	<a href="{$BASE_URL}job-tags/{$tag}/">{$tag}</a>
	{/if}
{/foreach}
{else}
	{$job.tags}
{/if}
</p>
<!-- Tagging -->
</pre>
</code>

on /_templates, create a file called __tag-jobs-header.tpl__ and add the following:

<code>
<pre lang="php">
{include file="header.tpl"}
<div id="content">
<div id="job-listings">
	{if $jobs}
	<div id="sort-by-type">
	{$translations.category.display_only}&nbsp; 
	<a href="{$BASE_URL}jobs-tag/{$tag_key}/full-time/" title="{$tag_key} full time"><img alt="{$tag_key} full time" src="{$BASE_URL}img/icon-fulltime.png"/></a>
	<a href="{$BASE_URL}jobs-tag/{$tag_key}/part-time/" title="{$tag_key} part time"><img alt="{$tag_key} part time" src="{$BASE_URL}img/icon-parttime.png"/></a>
	<a href="{$BASE_URL}jobs-tag/{$tag_key}/freelance/" title="{$tag_key} freelance"><img alt="{$tag_key} freelance" src="{$BASE_URL}img/icon-freelance.png"/></a>
	</div><!-- #sort-by-type -->
	{/if}
	<h2>
	{$translations.jobtags.tags_with} "{$tag_key}" ({$jobs_count})
	</h2>
	{include file="posts-loop-tags.tpl"}
	</div><!-- #job-listings -->
</div><!-- #content -->
{include file="footer.tpl"}
</pre>
</code>

create another file on /_templates/, create another file named __posts-loop-tags.tpl__
<code>
<pre lang="php">
{if $is_home == 1}
<div><a href="{$BASE_URL}">« home</a></div>
{/if}
{if $no_categ != 1}
<table cellspacing="0" class="job-posts" id="job-posts">
{if !$jobs}
<div id="no-ads">
	{$translations.jobtags.no_tags_with} "<strong>{$tag_key}</strong>".<br/>
	<a href="{$BASE_URL}post/" title="{$translations.footer.new_job_title}">{$translations.footer.new_job}</a>
</div><!-- #no-ads -->
{/if}
{foreach item=job from=$jobs}
	<tr>
	<td>
	{if $job.type_id == $smarty.const.JOBTYPE_FULLTIME}
	<img alt="full-time" src="{$BASE_URL}img/icon-fulltime.png"/>
	{elseif $job.type_id == $smarty.const.JOBTYPE_PARTTIME}
	<img alt="part-time" src="{$BASE_URL}img/icon-parttime.png"/>
	{elseif $job.type_id == $smarty.const.JOBTYPE_FREELANCE}
	<img alt="freelance" src="{$BASE_URL}img/icon-freelance.png"/>
	{/if}
	<a href="{$BASE_URL}job/{$job.id}/{$job.url_title}/" title="{$job.title}">{$job.title}</a> <span class="la">{$translations.homepage.at}</span> {$job.company}{if $job.location == 'Anywhere'}, {$job.location}{else} <span class="la">{$translations.homepage.in}</span> {$job.location}{/if}
	</td>
	<td class="time-posted"><img alt="" src="{$BASE_URL}img/clock.gif"/> {$job.created_on}</td>
	</tr>
{/foreach}
</table>
<br/>
{$pages}
{/if}
{literal}
<script type="text/javascript">
$(document).ready(function()
{
	$(".job-posts tr").mouseover(function() {$(this).addClass("over");}).mouseout(function() {$(this).removeClass("over");});
	$(".job-posts tr:odd").addClass("alt");
});
</script>
{/literal}
</pre>
</code>