### Display Total # of Applications, Jobs and Companies

Jan 29, 2009

this is a merged script for displaying total number of applications sent through the site, how many active jobs and how many different companies are on your site.

so let's start. on page_home.php, add:

<pre lang="php">
// get companies
$sqlc = 'SELECT DISTINCT company FROM jobs WHERE is_temp = 0 AND is_active = 1 ORDER BY company ASC';
$resultc = $db-&gt;QueryArray($sqlc);
$smarty-&gt;assign('companies_count', count($resultc));

// get total applications
$sqlja = 'SELECT COUNT(id) FROM job_applications';
$rsja = $db-&gt;QueryItem($sqlja);
$smarty-&gt;assign('total_applications', $rsja)
</pre>

and on /_templates/home.tpl, add

<pre lang="php">
<center><h4>now serving <b>{$jobs_count_all|number_format:0:"":","}</b> jobs from <b>{$companies_count|number_format:0:"":","}</b> different companies with a total of <b>{$total_applications|number_format:0:"":","}</b> job applications processed</h4></center>
</pre>