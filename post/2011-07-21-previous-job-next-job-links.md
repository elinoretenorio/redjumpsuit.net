### Previous Job, Next Job links

Jul 21, 2011

this is for __jobberBase 1.9.1__

in this tutorial, i will show how you can add a Previous Job and Next Job links on your job posts, for easy navigation through your job site.

here are screenshots:

__first job post__

<img alt="first" class="aligncenter size-full wp-image-1726" height="255" src="http://www.redjumpsuit.net/wp-content/uploads/2011/07/first.png" title="first" width="425"/>

__in between jobs__

<img alt="active" class="aligncenter size-full wp-image-1725" height="239" src="http://www.redjumpsuit.net/wp-content/uploads/2011/07/active.png" title="active" width="433"/>

__last job post__

<img alt="last" class="aligncenter size-full wp-image-1724" height="221" src="http://www.redjumpsuit.net/wp-content/uploads/2011/07/last.png" title="last" width="437"/>



Steps:

1) open /_includes/class.Job.php and add this block on the Job class


<pre lang="php">
// get next job
public function GetNextJob($category_id=false)
{
	global $db;
	$condition = '';
	$sanitizer = new Sanitizer;
	
	if ($category_id) {
		$condition .= ' AND category_id = '. $this-&gt;mCategoryId;
	}
	
	$sql = 'SELECT id AS next_id, title AS title, company AS company FROM '.DB_PREFIX.'jobs WHERE 1 '. $condition .' AND is_active = 1 AND id &gt; '. $this-&gt;mId .' ORDER BY id ASC LIMIT 1';
	$result = $db-&gt;query($sql);
	$row = $result-&gt;fetch_assoc();
	if (!empty($row['next_id'])) {
		return $row['next_id'] .'/'. $sanitizer-&gt;sanitize_title_with_dashes($row['title'] . ' at ' . $row['company']) .'/';
	} 
}

// get previous job
public function GetPrevJob($category_id=false)
{
	global $db;
	$condition = '';
	$sanitizer = new Sanitizer;
	
	if ($category_id) {
		$condition .= ' AND category_id = '. $this-&gt;mCategoryId;
	}
	
	$sql = 'SELECT id AS prev_id, title AS title, company AS company FROM '.DB_PREFIX.'jobs WHERE 1 '. $condition .' AND is_active = 1 AND id &lt; '. $this-&gt;mId .' ORDER BY id DESC LIMIT 1';
	$result = $db-&gt;query($sql);
	$row = $result-&gt;fetch_assoc();
	if (!empty($row['prev_id'])) {
		return $row['prev_id'] .'/'. $sanitizer-&gt;sanitize_title_with_dashes($row['title'] . ' at ' . $row['company']) .'/';
	}
}
</pre>



2) open /_templates/default/_translations/translations_en.ini and add this on the [jobs] section


<pre lang="php">
prev = "&lt;&lt; Previous Job"
next = "Next Job &gt;&gt;"
divider = "or"
</pre>



3) open page_job.php and after this block:


<pre lang="php">
// ######### list other jobs by the same company #########
$compjobs = $job-&gt;ApiGetJobsByCompany($info['company'], 5, false);
$sanitizedcomp = $sanitizer-&gt;sanitize_title_with_dashes($info['company']);
$smarty-&gt;assign('compjobs', $compjobs);
$smarty-&gt;assign('jobsat', $sanitizedcomp);
// ######### list other jobs by the same company #########
</pre>



add this block:


<pre lang="php">
// get next and previous jobs
$prev = $job-&gt;GetPrevJob(true); // set to true if previous job of the same category or leave blank
$next = $job-&gt;GetNextJob(true); // set to true if next job of the same category or leave blank

if (!empty($prev)) {
	$smarty-&gt;assign('prev', BASE_URL . URL_JOB . '/' . $prev);
}
if (!empty($next)) {
	$smarty-&gt;assign('next', BASE_URL . URL_JOB . '/' . $next);
}
</pre>



4) finally, open /_templates/default/job.tpl and add the code below wherever you want the __&lt;&lt; Previous Job or Next Job &gt;&gt;__ links to appear.


<pre lang="php">
{if $prev}<a href="{$prev}">{$translations.jobs.prev}</a>{else}{$translations.jobs.prev}{/if} {$translations.jobs.divider} {if $next}<a href="{$next}">{$translations.jobs.next}</a>{else}{$translations.jobs.next}{/if}
</pre>



that should be all. 

please consider donating if you like this tutorial or used it on your website ;)
