### Show recent job applications

Apr 05, 2012

for: jobberBase 1.9.1

this tutorial will show you how to display most recent applied to jobs (as opposed to most applied to jobs.) it will display jobs with the most recent applications (but not necessarily the most popular job)


<img alt="recent-applied" class="aligncenter size-full wp-image-1755" height="288" src="http://www.redjumpsuit.net/wp-content/uploads/2012/04/recent-applied.png" title="recent-applied" width="539"/>



open page_home.php and add this on the list of the define block:


<pre lang="php">
define('NUMBER_OF_RECENT_APPLIED_TO_JOBS_TO_GET', $settings['most_applied_jobs']);
</pre>



on the same page, after this line:


<pre lang="php">
$smarty-&gt;assign('most_applied_to_jobs', $job-&gt;GetMostAppliedToJobs(NUMBER_OF_MOST_APPLIED_TO_JOBS_TO_GET));
</pre>



add this:


<pre lang="php">
$smarty-&gt;assign('recent_applied_to_jobs', $job-&gt;GetRecentAppliedJobs(NUMBER_OF_RECENT_APPLIED_TO_JOBS_TO_GET));
</pre>



next, open /_includes/class.Job.php and look for this block:


<pre lang="php">
public function GetMostAppliedToJobs($limit = false)
	{
}
</pre>



add this after:


<pre lang="php">
public function GetRecentAppliedJobs($limit = false)
	{
		global $db;
		
		$jobs = array();
		
		$sql_limit = 'LIMIT ' . $limit;
		
		$sql = 'SELECT job_id AS job_id, DATE_FORMAT(created_on, "' . DATE_FORMAT . '") AS applied_on FROM  '.DB_PREFIX.'job_applications 
				ORDER BY created_on DESC ' . $sql_limit;
		$result = $db-&gt;query($sql);
		while ($row = $result-&gt;fetch_assoc())
		{
			$current_job = new Job($row['job_id']);
			$job = $current_job-&gt;GetInfo();
			$job['applied_on'] = $row['applied_on'];
			$jobs[] = $job;
		}
		return $jobs;
	}
</pre>



third, open /_templates/default/home.tpl and after this block:


<pre lang="php">
{if $most_applied_to_jobs}
	<br/>
	<h2>{$translations.homepage.popular_jobs}</h2>
	
	{foreach item=job from=$most_applied_to_jobs}
		<div class="{cycle values='row,row-alt'}">
			<span class="row-info">
				<img alt="{$job.type_name}" src="{$BASE_URL}_templates/{$THEME}/img/icon-{$job.type_var_name}.png"/>
				<a href="{$BASE_URL}{$URL_JOB}/{$job.id}/{$job.url_title}/" title="{$job.title}">{$job.title}</a> <span class="la">{$translations.homepage.at}</span> {$job.company}{if $job.is_location_anywhere}, {$translations.jobs.location_anywhere}{else} <span class="la">{$translations.homepage.in}</span> {$job.location}{/if}
			</span>
			<span class="time-posted"><strong>{$job.apps}</strong> {$translations.homepage.applicants}</span>			
		</div>
	{/foreach}
{/if}
</pre>



add this block:


<pre lang="php">
{if $recent_applied_to_jobs}
	<br/>
	<h2>{$translations.homepage.applied_jobs}</h2>
	{foreach item=job from=$recent_applied_to_jobs}
		<div class="{cycle values='row,row-alt'}">
			<span class="row-info">
				<img alt="{$job.type_name}" src="{$BASE_URL}_templates/{$THEME}/img/icon-{$job.type_var_name}.png"/>
				<a href="{$BASE_URL}{$URL_JOB}/{$job.id}/{$job.url_title}/" title="{$job.title}">{$job.title}</a> <span class="la">{$translations.homepage.at}</span> {$job.company}{if $job.is_location_anywhere}, {$translations.jobs.location_anywhere}{else} <span class="la">{$translations.homepage.in}</span> {$job.location}{/if}
			</span>
			<span class="time-posted"><img alt="" src="{$BASE_URL}_templates/{$THEME}/img/clock.gif"/> {$job.applied_on}</span>			
		</div>
	{/foreach}
{/if}
</pre>



also replace this:


<pre lang="php">
{if !$latest_jobs &amp;&amp; !$most_applied_to_jobs}
</pre>



with this:


<pre lang="php">
{if !$latest_jobs &amp;&amp; !$most_applied_to_jobs &amp;&amp; !$recent_applied_to_jobs}
</pre>



lastly, open /_templates/default/_translations/translations_en.ini and add this under [homepage]:


<pre lang="php">
applied_jobs = "Recent applied to IT jobs"
</pre>



that should be it. enjoy!
