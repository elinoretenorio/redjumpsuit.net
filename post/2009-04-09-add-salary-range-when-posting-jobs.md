### Add Salary Range When Posting Jobs

Apr 09, 2009

<p style="text-align: center;"><img alt="job-post" class="aligncenter size-full wp-image-118" height="267" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/job-post.png" title="job-post" width="416"/></p>

<p style="text-align: center;"><img alt="job-details" class="aligncenter size-full wp-image-119" height="266" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/job-details.png" title="job-details" width="428"/></p>

LET'S BEGIN!

on your jobberbase "jobs" table, run this sql script

<pre lang="php">ALTER TABLE `jobs` ADD `salaryfrom` INT( 4 ) NULL ,
ADD `salaryto` INT( 4 ) NULL ,
ADD `salaryper` VARCHAR( 10 ) NULL</pre>

in /_includes/translations.ini, under [publish] add

<pre lang="php">salary_range = "Salary Range"
currency = "USD"
currency_warning = "Please use numbers only"</pre>

on /_templates/publish-write.tpl, under

<pre lang="html">
<!-- #textile-suggestions -->

</pre>

add beneath it:

<pre lang="php">
<!-- Salary Range -->
<tr>
	<td class="publish-label" valign="top">{$translations.publish.salary_range}:</td>
	<td>{$translations.publish.currency} <input id="salaryfrom" maxlength="10" name="salaryfrom" size="5" tabindex="5" type="text" value="{if $job.salaryfrom}{$job.salaryfrom}{else}{$smarty.post.salaryfrom}{/if}"/> to
	<input id="salaryto" maxlength="10" name="salaryto" size="5" tabindex="6" type="text" value="{if $job.salaryto}{$job.salaryto}{else}{$smarty.post.salaryto}{/if}"/> per
	<select name="salaryper" tabindex="7">
		<option value=""></option>
		<option $job.salaryper="Hour" $smarty.post.salaryper="Hour" if}="" selected{="" value="Hour" {if="" ||="" }="">Hour</option>
		<option $job.salaryper="Week" $smarty.post.salaryper="Week" if}="" selected{="" value="Week" {if="" ||="" }="">Week</option>
		<option $job.salaryper="Month" $smarty.post.salaryper="Month" if}="" selected{="" value="Month" {if="" ||="" }="">Month</option>
		<option $job.salaryper="Year" $smarty.post.salaryper="Year" if}="" selected{="" value="Year" {if="" ||="" }="">Year</option>
	</select><br/>
	<div class="suggestion">
	{$translations.publish.currency_warning}
	</div>
	</td>
</tr>
<!-- /Salary Range -->

</pre>

on /_includes/class.Job.php, add on the vars list

<pre lang="php">var $mSalaryFrom = false;
var $mSalaryTo = false;
var $mSalaryPer = false;</pre>

under the first SELECT statement after this line:

<pre lang="php">a.is_temp AS is_temp, a.is_active AS is_active, a.spotlight AS spotlight,</pre>

add this line:

<pre lang="php"> a.salaryfrom AS salaryfrom, a.salaryto AS salaryto, a.salaryper AS salaryper,</pre>

on the __$this-&gt;__ list after the SELECT statement, add after __$this-&gt;mIsSpotlight = $row\['spotlight'\];__

<pre lang="php">$this-&gt;mSalaryFrom = $row['salaryfrom'];
$this-&gt;mSalaryTo = $row['salaryto'];
$this-&gt;mSalaryPer = $row['salaryper'];</pre>

add on __public function GetInfo()__ and __public function GetBasicInfo()__ and __public function GetBasicInfoAdmin()__, before the line __'is\_spotlight' =&gt; $this-&gt;mIsSpotlight);__

<pre lang="php">'salaryfrom' =&gt; $this-&gt;mSalaryFrom,
'salaryto' =&gt; $this-&gt;mSalaryTo,
'salaryper' =&gt; $this-&gt;mSalaryPer,</pre>

on __public function Create($params)__, before the __INSERT INTO jobs__ block:

<pre lang="php">
if (($params['salaryfrom']) == '')
{
	$params['salaryfrom'] = 0;
}

if (($params['salaryto']) == '')
{
	$params['salaryto'] = 0;
}

if (($params['salaryper']) == '')
{
	$params['salaryper'] = "Hour";
}
</pre>

then add after __INSERT INTO jobs (type\_id, category\_id, title, description, company, city\_id, url, apply,__:

<pre lang="php">
salaryfrom, salaryto, salaryper,
</pre>

and on the same block, add before the line __NOW(), 1, 0, 0, "' . $this-&gt;GenerateAuthCode() . '",__

<pre lang="php">' . $params['salaryfrom'] . ',
' . $params['salaryto'] . ',
"' . $params['salaryper'] . '",</pre>

to allow editing, on __public function Edit($params)__, before the __UPDATE jobs SET__ block:

<pre lang="php">
if (($params['salaryfrom']) == '')
{
	$params['salaryfrom'] = 0;
}

if (($params['salaryto']) == '')
{
	$params['salaryto'] = 0;
}

if (($params['salaryper']) == '')
{
	$params['salaryper'] = "Hour";
}
</pre>

then add after __UPDATE jobs SET__ after the __apply = "' . $params\['apply'\] . '",__:

<pre lang="php">
salaryfrom = ' . $params['salaryfrom'] . ',
salaryto = ' . $params['salaryto'] . ',
salaryper = "' . $params['salaryper'] . '",
</pre>

now on __page\_write.php__ on the root folder, find the blocks (there are 2) which starts with __$data = array(__ and add the following before the line __'apply\_online' =&gt; $apply\_online);__

<pre lang="php">'salaryfrom' =&gt; $salaryfrom,
'salaryto' =&gt; $salaryto,
'salaryper' =&gt; $salaryper,</pre>

second to the last, on /_templates/job-details.tpl, after the line __{$job.description}__, under it add:

<pre lang="php">
{if ($job.salaryfrom != "" &amp;&amp; $job.salaryto != "") &amp;&amp; ($job.salaryfrom &gt; 0 &amp;&amp; $job.salaryto &gt; 0)}
<p><br/>
{$translations.publish.salary_range} <strong>{$translations.publish.currency} {$job.salaryfrom}</strong> to <strong>{$job.salaryto}</strong> per <strong>{$job.salaryper}</strong>
</p>
{/if}
</pre>

I added the if statement so that it wouldn't display when the salary range is not indicated.

And finally, if you need help adding this to your jobberBase website, feel free to contact me!

UPDATE!!!
To enable editing job ads on your admin panel, see this follow-up tutorial:

<http://www.redjumpsuit.net/2009/06/05/editing-salary-range-in-admin/>