### Category Jobs Filter

Jun 26, 2009

the jobs listed in the category can get long and sometimes irrelevant anymore to those looking for jobs, so to keep the list relevant, there is a way job seekers can filter the jobs in the category they are looking at. here is a sample screenshot:

<div align="center"><img alt="filter-jobs" class="aligncenter size-full wp-image-520" height="339" src="http://www.redjumpsuit.net/wp-content/uploads/2009/06/filter-jobs.png" title="filter-jobs" width="435"/>
</div>

  
  

and here is an instruction on how it can be done:

Open up /_includes/translations.ini and add this:

<pre lang="php">
[header]
filter_jobs = "Filter Jobs"

[filter]
day = "24 Hours"
week = "1 Week"
month = "1 Month"
</pre>

Open up /_templates/sidebar.tpl

Before the line:

<pre lang="php">
{if $CURRENT_PAGE != ''}
</pre>

Add this block:

<pre lang="php">
{if $current_category != 'home' &amp;&amp; $current_category != ''}
<h4>{$translations.header.filter_jobs}</h4>
<ul>
    <li>
		{if $filter != 'h'}
			<a href="{$BASE_URL}advanced/{$current_category}/h/">{$translations.filter.day}</a>
		{else}
			<strong><a href="#">{$translations.filter.day}</a></strong>
		{/if}
	</li>
    <li>
		{if $filter != 'w'}
			<a href="{$BASE_URL}advanced/{$current_category}/w/">{$translations.filter.week}</a>
		{else}
			<strong><a href="#">{$translations.filter.week}</a></strong>
		{/if}
	</li>
    <li>
		{if $filter != 'm'}
			<a href="{$BASE_URL}advanced/{$current_category}/m/">{$translations.filter.month}</a>
		{else}
			<strong><a href="#">{$translations.filter.month}</a></strong>
		{/if}
	</li>
</ul>
<br/>
{/if}
</pre>

Create a file on the root folder named 'page_advancedsearch.php' and paste this code:

<pre lang="php">
<?php

$list = new AdvancedSearch();
$the_jobs = array();

$the_jobs = $list->filterjobs($extra,50,$id);

$smarty-&gt;assign('jobs', $the_jobs);
$smarty-&gt;assign('current_category', $id);
$smarty-&gt;assign('filter', $extra);

$smarty-&gt;assign('seo_title', get_seo_title($id));
$smarty-&gt;assign('seo_desc', get_seo_desc($id));
$smarty-&gt;assign('seo_keys', get_seo_keys($id));

$template = 'category.tpl';
?&gt;
</pre>

Create a file on your /_includes folder called 'class.AdvancedSearch.php' and paste this code:

<pre lang="php">
<?php
/**
 * jobber job board platform
 *
 * @author    RedJumpsuit <myredjumpsuit@gmail.com>
 * @web        www.redjump.co.cc
 *
 * Advanced Search is used for more extensive searching
 */

class AdvancedSearch
{
    function __construct()
    { }

    public function filterjobs($filter,$per_page=50,$catname)
    {
        global $db;
        $jobs = array();

        $condition = '';

        // 1 day
        if ($filter == 'h')
        {
            $condition = ' AND a.created_on &gt;= DATE_SUB(NOW(),INTERVAL 1 DAY) ';
        }
        // 1 week
        elseif ($filter == 'w')
        {
            $condition = ' AND a.created_on &gt;= DATE_SUB(NOW(), INTERVAL 1 WEEK ) ';
        }
        // 1 month
        else
        {
            $condition = ' AND a.created_on &gt;= DATE_SUB(NOW(),INTERVAL 1 MONTH ) ';
        }

         $sql = 'SELECT a.*, b.name AS city_name, c.name AS category_name, REPLACE(LOWER(a.title)," ","-") AS url_title
                    FROM jobs a, cities b, categories c
                    WHERE a.city_id = b.id
                        AND a.category_id = c.id
                        AND c.var_name = "'. $catname .'"'. $condition . ' LIMIT 0, '. $per_page;

        $result = $db-&gt;query($sql);
        while ($row = $result-&gt;fetch_assoc())
        {
            $jobs[] = $row;
        }

        if ($jobs)
        {
            return $jobs;
        }

    }
}
?&gt;
</pre>

Second to the last, open your index.php and under this block:

<pre lang="php">
// per category
case 'jobs':
    require_once 'page_category.php';
    $flag = 1;
    break;
</pre>

Add this block

<pre lang="php">
// per filter
case 'advanced':
    require_once 'page_advancedsearch.php';
    $flag = 1;
    break;
</pre>

Finally, open up your config.php file and add this at the end of the require_once class list:

<pre lang="php">
// advanced search
require_once '_includes/class.AdvancedSearch.php';
</pre>