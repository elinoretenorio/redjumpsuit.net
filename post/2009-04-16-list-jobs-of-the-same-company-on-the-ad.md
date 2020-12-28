### List Jobs of the Same Company on the Ad

Apr 16, 2009

<img alt="recentjobs" class="aligncenter size-full wp-image-148" height="608" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/recentjobs.png" title="recentjobs" width="528"/>

on /_templates/ folder, create a new file and name it "company-posts-loop.tpl", copy the code below and save it:

<code><pre lang="php">
<!-- Jobs from the same company -->
{if count($compjobs) &gt; 1}
<strong>{$translations.jobs.recent_jobs} {$job.company}</strong><br/><br/>
<table cellspacing="0" class="job-posts" id="job-posts">
{foreach item=compjob from=$compjobs}
{if $compjob.id != $job.id }
<tr>
	<td>
	{if $compjob.type_id == $smarty.const.JOBTYPE_FULLTIME}
	<img alt="full-time" src="{$BASE_URL}img/icon-fulltime.png"/>
	{elseif $compjob.type_id == $smarty.const.JOBTYPE_PARTTIME}
	<img alt="part-time" src="{$BASE_URL}img/icon-parttime.png"/>
	{elseif $compjob.type_id == $smarty.const.JOBTYPE_FREELANCE}
	<img alt="freelance" src="{$BASE_URL}img/icon-freelance.png"/>
	{/if}
	<a href="{$BASE_URL}job/{$compjob.id}/{$compjob.url_title}/" title="{$compjob.title}">{$compjob.title}</a> <span class="la">{$translations.homepage.at}</span> {$compjob.company}{if $compjob.location == 'Anywhere'}, {$compjob.location}{else} <span class="la">{$translations.homepage.in}</span> {$compjob.location}{/if}
	</td>
	<td class="time-posted"><img alt="" src="{$BASE_URL}img/clock.gif"/> {$compjob.created_on}</td>
</tr>
{/if}
{/foreach}
</table><br/>
<a href="{$BASE_URL}jobs-at/{$jobsat}/">{$translations.jobs.all_jobs}</a><strong> {$job.company}</strong>
<br/><br/>
{/if}
<!-- Jobs from the same company -->
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

on /_includes/translations.ini, under [jobs] add the following:

<code>
<pre lang="php">
recent_jobs = "Recent jobs at"
all_jobs = "View all jobs at"
</pre>
</code>

on /page_job.php, add the following code.

on the top, right after the php tags:

<code>
<pre lang="php">$sanitizer = new Sanitizer();</pre>
</code>

and before the line "$smarty-&gt;assign('job', $info);", add the following:

<code>
<pre lang="php">
// ######### list other jobs by the same company #########
$compjobs = $job-&gt;ApiGetJobsByCompany($info['company'], 5, false);
$sanitizedcomp = $sanitizer-&gt;sanitize_title_with_dashes($info['company']);
$smarty-&gt;assign('compjobs', $compjobs);
$smarty-&gt;assign('jobsat', $sanitizedcomp);
// ######### list other jobs by the same company #########</pre>
</code>

this will display the 5 most recent jobs by the same company. the 2nd param [limit] of the function "ApiGetJobsByCompany([company], [limit], [for feed])" will determine the maximum jobs that will be displayed from the same company. Also, this will not list the current job that is currently being displayed.

and lastly, open up /_templates/job-details.tpl and add the new template under the job description portion.

<code>
<pre lang="php">
<div id="job-description">
{$job.description}</div>
{include file="company-posts-loop.tpl"}
</pre>
</code>