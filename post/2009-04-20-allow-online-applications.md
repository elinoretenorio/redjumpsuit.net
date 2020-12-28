### Allow Online Applications

Apr 20, 2009

in case you didn't know, jobberBase actually already has the provision for whether or not to allow online applications. that's why this mod was a lot easier to work on compared to starting from scratch. anyway, without further ado...

<img alt="apply-post" class="aligncenter size-full wp-image-197" height="399" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/apply-post.png" title="apply-post" width="390"/>

<img alt="apply-details" class="aligncenter size-full wp-image-198" height="338" src="http://www.redjumpsuit.net/wp-content/uploads/2009/04/apply-details.png" title="apply-details" width="360"/>

HERE IT IS!

open up /_templates/publish-write.tpl and look for the block

<code>
<pre lang="php">
<fieldset class="hidden">
	<input checked="checked" id="apply_online" name="apply_online" type="checkbox"/>
	<input $job.id="" $smarty.session.later_edit}value="edit" if}="" name="action" type="hidden" {="" {else}value="publish" {if="" ||=""/>
	{if $smarty.session.later_edit}<input name="auth" type="hidden" value="{$smarty.session.later_edit}"/>{/if}
	{if $job.id}<input name="job_id" type="hidden" value="{$job.id}"/>{/if}
</fieldset>
</pre>
</code>

from this block, cut this line:

<code>
<pre lang="php">
<input checked="checked" id="apply_online" name="apply_online" type="checkbox"/>
</pre>
</code>

still on the publish-write.tpl file, above the line:

<code>
<pre lang="php">
<fieldset><input id="submit" name="submit" type="submit" value="{$translations.publish.step1_submit}"/></fieldset>
</pre>
</code>

add this:

<code>
<pre lang="php">
<fieldset><input !isset($job.apply_online)}checked="checked" $job.apply_online='1}checked="checked"{/if}{if' id="apply_online" if}="" name="apply_online" type="checkbox" {="" {if=""/> <strong>Allow online applications</strong></fieldset>
</pre>
</code>

now open the file on the root folder called page_write.php, look for the following blocks (there are 2 instances)

<code>
<pre lang="php">
if ($poster_email == '')
{
	$errors['poster_email'] = $translations['jobs']['email_error'];
}
</pre>
</code>

below the 2 instances of this block, add the following:

<code>
<pre lang="php">
if (isset($apply_online) &amp;&amp; $apply_online == 'on')
{
	$apply_online = 1;
}
else
{
	$apply_online = 0;
}
</pre>
</code>

and the last file to modify is /_includes/class.Job.php.

look for the __public function Create($params)__ and __public function Edit($params)__ and in these functions, update the line

<code>
<pre lang="php">
if ($params['apply_online'] == 'on')
</pre>
</code>

to

<code>
<pre lang="php">
if ($params['apply_online'] == 1)
</pre>
</code>

now every time someone posts a job, the poster will have an option whether to allow online applications or not.

a future enhancement to this mod can be, when the job poster unchecks the "Allow online applications", it will give an option (probably a text box) with a "How to apply" instructions.

__UPDATE!!!__

some users reported as having issues with this hack when the ad gets edited and the check doesn't retain. so here's a small fix:

1) add this block on page_write.php

<pre lang="php">
if (isset($apply_online))
{
     $smarty-&gt;assign('is_apply', $apply_online);
}
</pre>

before the last line

<pre lang="php">
$template = 'publish-write.tpl';
</pre>

2) then open the template file /_templates/publish-write.tpl and change this line:

<pre lang="php">
<input !isset($job.apply_online)}checked="checked" $job.apply_online='1}checked="checked"{/if}{if' id="apply_online" if}="" name="apply_online" type="checkbox" {="" {if=""/>
</pre>

to this line

<pre lang="php">
<input !isset($is_apply)}checked="checked" !isset($job.apply_online)="" $is_apply='1}checked="checked"{/if}{if' $job.apply_online="1" &&="" id="apply_online" if}="" name="apply_online" type="checkbox" {="" {if="" ||=""/><strong>{$translations.publish.apply_online}</strong>
</pre>