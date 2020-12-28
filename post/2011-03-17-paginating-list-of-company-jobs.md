### Paginating List of Company Jobs

Mar 17, 2011

__For version: 1.9.1__

if you have a fairly large database of jobs, and you have companies that have over thousands of jobs, more or less you will probably be getting "Out of memory" error when trying to list all the jobs of these companies. 

Error looks like this:


<pre lang="php">
Fatal error: Out of memory (allocated 66584576) (tried to allocate 830 bytes) in /public_html/_includes/class.Job.php on line 58
</pre>



The fix is to paginate the list of jobs instead of listing all of the jobs all the same time.

Here's the steps:

1) Create a backup of page_company.php
2) Open page_company.php and replace all the code with this:


<pre lang="php">
<?php
	
	$sanitizer = new Sanitizer();
	
	$sql = 'SELECT DISTINCT company FROM '.DB_PREFIX.'jobs';
	$comps = $db->QueryArray($sql);
	
	foreach ($comps as $comp)
	{
		if ($sanitizer-&gt;sanitize_title_with_dashes($comp['company']) == $id)
		{
			$company = $comp['company'];
			break;
		}
	}

	$jobsCount =  $job-&gt;CountJobsOfCompany($company);
	$paginatorLink = BASE_URL . URL_JOBS_AT_COMPANY . "/$id/";
		
	$paginator = new Paginator($jobsCount, JOBS_PER_PAGE, @$_REQUEST['p']);
	$paginator-&gt;setLink($paginatorLink);
	$paginator-&gt;paginate();
	
	$firstLimit = $paginator-&gt;getFirstLimit();
	$lastLimit = $paginator-&gt;getLastLimit();
	
	$smarty-&gt;assign('jobs', $job-&gt;ApiGetJobsByCompany($company, false, false, $firstLimit, JOBS_PER_PAGE));
	$smarty-&gt;assign('current_company', $company);
	$smarty-&gt;assign('pages', $paginator-&gt;pages_link);

	$html_title = $translations['companies']['are_you_looking'] . ' ' . $company . '?';
	$meta_description = $translations['companies']['meta_part1'] . ' ' . $company . '! ' . $translations['companies']['meta_part2'];
	$template = 'company.tpl';
?&gt;
</pre>



3) Open /_includes/class.Job.php and look for the block:


<pre lang="php">
public function ApiGetJobs($type_id = false, $categ_id = false, $limit = false, $random, $days_behind, $for_feed = false, $city_id = false)
	{ ... }
</pre>



add this block after:


<pre lang="php">
public function CountJobsOfCompany($company)
	{
		global $db;
		
		$sql = 'SELECT COUNT(id) AS total FROM '.DB_PREFIX.'jobs WHERE company LIKE "'. $db-&gt;real_escape_string($company) .'"';
		
		$result = $db-&gt;query($sql);
		$row = $result-&gt;fetch_assoc();
		return $row['total'];
	}
</pre>



4) Look for this block (should be below the block you just added):


<pre lang="php">
// Get all jobs published by a company
	public function ApiGetJobsByCompany($company = false, $limit = false, $for_feed = false)
	{ ... }
</pre>



and replace it with this:


<pre lang="php">
// Get all jobs published by a company
	public function ApiGetJobsByCompany($company = false, $limit = false, $for_feed = false, $startIndex = false, $numberOfJobsToGet = false)
	{
		global $db;
		
		$jobs = array();
		$conditions = '';
		
		if ($company)
		{
			$conditions .= ' AND company LIKE "%' . trim($db-&gt;real_escape_string($company)) . '%"';
		}
		
		if ($for_feed)
		{
			// job was posted more than 10 minutes ago
			$conditions .= ' AND DATE_SUB(NOW(), INTERVAL 10 MINUTE) &gt; created_on';
		}
	
		if($numberOfJobsToGet &gt; 0)
			$sql_limit = ' ORDER BY created_on DESC limit ' . $startIndex . ',' . $numberOfJobsToGet;
		else
			$sql_limit = '';
		
		$sql = 'SELECT id
		               FROM '.DB_PREFIX.'jobs
		               WHERE 1 ' . $conditions . ' AND is_temp = 0 AND is_active = 1 ' . $sql_limit;
		$result = $db-&gt;query($sql);
		while ($row = $result-&gt;fetch_assoc())
		{
			$current_job = new Job($row['id']);
			$job = $current_job-&gt;GetInfo();
			unset($job['poster_email']);
			unset($job['auth']);
			$jobs[] = $job;
		}
		return $jobs;
	}
</pre>



So when listing jobs of a certain company with a few thousand postings, it will now paginate instead of listing all of the company's jobs causing your page to throw "Out-of-memory" error.

please consider donating if you like this tutorial or used it on your website ;)
