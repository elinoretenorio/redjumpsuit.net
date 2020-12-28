### Multiple Admin Users

May 09, 2010

<img alt="admin-users" class="alignleft size-medium wp-image-1366" height="270" src="http://www.redjumpsuit.net/wp-content/uploads/2010/05/admin-users-300x270.png" title="admin-users" width="300"/>

Note: this hack is for jobberBase 1.8

this will allow you to create unlimited number of admin users who can manage the site for you. the superuser will still be "admin" who will be able to create and delete other accounts, the rest of the users can do the rest of the "admin" user can do.

it should be useful for sites who has more than one person managing all the aspects of their job board and did not want to share their password with others (or using one general password for everybody else.) as this is a just a basic multi-admin user hack, there is still a lot more that can be done with this hack like auditing actions done by all the accounts or restricting access to only certain pieces of the admin site.

1) open /admin/index.php

after this block


<pre lang="php">
case 'password':
	if(!isset($_SESSION['AdminId']))
	{
		redirect_to(BASE_URL);
		exit;
	}
	require_once 'page_password.php';
	$html_title = 'Change password / ' . SITE_NAME;
	$template = 'password.tpl';
	$flag = 1;
	break;
</pre>



add this block:


<pre lang="php">
case 'users':
	if (!isset($_SESSION['AdminId']) || (isset($_SESSION['AdminId']) &amp;&amp; $_SESSION['AdminId'] &gt; 1))
	{
		redirect_to(BASE_URL);
		exit;
	}
	require_once 'page_users.php';
	$html_title = 'Add new users / ' . SITE_NAME;
	$template = 'users.tpl';
	$flag = 1;
	break;
</pre>



2) create a file called "page_users.php" on the /admin folder and add the code below:


<pre lang="php">
<?php
/*
 * jobberBase job board platform
 *
 * @author		RedJumpsuit <myredjumpsuit@gmail.com>
 * @web		http://www.redjumpsuit.net
 * 
 * Manage multiple admin users
 */
 
$smarty-&gt;assign('current_category', 'users');

require_once '_includes/class.Admin.php';
$ad = new CAdmin();

$smarty-&gt;assign('error', '');

if ($extra == 'delete')
{
	if ($ad-&gt;delete($id, $_SESSION['AdminId']))
	{
		$smarty-&gt;assign('success', 'User was deleted.');
	}
	else
	{
		$smarty-&gt;assign('error', 'User could not be deleted.');
	}
}

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
	if (empty($_POST['username'])) {
		$smarty-&gt;assign('error', 'The username is empty. Please type in a new username.');
	} elseif (empty($_POST['new_password'])) {
		$smarty-&gt;assign('error', 'The password is empty. Please type in a new password.');
	} elseif ($_POST['new_password'] != $_POST['verify_password']) {
		$smarty-&gt;assign('error', 'The password and verification password do not match.');
	} else {
		
		if($ad-&gt;create($_POST['username'], $db-&gt;real_escape_string($_POST['new_password']), $_SESSION['AdminId']))
		{
			$smarty-&gt;assign('success', 'New user has been created!');
		}
		else
		{
			$smarty-&gt;assign('error', 'User already exists.');
		}
	}
}

$smarty-&gt;assign('users', $ad-&gt;listusers());	
?&gt;
</pre>



3) create a file called "users.tpl" under /admin/_templates/ and add the code below:


<pre lang="php">
{include file="header.tpl"}
<div id="content">
		<h3>Add new Admin user</h3>
		
		<form action="{$smarty.server.REQUEST_URI}" id="publish_form" method="post">
			<fieldset>
				<table border="0" cellpadding="2" cellspacing="2">
					{if $error}
					<tr>
						<td colspan="2">
							<img alt="" src="{$BASE_URL_ADMIN}img/exclamation.png"/> {$error}
						</td>
					</tr>
					{elseif $success}
					<tr>
						<td colspan="2">
							<img alt="" src="{$BASE_URL_ADMIN}img/icon_accept.gif"/> {$success}
						</td>
					</tr>
					{/if}
					<tr>
						<td>Username:</td>
						<td><input name="username" size="30" type="username"/></td>
					</tr>
					<tr>
						<td>New password:</td>
						<td><input name="new_password" size="30" type="password"/></td>
					</tr>
					<tr>
						<td>Verify password:</td>
						<td><input name="verify_password" size="30" type="password"/></td>
					</tr>
				</table>
			</fieldset>
			<p>
				<button class="submit_button" type="submit">Add new user</button>
			</p>
		</form>
		
<h2>List of Admin Users</h2>
<table cellspacing="0" class="job-posts" id="job-posts">
<tr class="alt">
	<td>ID</td>
	<td>Username</td>				
	<td>Action</td>
</tr>
{foreach from=$users item=list}
<tr>
	<td class="center">{$list.id}</td>
	<td>{$list.username}</td>
	<td class="center"><a href="{$BASE_URL_ADMIN}users/{$list.username}/delete/">Delete</a></td>
</tr>
{/foreach}
</table>

</div><!-- #content -->

{include file="footer.tpl"}
</pre>



4) create a file called "sidebar.tpl" under /admin/_templates/ and add the code below:


<pre lang="php">
<div id="sidebar">
<ul>
{if $smarty.session.AdminId == 1}
<h3>Add-Ons</h3>
<li $current_category="users" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}users/">Manage Users</a></li>
{/if}
</ul>
</div>
</pre>



5) open /admin/_templates/header.tpl and add this at the very end of the page:


<pre lang="php">
{if $smarty.session.AdminId}
<div id="sidebar">
	{include file="sidebar.tpl"}
</div><!-- #sidebar -->
{/if}
</pre>



6) lastly, open /admin/_includes/class.Admin.php and after this block:


<pre lang="php">
public function getId()
 	{
 		return $this-&gt;userId;
 	}
</pre>



add this:


<pre lang="php">
public function create($username,$password,$id)
 	{
		if ($id == 1) 
		{
			global $db;
			$md5password = md5($password);
			$sql = 'SELECT id FROM '.DB_PREFIX.'admin WHERE username="'.$username.'"';
			$result = $db-&gt;query($sql);
			$row = $result-&gt;fetch_assoc();
			if (empty($row))
			{
				$sql = 'INSERT INTO '.DB_PREFIX.'admin (
							username, password)
						VALUES (
							"'. trim($username) .'",
							"'. $md5password .'")';
				$db-&gt;query($sql);
				return true;
			} 
			else
			{
				return false;
			}
		} 
		else 
		{
			return false;
		}
 	}
	
	public function listusers()
	{
		global $db;
		$sql = 'SELECT * FROM '.DB_PREFIX.'admin WHERE id &gt; 1';
		$result = $db-&gt;query($sql);
		while ($row = $result-&gt;fetch_assoc())
		{
			$users[] = $row;
		}
		
		if (isset($users))
		{
			return $users;
		}
	}
	
	public function delete($username,$id)
	{
		if ($id == 1) 
		{
			global $db;
			$sql = 'DELETE FROM '.DB_PREFIX.'admin WHERE username = "'. trim($username) .'"';
			if ($db-&gt;query($sql))
			{
				return true;
			}
			else
			{
				return false;
			}
		} 
		else 
		{
			return false;
		}
	}
</pre>



like i said, there is more that can be done to extend this hack, but at least you will have a good place to start on.

