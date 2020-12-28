### Turn "Apply" to "Comment"

Feb 24, 2009

Not everyone actually wants to use Jobberbase's "Apply" function on the job ads so instead of disabling it, why don't turn "Apply" to "Comment" function.

This short/not-so-short guide will take you through turning the "Apply" function to "Comments."

Ok, first things first, we'll utilize the "__job\_applications__" table that already exists (be sure to backup your db before trying this so you'll have a fall-back.)

So here it goes...

1. Backup your db

2. Add the following fields to your "job_applications" table:

<pre lang="php">
`apply_name` varchar(100) NOT NULL,
`apply_email` varchar(150) NOT NULL,
`apply_location` varchar(150) NOT NULL,
`apply_url` varchar(150) DEFAULT NULL,
`apply_msg` text,
`is_active` tinyint(4) NOT NULL,
</pre>

3. Open /page_apply.php, and comment the lines shown as commented here and replace where it says replace:

Replace starting Line 8 with this until you find this line "if (empty($errors))"

<pre lang="php">
// #1 Replace only
// validation
if ($apply_name == '')
	{ $errors['apply_name'] = $translations['comment']['apply_name_error']; }
if ($apply_email == '')
	{ $errors['apply_email'] = $translations['comment']['apply_email_error']; }
if ($apply_location == '')
	{ $errors['apply_location'] = $translations['comment']['apply_location_error']; }
if ($apply_url == '')
	{ $errors['apply_url'] = $translations['comment']['apply_url_error']; }
if ($apply_msg == '')
	{ $errors['apply_msg'] = $translations['comment']['apply_msg_error']; }

/*
if ($_FILES['apply_cv'] &amp;&amp; $_FILES['apply_cv']['size'] &gt;= MAX_CV_SIZE)
	{ $errors['apply_cv'] = $translations['apply']['cv_error']; }
*/

if (!validate_email($apply_email))
	{ $errors['apply_email'] = $translations['apply']['email_invalid']; }
</pre>

<pre lang="php">
/* #2 Comment Only
$filename = time() . '_' . $_FILES['apply_cv']['name'];
if (move_uploaded_file($_FILES['apply_cv']['tmp_name'], FILE_UPLOAD_DIR . $filename))
{
	$attach = $filename;
}
else
{
	$attach = '';
}
*/
</pre>

Comment the following lines

<pre lang="php">
/* #3 Comment and must replace
$data = array('apply_email' =&gt; $apply_email,
		'apply_name' =&gt; $apply_name,
		'apply_msg' =&gt; $apply_msg,
		'company_email' =&gt; $j-&gt;mPosterEmail,
		'company_name' =&gt; $j-&gt;mCompany,
		'job_title' =&gt; $j-&gt;mTitle,
		'attachment_path' =&gt; APP_PATH . FILE_UPLOAD_DIR . $attach,
		'attachment_filename' =&gt; $attach,
		'job_id' =&gt; $job_id);
*/</pre>

and replace #3 with this:

<pre lang="php">
// #3 Replace with this
$data = array('apply_email' =&gt; $apply_email,
		'apply_name' =&gt; $apply_name,
		'apply_location' =&gt; $apply_location,
		'apply_url' =&gt; $apply_url,
		'apply_msg' =&gt; $apply_msg,
		'job_id' =&gt; $job_id);
</pre>

<pre lang="php">
/* #4 Comment only
$alex = new Postman();
if ($alex-&gt;MailApplyOnline($data))
	{ $_SESSION['apply_mail_sent'] = 1; }
else
	{ $_SESSION['apply_mail_sent'] = -1; }
*/
</pre>

add before that

<pre lang="php">
$_SESSION['apply_mail_sent'] = 1;
</pre>

and last on this page:

<pre lang="php">
// delete uploaded file (cleanup)
/* #5 Comment only
if ($attach != '')
	{ unlink(APP_PATH . FILE_UPLOAD_DIR . $filename); }
*/
</pre>

4. Open /_includes/class.JobApplication.php and follow where it says comment and replace.

Comment this:

<pre lang="php">
/*  #6 Comment and must replace
public function Apply()
	{
		global $db;
		$ip = $_SERVER['REMOTE_ADDR'];

		$sql = 'SELECT id
		FROM job_applications
		WHERE ip = "' . $ip . '" AND NOW() &lt;  DATE_ADD(created_on,INTERVAL 1 MINUTE)';
		$result = $db-&gt;query($sql);
		$row = $result-&gt;fetch_assoc();
		if (empty($row))
		{
			$sql = 'INSERT INTO job_applications (job_id, created_on, ip)
			VALUES (' . $this-&gt;mJobId . ', NOW(), "' . $ip . '")';
			$db-&gt;query($sql);
			return true;
		}
		else
		{ return false; }
	}
*/</pre>

and replace with this:

<pre lang="php">
public function Apply($data)
{
	global $db;
	$ip = $_SERVER['REMOTE_ADDR'];
	$sql = 'INSERT INTO job_applications (job_id, created_on, ip, apply_name, apply_email, apply_location, apply_url, apply_msg, is_active)
			VALUES (' . $data['job_id'] . ', NOW(), "' . $ip . '","'. $data['apply_name'] .'", "'. $data['apply_email'] .'","'. $data['apply_location'] .'", "'. $data['apply_url'] .'","'. $data['apply_msg'] .'",0)';
	if ($db-&gt;query($sql))
	{ return true; }
	else
	{ return false; }
}
</pre>

5. Open /_includes/class.Job.php and add this function (I added mine at the bottom of the page before the last closing tag)

<pre lang="php">
// #7 Get all job_applications
public function GetAllJobApplications()
	{
	global $db;
	$jobcomments = array();
	$sql = 'SELECT *
	FROM job_applications
	WHERE job_id = ' . $this-&gt;mId . ' ORDER BY created_on DESC';
	$result = $db-&gt;query($sql);
	while ($row = $result-&gt;fetch_assoc())
		{
			$jobcomments[] = array('apply_name' =&gt; $row['apply_name'],'apply_email' =&gt; $row['apply_email'], 'apply_location' =&gt; $row['apply_location'],'apply_url' =&gt; $row['apply_url'], 'apply_msg' =&gt; $row['apply_msg'], 'created_on' =&gt; $row['created_on']);
		}
	return $jobcomments;
	}
	</pre>

6. Open /_templates/job-detail.tpl and replace the form block with this:

<pre lang="php">
<form action="{$BASE_URL}apply-online/" enctype="multipart/form-data" id="frm-apply-online" method="post">
<table border="0">
<tbody>
	<tr>
		<td><label for="apply_name">{$translations.comment.apply_name}:</label></td>
		<td><input id="apply_name" maxlength="100" name="apply_name" size="30" type="text" value="{$smarty.session.apply_fields.apply_name}"/>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_name}<img alt="" src="{$BASE_URL}img/icon-delete.png"/>{/if}</span></td>
	</tr>
	<tr>
		<td><label for="apply_email">{$translations.comment.apply_email}:</label></td>
		<td><input id="apply_email" maxlength="150" name="apply_email" size="30" type="text" value="{$smarty.session.apply_fields.apply_email}"/>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_email}<img alt="" src="{$BASE_URL}img/icon-delete.png"/>{/if}</span></td>
	</tr>
	<tr>
		<td><label for="apply_location">{$translations.comment.apply_location}:</label></td>
		<td><input maxlength="150" name="apply_location" size="30" type="text" value="{$smarty.session.apply_fields.apply_location}"/>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_location}<img alt="" src="{$BASE_URL}img/icon-delete.png"/>{/if}</span></td>
	</tr>
	<tr>
		<td><label for="apply_url">{$translations.comment.apply_url}:</label></td>
		<td><input maxlength="150" name="apply_url" size="30" type="text" value="{$smarty.session.apply_fields.apply_url}"/>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_url}<img alt="" src="{$BASE_URL}img/icon-delete.png"/>{/if}</span></td>
	</tr>
	<tr>
		<td valign="top"><label for="apply_msg">{$translations.comment.apply_msg}:</label></td>
		<td><textarea cols="60" id="apply_msg" name="apply_msg" rows="10">{$smarty.session.apply_fields.apply_msg}</textarea>
		<span class="validation-error">{if $smarty.session.apply_errors.apply_msg}<img alt="" src="{$BASE_URL}img/icon-delete.png"/>{/if}</span></td>
	</tr>
	<tr>
		<td colspan="2"><input id="submit" name="submit" type="submit" value="{$translations.apply.submit}"/> {$translations.apply.or}
		<a href="#" onclick="$('#apply-online').SwitchVertically(10); return false;">{$translations.apply.cancel}</a></td>
	</tr>
	</tbody>
</table>
<input id="job_id" name="job_id" type="hidden" value="{$job.id}"/>
</form>
</pre>

7. Open /_includes/translation.ini and add at the bottom:

<pre lang="php">
[comment]
apply_name = "Your Name"
apply_email = "Your Email"
apply_url = "Website"
apply_location = "Your Location"
apply_msg = "Your Comment"
apply_name_error = "Your Name is required"
apply_email_error = "Your Email is required"
apply_url_error = "Website is required"
apply_location_error = "Your Location is required"
apply_msg_error = "Your Comment is required"
</pre>

8. We're almost done. Now open /page_job.php and add these lines before "$smarty-&gt;assign('job', $info);"

<pre lang="php">
$infoc = $job-&gt;GetAllJobApplications();
$smarty-&gt;assign('jobcomments', $infoc);
</pre>

9. And lastly, to display the comments, open again /_templates/job-detail.tpl and add this block where you want the comments to appear

<pre lang="php">
<table align="center" border="0" cellpadding="2" cellspacing="2" width="50%">{if $jobcomments}
{foreach item=jobc from=$jobcomments}
<tbody>
<tr>
<td>
<div class="job-comments"><strong>{$translations.comment.apply_name}:</strong> {$jobc.apply_name}<br/>
<strong>{$translations.comment.apply_email}:</strong> <a href="{$jobc.apply_email}">{$jobc.apply_email}</a><br/>
<strong>{$translations.comment.apply_url}:</strong> <a href="{$jobc.apply_url}">{$jobc.apply_url}</a><br/>
<strong>{$translations.comment.apply_location}:</strong> {$jobc.apply_location}<br/>
<strong>Date:</strong> {$jobc.created_on}<br/>
<div class="apply-status-ok">{$jobc.apply_msg}</div>
</div></td>
</tr>
{/foreach}
{/if}</tbody></table>
</pre>

Final note, be sure to change the text "__Apply__" to "__Comment__" (via the /_includes/translations.ini file) on the job ad ;)