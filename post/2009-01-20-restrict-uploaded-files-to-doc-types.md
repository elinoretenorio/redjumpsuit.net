### Restrict Uploaded Files to Doc Types

Jan 20, 2009

Hi, to those who are wanting to limit the file types that applicants send. Here is a short mod

on page_apply.php find:

<pre lang="php">
if ($_FILES['apply_cv'] &amp;&amp; $_FILES['apply_cv']['size'] &gt;= MAX_CV_SIZE)
{
    $errors['apply_cv'] = $translations['apply']['cv_error'];
}
</pre>

add below it:

<pre lang="php">
//check if file-type is acceptable
if ($_FILES['apply_cv'] &amp;&amp; (($_FILES['apply_cv']['type'] != "text/plain") AND ($_FILES['apply_cv']['type'] != "application/pdf") AND ($_FILES['apply_cv']['type'] != "application/msword") AND ($_FILES['apply_cv']['type'] != "application/rtf") AND ($_FILES['apply_cv']['type'] != "application/vnd.oasis.opendocument.text")))
{
    $errors['apply_attach'] = $translations['apply']['attach_invalid'];
}
</pre>

Open translations.ini in /_includes folder and add a line on the [apply] section:

<pre lang="php">
attach_invalid = "Your attachment is invalid. We only accept *.doc, *.docx, *.pdf and *.txt"
</pre>

Open job-details.tpl in /_templates folder and add:

<pre lang="php">
<tr>
<td valign="top"><label for="apply_cv">{$translations.apply.cv_label}:</label></td>
<td>
<input $smarty.session.apply_errors.apply_attach}class="error" id="apply_cv" if}="" name="apply_cv" type="file" {="" {if=""/>
<span class="validation-error">{if $smarty.session.apply_errors.apply_cv OR $smarty.session.apply_errors.apply_attach}<img alt="" src="{$BASE_URL}img/icon-delete.png"/>{/if}</span>
<div class="suggestion">{$translations.apply.cv_info}</div>
</td>
</tr>
</pre>