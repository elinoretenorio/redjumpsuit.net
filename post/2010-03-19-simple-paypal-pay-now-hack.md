### Simple PayPal Pay Now hack 

Mar 19, 2010

<img alt="paypal-button" class="alignleft size-full wp-image-1234" height="67" src="http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-button.png" title="paypal-button" width="147"/>

this is a very simple PayPal Buy Now hack for jobberBase (plain straightforward use of hosted Buy Now buttons) if you only need to be able to direct your job posters to a paypal payment facility with the details of the job being posted (job id, title, etc) and approve jobs based on the payment made.

now available:

### [Simple PayPal Payment for jobberBase 1.9.1](http://www.redjumpsuit.net/2011/07/07/simple-paypal-payment-for-jobberbase-1-9-1/)



what you need to do:
* for each job type, create a hosted Buy Now buttons on PayPal (and change button type to Pay Now)
* take note of the PayPal button id for each of the Buy Now/Pay Now buttons you created
* when you have finished following the tutorial below, go to Admin-&gt;PayPal-&gt;PayPal Setting for Job Types and enter the corresponding amount and PayPal button ID for each of the job type
* you will approve the jobs paid for in the Admin to activate the job ad

![paypal-hosted-buttons](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-hosted-buttons.png "paypal-hosted-buttons")

please consider donating :) 

other screenshots here:

__Posting Job__
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-post.png "paypal-post")
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-paynow.png "paypal-paynow")
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-paypal.png "paypal-pay")
__Admin__
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-link.png "paypal-link")
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-settings.png "paypal-settings")
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/paypal-jobtypes.png "paypal-jobtypes")

create the paypal settings table


<pre lang="php">
CREATE TABLE IF NOT EXISTS `paypal_settings` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `button_type` varchar(100) CHARACTER SET utf8 NOT NULL,
  `button_image` varchar(255) CHARACTER SET utf8 NOT NULL,
  `currency_code` varchar(3) CHARACTER SET utf8 NOT NULL,
  `return_url` varchar(255) CHARACTER SET utf8 NOT NULL,
  `is_active` tinyint(4) NOT NULL,
  `first_post_only` tinyint(4) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=MyISAM  DEFAULT CHARSET=latin1 AUTO_INCREMENT=2 ;

INSERT INTO `paypal_settings` (`id`, `button_type`, `button_image`, `currency_code`, `return_url`, `is_active`, `first_post_only`) VALUES
(1, '_s-xclick', 'https://www.paypal.com/en_US/i/btn/btn_paynowCC_LG.gif', 'USD', 'http://localhost/jb18/paypal/', 1, 0);
</pre>



alter the types table to add amount and paypal button id


<pre lang="php">
ALTER TABLE `types` ADD `amount` INT( 11 ) NULL ,
ADD `paypal_button_id` VARCHAR( 255 ) NULL 
</pre>



1) /config.php

after the line


<pre lang="php">
require_once '_includes/smarty/libs/Smarty.class.php';
</pre>



add below


<pre lang="php">
// paypal settings
require_once '_includes/class.PayPal.php';
</pre>



after the line


<pre lang="php">
define('CAPTCHA_PRIVATE_KEY', $settings['captcha_private_key']);
</pre>



add below


<pre lang="php">
// PayPal Settings
$pp = new SimplePayPal();
$pp_settings = $pp-&gt;showsettings();
define('PAYPAL_IS_ACTIVE',$pp_settings['is_active']);
define('PAYPAL_FIRST_POST_ONLY',$pp_settings['first_post_only']);
define('PAYPAL_BUTTON_TYPE',$pp_settings['button_type']);
define('PAYPAL_BUTTON_IMAGE',$pp_settings['button_image']);
define('PAYPAL_CURRENCY_CODE',$pp_settings['currency_code']);
define('PAYPAL_RETURN_URL',$pp_settings['return_url']);
</pre>



2) /index.php

after the lines


<pre lang="php">
case 'confirm':
	if(!ENABLE_NEW_JOBS) { redirect_to(BASE_URL); exit; }
	$flag =1;
	$job = new Job($id);
	$job_title = BASE_URL . URL_JOB .'/' . $job-&gt;mId . '/' . $job-&gt;mUrlTitle . '/';
	$smarty-&gt;assign('auth', $job-&gt;GetAuth());
	$smarty-&gt;assign('job_url', $job_title);
	$smarty-&gt;assign('first_time_post', $extra);			
	$template = 'publish-confirmation.tpl';
	break;
</pre>



add this block


<pre lang="php">
case 'payment':
	if(!ENABLE_NEW_JOBS) { redirect_to(BASE_URL); exit; }
	$flag =1;
	$job = new Job($id);
	$job_title = BASE_URL . URL_JOB .'/' . $job-&gt;mId . '/' . $job-&gt;mUrlTitle . '/';
	$smarty-&gt;assign('auth', $job-&gt;GetAuth());
	$smarty-&gt;assign('job_url', $job_title);
	$smarty-&gt;assign('first_time_post', $extra);
	if (PAYPAL_IS_ACTIVE == 1) {
		$smarty-&gt;assign('first_time_post', $job-&gt;CheckPosterEmail());
		$smarty-&gt;assign('paypal_item_number', $id);
		$smarty-&gt;assign('paypal_item_name', $job-&gt;mTitle);
		$smarty-&gt;assign('paypal_job_type', get_type_name_by_id($job-&gt;mTypeId));
		$smarty-&gt;assign('paypal_amount', get_type_amount_by_id($job-&gt;mTypeId));
		$smarty-&gt;assign('paypal_button_id', get_type_button_id_by_id($job-&gt;mTypeId));
	}
	$template = 'publish-paypal.tpl';
	break;
</pre>


	
3) /page_publish.php

after


<pre lang="php">
$job-&gt;Publish();
</pre>



change this line


<pre lang="php">
redirect_to(BASE_URL . 'confirm/' . $job-&gt;mId  . '/'.$first_time_post.'/');
</pre>



to this block


<pre lang="php">
if (PAYPAL_IS_ACTIVE == 1 &amp;&amp; PAYPAL_FIRST_POST_ONLY == 0)
{
	redirect_to(BASE_URL . 'payment/' . $job-&gt;mId  . '/'.$first_time_post.'/');
}
else
{
	redirect_to(BASE_URL . 'confirm/' . $job-&gt;mId  . '/'.$first_time_post.'/');
}
</pre>



4) /_includes/functions.php

replace this whole function


<pre lang="php">
function get_types()
{
...
}
</pre>



with this block


<pre lang="php">
function get_types()
{
	global $db;
	$sql = 'SELECT id, name, var_name, amount, paypal_button_id  
		FROM '.DB_PREFIX.'types ';
	$result = $db-&gt;query($sql);
	$types = array();
	while($row = $result-&gt;fetch_assoc())
	{
		$types[] = array('id' =&gt; $row['id'], 'name' =&gt; $row['name'], 'var_name' =&gt; $row['var_name'], 'amount' =&gt; $row['amount'], 'paypal_button_id' =&gt; $row['paypal_button_id']);
	}
	return $types;
}
</pre>



before the last 

<pre>?&gt;</pre>



add these blocks


<pre lang="php">
function get_type_name_by_id($id)
{
	global $db;
	$sql = 'SELECT name FROM '.DB_PREFIX.'types WHERE 
		id = '.$id;
	$result = $db-&gt;query($sql);
	$row = $result-&gt;fetch_assoc();
	
	if ($row)
		return $row['name'];
	return false;
}

// get type amount
function get_type_amount_by_id($id)
{
	global $db;
	$sql = 'SELECT amount FROM '.DB_PREFIX.'types WHERE 
		id = '.$id;
	$result = $db-&gt;query($sql);
	$row = $result-&gt;fetch_assoc();
	
	if ($row)
		return $row['amount'];
	return false;
}

// get type button id
function get_type_button_id_by_id($id)
{
	global $db;
	$sql = 'SELECT paypal_button_id FROM '.DB_PREFIX.'types WHERE 
		id = '.$id;
	$result = $db-&gt;query($sql);
	$row = $result-&gt;fetch_assoc();
	
	if ($row)
		return $row['paypal_button_id'];
	return false;
}
</pre>



5) /_includes/class.Job.php

replace the whole function 


<pre lang="php">
public function Publish()
{
...
}
</pre>



with this function


<pre lang="php">
public function Publish()
{
	global $db;
	if (PAYPAL_IS_ACTIVE == 0)
	{
		if ($this-&gt;CheckPosterEmail()) {
			$sql = 'UPDATE '.DB_PREFIX.'jobs SET is_temp = 0, is_active = 1 WHERE id = ' . $this-&gt;mId;
		} else {
			$sql = 'UPDATE '.DB_PREFIX.'jobs SET is_temp = 0, is_active = 0 WHERE id = ' . $this-&gt;mId;
		}
	} else {
		if ($this-&gt;CheckPosterEmail() &amp;&amp; PAYPAL_FIRST_POST_ONLY == 1) {
			$sql = 'UPDATE '.DB_PREFIX.'jobs SET is_temp = 0, is_active = 1 WHERE id = ' . $this-&gt;mId;
		} else {
			$sql = 'UPDATE '.DB_PREFIX.'jobs SET is_temp = 0, is_active = 0 WHERE id = ' . $this-&gt;mId;
		}
	}
	$db-&gt;query($sql);
}
</pre>



6) open /_includes/ and create a file called 'class.PayPal.php' and copy code below


<pre lang="php">
<?php
/**
 * jobber job board platform
 *
 * @author	RedJumpsuit <myredjumpsuit@gmail.com>
 * @web		http://www.redjumpsuit.net
 * 
 * Very simple PayPal integration for jobberBase - strictly no IPN :)
 */

class SimplePayPal
{

	function __construct()
	{ }
	
	// update paypal settings
	public function updatesettings($data)
	{
		global $db;
		
		$sql = 'UPDATE '.DB_PREFIX.'paypal_settings
					SET 
						button_type = "'. trim($data['button_type']) .'",
						button_image = "'. trim($data['button_image']) .'",	
						currency_code = "'. trim($data['currency_code']) .'", 		
						return_url = "'. trim($data['return_url']) .'", 
						is_active = '. $data['is_active'] .', 	
						first_post_only = '. $data['first_post_only'] .' 
					WHERE id = 1';
	
		if ($db-&gt;query($sql))
		{
			return true;
		}
		else
		{
			return false;
		}
	}
	
	// update paypal amount
	public function updatetypeamount($id,$amount=0)
	{
		global $db;
		
		if (empty($amount))
		{
			$amount = 0;
		}
		
		$sql = 'UPDATE '.DB_PREFIX.'types
					SET 
						amount = '. $amount .'					
					WHERE id = '. $id ;
	
		if ($db-&gt;query($sql))
		{
			return true;
		}
		else
		{
			return false;
		}
	}
	
	// update paypal amount
	public function updatetypebutton($id,$button='')
	{
		global $db;
		
		if (empty($button))
		{
			$button = '';
		}
		$sql = 'UPDATE '.DB_PREFIX.'types
					SET 
						paypal_button_id = "'. trim($button) .'"					
					WHERE id = '. $id ;
	
		if ($db-&gt;query($sql))
		{
			return true;
		}
		else
		{
			return false;
		}
	}
	
	// show paypal settings
	public function showsettings()
	{
		global $db;
		$pp = array();
		
		$sql = 'SELECT * FROM '.DB_PREFIX.'paypal_settings';
		$result = $db-&gt;query($sql);
		$row = $result-&gt;fetch_assoc();
		
		$pp['button_type'] = $row['button_type'];
		$pp['button_image'] = $row['button_image'];
		$pp['currency_code'] = $row['currency_code'];
		$pp['return_url'] = $row['return_url'];
		$pp['is_active'] = $row['is_active'];
		$pp['first_post_only'] = $row['first_post_only'];
		
		if (isset($pp))
		{
			return $pp;
		}
	}
	
}
?&gt;
</pre>



7) /_includes/translations.ini

add this at the bottom of the file


<pre lang="php">
[paypal]
button_type = "Button Type"
button_image = "Button Image"
currency_code = "Currency Code"
return_url = "Return URL"
is_active = "PayPal Active"
first_post_only = "First Post"
settings = "PayPal Settings"
update = "Update PayPal Settings"
update_types = "Update Types"
info = "Payment is required before job is posted."
amount = "Amount"
payment_needed = "Your job post has been submitted. In order for your post to be activated, complete the payment for the ad using the payment button below. Once payment is received you will be notified by email and your ad will be posted on the website."
</pre>



8) /_templates/publish-write.tpl

after this line


<pre lang="php">
<label for="type_id_{$types[tmp2].id}"><img alt="{$types[tmp2].name}" src="{$BASE_URL}_templates/{$THEME}/img/icon-{$types[tmp2].var_name}.png"/>
</label></pre>



add this before the closing label tag


<pre lang="php">
{if $smarty.const.PAYPAL_IS_ACTIVE == 1}{$smarty.const.PAYPAL_CURRENCY_CODE}{$types[tmp2].amount}{/if}
</pre>



this displays the amount corresponding to the job type (full time, part time, freelance, etc)

9) open /_templates/ and create a file called 'publish-paypal.tpl' and copy code below


<pre lang="php">
{include file="header.tpl"}
		
<div id="content">
	<div id="job-listings"></div><!-- #job-listings -->
	<div class="steps">
		<div id="step-1">
			{$translations.publish.step1}
		</div>
		<div id="step-2">
			{$translations.publish.step2}
		</div>
		<div class="step-active" id="step-3">
			{$translations.publish.step3}
		</div>
		<div class="clear"></div>
	</div>
	<br/>
	
	{if $first_time_post == 1 &amp;&amp; $smarty.const.PAYPAL_FIRST_POST_ONLY == 1}
		<div class="posted-ok">
				<strong>{$translations.publish.congratulations}</strong><br/><a href="{$job_url}">{$translations.publish.view}</a>.
			</div>
			<h4>{$translations.publish.options_title}</h4>
			<p>
				{$translations.publish.options_info}:
			</p>
			<ul>	
				<li><a href="{$BASE_URL}post/{$CURRENT_ID}/{$auth}/" title="{$translations.publish.edit}">» {$translations.publish.edit}</a></li>
				<li><a href="{$BASE_URL}deactivate/{$CURRENT_ID}/{$auth}/" title="{$translations.publish.deactivate}">» {$translations.publish.deactivate}</a></li>
			</ul>
		</div>
	{else}
		<div class="posted-pending">
			<p>{$translations.paypal.payment_needed}</p>
			<form action="https://www.paypal.com/cgi-bin/webscr" method="post">
			<input name="cmd" type="hidden" value="{$smarty.const.PAYPAL_BUTTON_TYPE}"/>
			<input name="currency_code" type="hidden" value="{$smarty.const.PAYPAL_CURRENCY_CODE}"/>
			<input name="item_number" type="hidden" value="{$paypal_item_number}"/>
			<input name="item_name" type="hidden" value="#{$paypal_item_number} - ({$paypal_job_type}) {$paypal_item_name}"/>
			<input name="hosted_button_id" type="hidden" value="{$paypal_button_id}"/>
			<input name="notify_url" type="hidden" value="{$smarty.const.PAYPAL_RETURN_URL}"/>
			<div class="suggestion">
				<input alt="PayPal - The safer, easier way to pay online!" border="0" name="submit" src="{$smarty.const.PAYPAL_BUTTON_IMAGE}" type="image"/>
				<strong>{$translations.paypal.amount}: {$smarty.const.PAYPAL_CURRENCY_CODE} {$paypal_amount}</strong> | {$translations.paypal.info}
				<img alt="" border="0" height="1" src="https://www.paypal.com/en_US/i/scr/pixel.gif" width="1"/>
			</div>
			</form>
		</div>
	{/if}
	
	<p>
			<a href="{$BASE_URL}">« {$translations.notfound.back}</a>
		</p>
<!-- /content -->

{include file="footer.tpl"}
</pre>



10) /admin/index.php

under the lines


<pre lang="php">
case URL_JOBS:
	if(!isset($_SESSION['AdminId']))
	{
		redirect_to(BASE_URL);
		exit;
	}
	require_once 'page_category.php';
	$flag = 1;
	break;
</pre>



add this


<pre lang="php">
case 'paypal':
	if(!isset($_SESSION['AdminId']))
	{
		redirect_to(BASE_URL);
		exit;
	}
	require_once 'page_paypal.php';
	$flag = 1;
	break;
</pre>



11) open /admin/ and create a file called 'page_paypal.php' and copy code below


<pre lang="php">
<?php

$pp = new SimplePayPal();

if ($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_POST['update_what']) && trim($_POST['update_what']) == 'settings') 
{
	if (empty($_POST['button_type'])) 
	{
		$smarty->assign('error', 'Button Type is required.');
	} 
	elseif (empty($_POST['button_image'])) 
	{
		$smarty-&gt;assign('error', 'Button Image is required.');
	} 
	elseif (empty($_POST['currency_code'])) 
	{
		$smarty-&gt;assign('error', 'Currency Code is required.');
	} 
	elseif (empty($_POST['return_url'])) 
	{
		$smarty-&gt;assign('error', 'Return URL is required.');
	}
	else 
	{
		
		$data = array('button_type' =&gt; $_POST['button_type'],
					  'button_image' =&gt; $_POST['button_image'],
					  'currency_code' =&gt; $_POST['currency_code'],
					  'return_url' =&gt; $_POST['return_url'],
					  'is_active' =&gt; $_POST['is_active'],
					  'first_post_only' =&gt; $_POST['first_post_only']);
		
		$pp-&gt;updatesettings($data);
		
		unset($_POST['button_type']);
		unset($_POST['button_image']);
		unset($_POST['currency_code']);
		unset($_POST['return_url']);
		unset($_POST['is_active']);
		unset($_POST['first_post_only']);
			
		$smarty-&gt;assign('success', 'PayPal Settings has been updated!');
		
	}
}

if ($_SERVER['REQUEST_METHOD'] == 'POST' &amp;&amp; isset($_POST['update_what']) &amp;&amp; trim($_POST['update_what']) == 'types') 
{
	
	foreach ($_POST['amount'] as $k1 =&gt; $v1) {
		$pp-&gt;updatetypeamount($k1,$v1);
	}
	
	foreach ($_POST['paypal_button_id'] as $k2 =&gt; $v2) {
		$pp-&gt;updatetypebutton($k2,$v2);
	}
	
	$smarty-&gt;assign('type_success', 'PayPal Setting for Job Types has been updated!');
	
}

$smarty-&gt;assign('current_category', 'paypal');
$smarty-&gt;assign('paypal', $pp-&gt;showsettings());	
$smarty-&gt;assign('types', get_types());
$html_title = 'PayPal Settings / ' . SITE_NAME;
$template = 'paypal.tpl';
?&gt;	
</pre>



12) /admin/_templates/header.tpl

after this line


<pre lang="php">
<li $current_category="settings" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}settings/">Settings</a></li>
</pre>



add this line


<pre lang="php">
<li $current_category="paypal" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}paypal/">PayPal</a></li>
</pre>



13) open /admin/_templates and create a file called 'paypal.tpl' and copy code below


<pre lang="php">
{include file="header.tpl"}
<div id="content">
<h3 class="page-heading">{$translations.paypal.settings}</h3>
<form action="{$smarty.server.REQUEST_URI}" id="publish_form" method="post" name="settings">
<fieldset>
<table border="0" cellpadding="2" cellspacing="2">
{if $success}
<tr>
	<td colspan="2">
		<img alt="" src="{$BASE_URL_ADMIN}img/icon_accept.gif"> {$success}
	</img></td>
</tr>
{elseif $error}
<tr>
	<td colspan="2">
		<img alt="" src="{$BASE_URL_ADMIN}img/exclamation.png"/> {$error}
	</td>
</tr>
{/if}
{if $success == ''}
<tr>
	<td>{$translations.paypal.button_type}:</td>
	<td><input maxlength="100" name="button_type" size="20" type="text" value="{if $paypal.button_type != ''}{$paypal.button_type}{else}{$smarty.const.PAYPAL_BUTTON_TYPE}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.paypal.button_image }:</td>
	<td><input maxlength="255" name="button_image" size="60" type="text" value="{if $paypal.button_image != ''}{$paypal.button_image}{else}{$smarty.const.PAYPAL_BUTTON_IMAGE}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.paypal.currency_code}:</td>
	<td><input maxlength="3" name="currency_code" size="20" type="text" value="{if $paypal.currency_code != ''}{$paypal.currency_code}{else}{$smarty.const.PAYPAL_CURRENCY_CODE}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.paypal.return_url}:</td>
	<td><input maxlength="255" name="return_url" size="60" type="text" value="{if $paypal.return_url != ''}{$paypal.return_url}{else}{$smarty.const.PAYPAL_RETURN_URL}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.paypal.is_active}:</td>
	<td>
	<select name="is_active">
		<option $smarty.const.paypal_is_active="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.paypal_is_active="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if PayPal is active (Yes) or not (No)</span>
	</td>
</tr>
<tr>
	<td colspan="2">Option below applies only if PayPal is set to Active.</td>
	<td>
</td></tr>
<tr>
	<td>{$translations.paypal.first_post_only}:</td>
	<td>
	<select name="first_post_only">
		<option $smarty.const.paypal_first_post_only="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.paypal_first_post_only="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if PayPal is active on first post only (Yes) or required whenever posting a job (No)</span>
	</td>
</tr>
<tr>
	<td colspan="2">
		<input name="update_what" type="hidden" value="settings"/>
		<input id="submit" name="submit" type="submit" value="{$translations.paypal.update}">
	</input></td>
</tr>
{/if}
</table>
</fieldset>
</form>

<h2>PayPal Setting for Job Types</h2>
{if $type_success}
<p><img alt="" src="{$BASE_URL_ADMIN}img/icon_accept.gif"/> {$type_success}</p>
{/if}
<form action="{$smarty.server.REQUEST_URI}" id="publish_form" method="post" name="types">
<table cellspacing="0" class="job-posts" id="job-posts">
<tr class="alt">
	<td>ID</td>
	<td>Type Name</td>
	<td>Amount</td>
	<td>PayPal ID</td>					
</tr>
{foreach from=$types item=types}
<tr>
	<td class="center">{$types.id}</td>
	<td>{$types.name}</td>
	<td class="center"><input name="amount[{$types.id}]" type="text" value="{$types.amount}"/></td>
	<td class="center"><input name="paypal_button_id[{$types.id}]" type="text" value="{$types.paypal_button_id}"/></td>
</tr>
{/foreach}
</table>
<input name="update_what" type="hidden" value="types"/>
<input id="submit" name="submit" type="submit" value="{$translations.paypal.update_types}">
</input></form>
<p>&nbsp;</p>
<p>&nbsp;</p>
</div><!-- #job-details -->
</pre>