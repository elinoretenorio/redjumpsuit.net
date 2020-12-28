### Send Photo on Apply Now

Oct 21, 2009

i have seen this request come up in the jobberBase forum here and there, so today i had the time to put together a how-to which will allow the applicants to send a photo of themselves when they send their resume on the job they are applying to.

so here it goes...

1) On /_includes/translations.ini, add under [apply]


<pre lang="php">
photo_label = "Photo"
photo_error = "Photo size exceeds limit."
photo_info = "Accepts only *.jpg, *.gif and *.png"
</pre>



2) /_templates/default/job-details.tpl, under this block:


<pre lang="php">
<tr>
	<td valign="top"><label for="apply_cv">{$translations.apply.cv_label}:</label></td>
	<td>
		<input id="apply_cv" name="apply_cv" type="file"/>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_cv}<img alt="" src="{$BASE_URL}_templates/{$THEME}/img/icon-delete.png" title="{$smarty.session.apply_errors.apply_cv}"/>{/if}</span>	
		<div class="suggestion">{$translations.apply.cv_info}</div>
	</td>
</tr>
</pre>



add this block:


<pre lang="php">
<tr>
	<td valign="top"><label for="apply_photo">{$translations.apply.photo_label}:</label></td>
	<td>
		<input id="apply_photo" name="apply_photo" type="file"/>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_photo}<img alt="" src="{$BASE_URL}_templates/{$THEME}/img/icon-delete.png" title="{$smarty.session.apply_errors.apply_photo}"/>{/if}</span>	
		<div class="suggestion">{$translations.apply.photo_info}</div>
	</td>
</tr>
</pre>



3) on page_apply.php, under this block:


<pre lang="php">
if ($_FILES['apply_cv'] &amp;&amp; $_FILES['apply_cv']['size'] &gt;= MAX_CV_SIZE)
	{
		$errors['apply_cv'] = $translations['apply']['cv_error'];
	}
</pre>



add this block:


<pre lang="php">
if ($_FILES['apply_photo']['name'] != "" &amp;&amp; $_FILES['apply_photo']['size'] &gt;= MAX_CV_SIZE)
	{
		$errors['apply_photo'] = $translations['apply']['photo_error'];
	}
if ($_FILES['apply_photo']['name'] != "" &amp;&amp; (($_FILES['apply_photo']['type'] != "image/gif") AND ($_FILES['apply_photo']['type'] != "image/jpeg") AND ($_FILES['apply_photo']['type'] != "image/png")))
	{
		$errors['apply_photo'] = $translations['apply']['photo_error'];
	}
</pre>



next, under this block:


<pre lang="php">
$filename = time() . '_' . $_FILES['apply_cv']['name'];
if (move_uploaded_file($_FILES['apply_cv']['tmp_name'], FILE_UPLOAD_DIR . $filename))
{
	$attachment = $filename;
}
else
{
	$attachment = '';
}
</pre>



add this block:


<pre lang="php">
$phfilename = time() . '_' . $_FILES['apply_photo']['name'];
if (move_uploaded_file($_FILES['apply_photo']['tmp_name'], FILE_UPLOAD_DIR . $phfilename))
{
	$phattachment = $phfilename;
}
else
{
	$phattachment = '';
}
</pre>



after that, find this block:


<pre lang="php">
$data = array('apply_email' =&gt; $apply_email,
		'apply_name' =&gt; $apply_name,
		'apply_msg' =&gt; strip_tags($apply_msg),
		'company_email' =&gt; $j-&gt;mPosterEmail,
		'company_name' =&gt; $j-&gt;mCompany,
		'job_title' =&gt; $j-&gt;mTitle,
		'attachment_path' =&gt; APP_PATH . FILE_UPLOAD_DIR . $attachment,
		'attachment_filename' =&gt; $attachment,
		'job_id' =&gt; $job_id);
</pre>



and update with this block:


<pre lang="php">
$data = array('apply_email' =&gt; $apply_email,
		'apply_name' =&gt; $apply_name,
		'apply_msg' =&gt; strip_tags($apply_msg),
		'company_email' =&gt; $j-&gt;mPosterEmail,
		'company_name' =&gt; $j-&gt;mCompany,
		'job_title' =&gt; $j-&gt;mTitle,
		'attachment_path' =&gt; APP_PATH . FILE_UPLOAD_DIR . $attachment,
		'attachment_filename' =&gt; $attachment,
		'phattachment_path' =&gt; APP_PATH . FILE_UPLOAD_DIR . $phattachment,
		'phattachment_filename' =&gt; $phattachment,
		'job_id' =&gt; $job_id);
</pre>



lastly on that same file, find this block:


<pre lang="php">
// delete uploaded file (cleanup)
if ($attachment != '')
{
	unlink(APP_PATH . FILE_UPLOAD_DIR . $attachment);
}
</pre>



add this block under:


<pre lang="php">
// delete uploaded photo (cleanup)
if ($phattachment != '')
{
	unlink(APP_PATH . FILE_UPLOAD_DIR . $phattachment);
}
</pre>



4) open up /_includes/class.Postman.php and look for this function:


<pre lang="php">
public function MailApplyOnline()
{
...
}
</pre>



inside this function, look for this block:


<pre lang="php">
if ($data['attachment_filename'] != '')
{
    $mailer-&gt;AddAttachment($data['attachment_path'], $data['attachment_filename']);
}
</pre>



and add this below it:


<pre lang="php">
if ($data['phattachment_filename'] != '')
{
    $mailer-&gt;AddAttachment($data['phattachment_path'], $data['phattachment_filename']);
}
</pre>



just a note that i wrote this on top of my head (from what i remember i did for several other projects i did before) without testing/running the code, so if you come across any bugs, please let me know and if you found a fix, kindly share them here :)
