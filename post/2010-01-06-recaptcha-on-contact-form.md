### reCaptcha on Contact Form

Jan 06, 2010

<img alt="contact-us" class="alignleft size-full wp-image-1125" height="232" src="http://www.redjumpsuit.net/wp-content/uploads/2010/01/contact-us.png" title="contact-us" width="356"/>

one of our website has started getting spam messages from bots sending through the contact form, and obviously the easiest way to minimize (if not all together stop) is to just enable reCaptcha on the contact form. by default, jobberBase only has reCaptcha enabled when posting a new job and applying for a job. so here's how you can implement reCaptcha on the Contact Form as well.

open __page\_page.php__ on the root folder and add this before the first line:


<pre lang="php">
$smarty-&gt;assign('the_captcha', recaptcha_get_html(CAPTCHA_PUBLIC_KEY));
$smarty-&gt;assign('ENABLE_RECAPTCHA', ENABLE_RECAPTCHA);
</pre>



on the same file, before these lines:


<pre lang="php">
if ($contact_name == '')
{
	$errors['contact_name'] = $translations['contact']['name_error'];
}
</pre>



add this:


<pre lang="php">
// validation
if (ENABLE_RECAPTCHA)
{
	$resp = recaptcha_check_answer(CAPTCHA_PRIVATE_KEY,
	$_SERVER["REMOTE_ADDR"], 
	$_POST["recaptcha_challenge_field"],
	$_POST["recaptcha_response_field"]);

	if (!$resp-&gt;is_valid) 
	{
		$errors['captcha'] = $translations['captcha']['captcha_error'];
	}
}
</pre>



that should take care of generating the reCaptcha field. and then next, open /_includes/default/page.tpl and before the submit field


<pre lang="php">
<input id="submit" name="submit" type="submit" value="{$translations.contact.submit}"/>
</pre>



add this block:


<pre lang="php">
{if $ENABLE_RECAPTCHA}
<label>Anti-Spam:</label><br/>
	{literal}
	<script type="text/javascript">
	  var RecaptchaOptions = {
		theme : 'white',
		tabindex : 9
	  };
	</script>
	{/literal}
	{$the_captcha} <span class="validation-error">{if $smarty.session.apply_errors.captcha}
	<img alt="" src="{$BASE_URL}_templates/{$THEME}/img/icon-delete.png"/> {$errors.captcha}{/if}</span>
	<br/><br/>
{/if}
</pre>