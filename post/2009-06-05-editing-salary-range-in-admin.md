### Editing Salary Range in Admin

Jun 05, 2009

this is a follow-up post to the original Salary Range mod that a wrote some time back. if this is your first time to read about the Salary Range mod, please see the original post:

<http://www.redjumpsuit.net/2009/04/09/add-salary-range-when-posting-jobs/>

tonight i had the time to revisit the code and saw that you can't actually edit the ads without error from the admin panel. that's because the new variables were not added on the admin template and declared on the php file.

so anyway, here is the continuation of that original mod. this should be pretty easy if you've already followed the tutorial. with this you should be able to update the ad from the admin with the salary range mod without errors.

open /admin/page_edit_post.php, and before the lines

<pre lang="php">
$jobToEdit['textiledDescription'] = $textile-&gt;TextileThis($_POST['description']);
$jobToEdit['location_outside_ro'] = $jobToEdit['location_outside_ro_where'];
</pre>

add the following:

<pre lang="php">
$jobToEdit['salaryfrom'] = $_POST['salaryfrom'];
$jobToEdit['salaryto'] = $_POST['salaryto'];
$jobToEdit['salaryper'] = $_POST['salaryper'];
</pre>

and after this block

<pre lang="php">
$data = array('company' =&gt; $company,
	  'url' =&gt; $url,
	  'title' =&gt; $title,
	  'city_id' =&gt; $city_id,
	  'category_id' =&gt; $category_id,
	  'type_id' =&gt; $type_id,
	  'description' =&gt; $description,
	  'location_outside_ro_where' =&gt; ($isCitySelected ? '' : $location_outside_ro_where) ,
	  'apply' =&gt; '',
	  'poster_email' =&gt; $poster_email,
</pre>

add the following

<pre lang="php">
          'salaryfrom' =&gt; $salaryfrom,
	  'salaryto' =&gt; $salaryto,
	  'salaryper' =&gt; $salaryper,
</pre>

then open up /admin/_templates/edit-post.tpl and under these lines

<pre lang="php">
			
		<!-- #textile-suggestions -->
	

</pre>

add the following

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

this should take care of editing the job ad on your admin page.