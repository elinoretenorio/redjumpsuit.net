### Get Notified when Admin Password Changes

Aug 16, 2009

do you often forget your 'admin' password in your jobberBase website? here's a small hack to send yourself (if you are the administrator of the site) a copy of your new password whenever you change it in the admin panel.

1) open up /_includes/class.Postman.php and add this on the list of the public functions:


<pre lang="php">
// notify admin on password change
public function ChangePasswordAdmin($password)
{
	$subject = "Your password on " . SITE_NAME . " was changed";
	$msg = "Hello! :)";
	$msg .= "\n\nYour password was changed and can be accessed at: ";
	$msg .= "\n\n" . BASE_URL;
	$msg .= "\n\n---";
	$msg .= "\nUsername: admin";
	$msg .= "\nPassword: " . $password ;
	$msg .= "\n---\n\nThank you for using our service!\nThe " . SITE_NAME . " Team";
	mail(ADMIN_EMAIL, $subject, $msg, "From: " . SITE_NAME . " &lt;" . NOTIFY_EMAIL . "&gt;");
}
</pre>



2) open up /admin/page_password.php and add this


<pre lang="php">
$mail = new Postman();
$mail-&gt;ChangePasswordAdmin($db-&gt;real_escape_string($_POST['new_password']));
</pre>



before this line:


<pre lang="php">
$smarty-&gt;assign('error', 'Your password has been changed!');
</pre>



so then you don't have to memorize your password ;)
