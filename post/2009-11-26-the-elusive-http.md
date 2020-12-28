### The elusive http://

Nov 26, 2009

some people in the forum recently started asking for a workaround on the website URL redirecting issue when the site provided in the job ad lacks the `` http:// `` (the site will think you are redirecting from a page within the site) by default jobberBase should be adding this at the beginning of the URL but sometimes it does and sometimes it doesn't (still have to sit down and re-check why this is happening.) and just merely appending `` http:// `` on the URL in the job-details.tpl file will not always work. here is a script i created that tightly checks the URL provided in the job ad, whether `` http:// `` should be added or not (hasn't failed me yet so far!)

open up page_job.php, after this line


<pre lang="php">
$smarty-&gt;assign('job', $info);
</pre>



add this block:


<pre lang="php">
if (substr($info['url'],0,7) == "http://" AND strpos($info['url'], "."))
{
	$smarty-&gt;assign('job_url', $info['url']);
}
elseif (substr($info['url'],0,7) != "http://" AND strpos($info['url'], "."))
{
	$smarty-&gt;assign('job_url', 'http://'. $info['url']);
}
</pre>



then open the file job-details.tpl from your templates folder and use this line to display the job poster's URL.


<pre lang="php">
{if $job_url != ''}<a href="{$job_url}" target="_blank">{$job.company}</a>{else}{$job.company}{/if}
</pre>



if you've tried this and encountered error, let me know. i would be interested to find out!
