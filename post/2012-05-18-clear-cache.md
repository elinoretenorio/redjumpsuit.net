### Clear Cache

May 18, 2012

here is how you clear your jobberBase website cache (useful when updating the template, this will force refresh all your css, js and asset files to load a brand new cache)


<img alt="cache" class="aligncenter size-full wp-image-1762" height="299" src="http://www.redjumpsuit.net/wp-content/uploads/2012/05/cache.png" title="cache" width="472"/>



create a php file called page_clearcache.php under /admin:


<pre lang="php">
<?php 
$user_cache = '../_templates/' . THEME . '/_cache/'; 
$admin_cache = '_templates/_cache/'; 

foreach(glob($user_cache .'*.php') as $uc) 
{    
unlink($uc); 
} 
$smarty->assign('user_success', 1);

foreach(glob($admin_cache .'*.php') as $ac) 
{
  unlink($ac);
}
$smarty-&gt;assign('admin_success', 1);
?&gt;
</pre>



add in the case under /admin/index.php


<pre lang="php">
// clear cache
case 'clear-cache':
	if(!isset($_SESSION['AdminId']))
	{
		redirect_to(BASE_URL);
		exit;
	}
	require_once 'page_clearcache.php';
	$template = 'clear-cache.tpl';
	$flag = 1;
	break;
</pre>



update /admin/header.tpl and look for this:


<pre lang="php"> 
{section name=index loop=$settings_categories}
	<li><a href="{$BASE_URL_ADMIN}settings/{$settings_categories[index].var_name}/">{$settings_categories[index].name}</a></li>
{/section}
</pre>

<img alt="admin-clear" class="aligncenter size-full wp-image-1760" height="232" src="http://www.redjumpsuit.net/wp-content/uploads/2012/05/admin-clear.png" title="admin-clear" width="498"/>



then add this right after:


<pre lang="php">
	<li><a href="{$BASE_URL_ADMIN}clear-cache/">Clear Cache</a></li>
</pre>



create a template file under /admin/_templates called clear-cache.tpl and put this:

<pre lang="php">
{include file="header.tpl"}
<div id="content">
<h2 id="password">Clear Cache</h2>
{if $user_success}
		User cache cleared. 

	{/if}
	{if $admin_success}
		Admin cache cleared. 

	{/if}</div>
<!-- #content -->
{include file="footer.tpl"}
</pre>

<img alt="admin-clear-ok" class="alignleft size-full wp-image-1761" height="261" src="http://www.redjumpsuit.net/wp-content/uploads/2012/05/admin-clear-ok.png" title="admin-clear-ok" width="429"/>