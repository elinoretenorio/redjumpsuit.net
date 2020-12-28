### Integrating Google Maps

Mar 27, 2010

<img class="alignleft size-full wp-image-1234" src="http://www.redjumpsuit.net/wp-content/uploads/2010/03/googlemaps.png"/>

 this is possibly the next most requested hack for jobberBase and so i took on myself to put together this hack and hopefully this is something that a lot of people will find very useful for their website. i have been looking for a google maps api class for a while and finally found one that is not so hard to implement and should be very easy to follow. 

as always, if you encounter errors on this guide, please make sure you post the error string or code you receive as i will not be able to help you if i don't know what's wrong, right? 

__what you need:__
1. register for a google maps API key
2. download the <a href="http://www.phpinsider.com/php/code/GoogleMapAPI/" target="_blank">google maps api class</a> from phpinsider.net
3. unzip and save the GoogleMapAPI.class.php file on the /_includes folder

buy me coffee if you like this post! :) 

to start with, some screenshots for you people...

![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/googlemaps4.png)
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/googlemaps1.png)
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/googlemaps3.png)
![](http://www.redjumpsuit.net/wp-content/uploads/2010/03/googlemaps2.png)

so here goes:

1. create your google maps settings table

<pre lang="php">
CREATE TABLE `googlemaps` (
`id` INT NOT NULL AUTO_INCREMENT ,
`is_active` TINYINT NOT NULL DEFAULT '0',
`api_key` VARCHAR( 255 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
`height` VARCHAR( 10 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
`width` VARCHAR( 10 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
`sidebar` TINYINT NOT NULL DEFAULT '0',
`direction` TINYINT NOT NULL DEFAULT '0',
`type` TINYINT NOT NULL DEFAULT '0',
`scale` TINYINT NOT NULL DEFAULT '0',
PRIMARY KEY ( `id` )
) ENGINE = MYISAM 

INSERT INTO `googlemaps` (
`id` ,
`is_active` ,
`api_key` ,
`height` ,
`width` ,
`sidebar` ,
`direction` ,
`type` ,
`scale`
)
VALUES (
NULL , '1', 'your-google-maps-api-key', '200px', '300px', '0', '0', '0', '0'
);
</pre>



2. backup your jobs table (important!) after getting a backup, alter your jobs table to include an address field that will be used by google maps

<pre lang="php">
ALTER TABLE `jobs` ADD `address` VARCHAR( 255 ) NULL AFTER `company` 
</pre>



3. open /config.php

after


<pre lang="php">
require_once '_includes/smarty/libs/Smarty.class.php';
</pre>



add below


<pre lang="php">
// google map settings
require_once '_includes/class.GoogleMaps.php';
	
// google map api class
require_once '_includes/GoogleMapAPI.class.php';
</pre>



after


<pre lang="php">
define('CAPTCHA_PRIVATE_KEY', $settings['captcha_private_key']);
</pre>



add below


<pre lang="php">
// google maps api settings
$gm_class = new jbGoogleMaps();
$gm_settings = $gm_class-&gt;showsettings();
define('GOOGLE_MAPS_ACTIVE', $gm_settings['is_active']);
define('GOOGLE_MAPS_APIKEY', $gm_settings['api_key']);
define('GOOGLE_MAPS_H', $gm_settings['height']);
define('GOOGLE_MAPS_W', $gm_settings['width']);
define('GOOGLE_MAPS_SIDEBAR', $gm_settings['sidebar']);
define('GOOGLE_MAPS_DIRECTION', $gm_settings['direction']);
define('GOOGLE_MAPS_TYPECONTROL', $gm_settings['type']);	
define('GOOGLE_MAPS_SCALECONTROL', $gm_settings['scale']);
</pre>



4. open /page_write.php

after 


<pre lang="php">
'poster_email' =&gt; $poster_email,
</pre>



you will find this twice on this file, add this below


<pre lang="php">
'address' =&gt; $address,
 </pre>


 
5. open /page_job.php
 
after


<pre lang="php">
$smarty-&gt;assign('job', $info);
</pre>



add this block


<pre lang="php">
	if (GOOGLE_MAPS_ACTIVE == 1 &amp;&amp; $info['address'] != '') 
	{
	// google maps - declare google map class
	$map = new GoogleMapAPI();

	// google maps - enter your google maps api key
	$map-&gt;setAPIKey(GOOGLE_MAPS_APIKEY);

	// google maps - set height and width
	$map-&gt;setWidth(GOOGLE_MAPS_W);
	$map-&gt;setHeight(GOOGLE_MAPS_H);

	// google maps - set if sidebar enabled
	if (GOOGLE_MAPS_SIDEBAR == 0) {
		$map-&gt;disableSidebar();
	}

	// google maps - set if direction enabled
	if (GOOGLE_MAPS_DIRECTION == 0) {
		$map-&gt;disableDirections();
	}

	// google maps - set if type enabled
	if (GOOGLE_MAPS_TYPECONTROL == 0) {
		$map-&gt;disableTypeControls();
	}

	// google maps - set if scale enabled
	if (GOOGLE_MAPS_SCALECONTROL == 0) {
		$map-&gt;disableScaleControl();
	}

	// google maps - create map marker for the address
	$map-&gt;addMarkerByAddress($info['address'],$info['company'],'<b>'. $info['company'] .'</b>');

	// google maps - assign api to smarty variables
	$smarty-&gt;assign('google_map_header',$map-&gt;getHeaderJS());
	$smarty-&gt;assign('google_map_js',$map-&gt;getMapJS());
	$smarty-&gt;assign('google_map_sidebar',$map-&gt;getSidebar());
	$smarty-&gt;assign('google_map',$map-&gt;getMap());
}
</pre>



6. open /_templates/default/publish-write.tpl

after


<pre lang="php">
<tr>
	<td class="publish-label">{$translations.publish.name_label}:</td>
	<td><input $errors.company}class="error" id="company" if}="" name="company" size="40" tabindex="6" type="text" value="{if $job.company}{$job.company|escape}{else}{$smarty.post.company|escape}{/if}" {="" {if=""/>
	<span class="validation-error">{if $errors.company}<img alt="" src="{$BASE_URL}_templates/{$THEME}/img/icon-delete.png"/>{/if}</span>
	</td>
</tr> 
</pre>



add this


<pre lang="php">
<tr>
	<td class="publish-label">{$translations.publish.address}:</td>
	<td><input $errors.address}class="error" id="address" if}="" name="address" size="40" tabindex="7" type="text" value="{if $job.address}{$job.address}{else}{$smarty.post.address}{/if}" {="" {if=""/>
	</td>
</tr>
</pre>



notice the tabindex of the new field, it is set to 7, which means you have to adjust the tabindex (add 1) of the fields after it, don't say i didn't tell you!

7. open /_templates/default/job-details.tpl

after


<pre lang="php">
{$job.description}
</pre>



add this


<pre lang="php">
{if $smarty.const.GOOGLE_MAPS_ACTIVE == 1 &amp;&amp; $job.address != ''}
	<p>{$google_map}</p>
{/if}
</pre>



8. open /_templates/default/header.tpl

after this


<pre lang="php">
<script type="text/javascript">
	Jobber.I18n = {$translationsJson};
</script>
</pre>



add this


<pre lang="php">
{if $google_map_header}
	{$google_map_header}
	{$google_map_js}
	<!-- necessary for google maps polyline drawing in IE -->
	<style type="text/css">
	  v\:* {ldelim}
		behavior:url(#default#VML);
	 {rdelim}
	</style>
{/if}
</pre>



and update this


<pre lang="php">
<body>
</body></pre>



to this


<pre lang="php">
<body $google_map_header}onload="onLoad()" if}="" {="" {if="">
</body></pre>



9. open /_includes/translations.ini

on 


<pre lang="php">
[publish]
</pre>



add this


<pre lang="php">
address = "Address"
</pre>



then, add this at the end of the file


<pre lang="php">
[googlemaps]
settings = "Google Maps Settings"
is_active = "Is Active?"
api_key = "API Key"
height = "Height"
width = "Width"
sidebar = "Sdiebar?"
direction = "Direction?"
type = "Type Control?"
scale = "Scale Control?"
submit = "Update Google Maps Seetings"
</pre>



10. open /_includes/class.Job.php

after 


<pre lang="php">
var $mMySqlDate = false;
</pre>



add 


<pre lang="php">
var $mAddress = false;
</pre>



on the first SQL statement, after the field


<pre lang="php">
c.name as type_name, 
</pre>



add this


<pre lang="php">
a.address AS address,
</pre>



after this


<pre lang="php">
$this-&gt;mTypeVarName = $row['type_var_name'];
</pre>



add this


<pre lang="php">
$this-&gt;mAddress = $row['address'];
</pre>



wherever you find this line


<pre lang="php">
'type_name' =&gt; $this-&gt;mTypeName,
</pre>



add this after


<pre lang="php">
'address' =&gt; $this-&gt;mAddress,
</pre>



look for this line


<pre lang="php">
$sql = 'INSERT INTO '.DB_PREFIX.'jobs (type_id, category_id, title, description, company, city_id, url, apply, created_on, is_temp, is_active, 
</pre>



and update to this


<pre lang="php">
$sql = 'INSERT INTO '.DB_PREFIX.'jobs (type_id, category_id, title, description, company, address, city_id, url, apply, created_on, is_temp, is_active, 
</pre>



then after this


<pre lang="php">
"' . $params['company'] . '",
</pre>



add this


<pre lang="php">
 "' . $params['address'] . '",
 </pre>


 
still on the same file, look for this 


<pre lang="php">
company = "' . $params['company'] . '",
</pre>



then add this after that


<pre lang="php">
address = "' . $params['address'] . '",
</pre>



11. create a new file on /_includes/ and save it as class.GoogleMaps.php then paste this code in the file you created


<pre lang="php">
<?php
/**
 * jobber job board platform
 *
 * @author	RedJumpsuit <myredjumpsuit@gmail.com>
 * @web		http://www.redjumpsuit.net
 * 
 * Google Maps for jobberBase
 */

class jbGoogleMaps
{

	function __construct()
	{ }
	
	// update google maps settings
	public function updatesettings($data)
	{
		global $db;
		
		$sql = 'UPDATE '.DB_PREFIX.'googlemaps
					SET 
						is_active = '. $data['is_active'] .', 
						api_key = "'. trim($data['api_key']) .'",
						height = "'. trim($data['height']) .'",	
						width = "'. trim($data['width']) .'", 		
						sidebar = '. $data['sidebar'] .',
						direction = '. $data['direction'] .', 
						type = '. $data['type'] .', 
						scale = '. $data['scale'] .'
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
	
	// show google maps settings
	public function showsettings()
	{
		global $db;
		$gmaps = array();
		
		$sql = 'SELECT * FROM '.DB_PREFIX.'googlemaps';
		$result = $db-&gt;query($sql);
		$row = $result-&gt;fetch_assoc();
		
		$gmaps['is_active'] = $row['is_active'];
		$gmaps['api_key'] = $row['api_key'];
		$gmaps['height'] = $row['height'];
		$gmaps['width'] = $row['width'];
		$gmaps['sidebar'] = $row['sidebar'];
		$gmaps['direction'] = $row['direction'];
		$gmaps['type'] = $row['type'];
		$gmaps['scale'] = $row['scale'];
		
		if (isset($gmaps))
		{
			return $gmaps;
		}
	}
	
}
?&gt;
</pre>



12. open /admin/index.php

after this


<pre lang="php">
case 'cities':
	if(!isset($_SESSION['AdminId']))
	{
		redirect_to(BASE_URL);
		exit;
	}

	require_once 'page_cities.php';
	$flag = 1;
	$citiesPage = new CitiesPage();
	$template = $citiesPage-&gt;processRequest($id, $extra, $smarty);
	break;
</pre>


	
add this


<pre lang="php">
case 'googlemaps':
	if(!isset($_SESSION['AdminId']))
	{
		redirect_to(BASE_URL);
		exit;
	}
	require_once 'page_googlemaps.php';
	$flag = 1;
	break;
</pre>


	
13. open /admin/page_edit_post.php

after this


<pre lang="php">
$jobToEdit['type_id'] = $_POST['type_id'];
</pre>



add this


<pre lang="php">
$jobToEdit['address'] = $_POST['address'];
</pre>



then after this


<pre lang="php">
'poster_email' =&gt; $poster_email,
</pre>



add this


<pre lang="php">
'address' =&gt; $address,
</pre>



14. create a new file on /admin/ and save it as page_googlemaps.php and paste this code


<pre lang="php">
<?php

$gm = new jbGoogleMaps();

if ($_SERVER['REQUEST_METHOD'] == 'POST') 
{
	if (empty($_POST['api_key'])) 
	{
		$smarty->assign('error', 'API Key is required.');
	} 
	elseif (empty($_POST['height'])) 
	{
		$smarty-&gt;assign('error', 'Height is required.');
	} 
	elseif (empty($_POST['width'])) 
	{
		$smarty-&gt;assign('error', 'Width is required.');
	} 
	else 
	{
		
		$data = array('is_active' =&gt; $_POST['is_active'],
					  'api_key' =&gt; $_POST['api_key'],
					  'height' =&gt; $_POST['height'],
					  'width' =&gt; $_POST['width'],
					  'sidebar' =&gt; $_POST['sidebar'],
					  'direction' =&gt; $_POST['direction'],
					  'type' =&gt; $_POST['type'],
					  'scale' =&gt; $_POST['scale']);
		
		$gm-&gt;updatesettings($data);
		
		unset($_POST['is_active']);
		unset($_POST['api_key']);
		unset($_POST['height']);
		unset($_POST['width']);
		unset($_POST['sidebar']);
		unset($_POST['direction']);
		unset($_POST['type']);
		unset($_POST['Scale']);
			
		$smarty-&gt;assign('success', 'Google Maps Settings has been updated!');
		
	}
}

$smarty-&gt;assign('current_category', 'googlemaps');
$smarty-&gt;assign('gm', $gm-&gt;showsettings());	
$html_title = 'Google Maps Settings / ' . SITE_NAME;
$template = 'googlemaps.tpl';
?&gt;	
</pre>



15. open /admin/_templates/header.tpl

after this


<pre lang="php">
<li $current_category="settings" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}settings/">Settings</a></li>
</pre>



add this


<pre lang="php">
<li $current_category="googlemaps" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}googlemaps/">Google Maps</a></li>
</pre>



16. open /admin/_templates/edit-post.tpl

after this


<pre lang="php">
<tr>
	<td class="publish-label">{$translations.publish.name_label}:</td>
	<td><input $errors.company}class="error" id="company" if}="" name="company" size="40" tabindex="6" type="text" value="{if $job.company}{$job.company|escape}{else}{$smarty.post.company|escape}{/if}" {="" {if=""/>
	<span class="validation-error">{if $errors.company}<img alt="" src="{$BASE_URL_ADMIN}img/icon-delete.png"/>{/if}</span>
	</td>
</tr> 
</pre>



add this


<pre lang="php">
<tr>
	<td class="publish-label">{$translations.publish.address}:</td>
	<td><input $errors.address}class="error" id="address" if}="" name="address" size="40" tabindex="7" type="text" value="{if $job.address}{$job.address}{else}{$smarty.post.address}{/if}" {="" {if=""/>
	</td>
</tr>
</pre>



again, don't forget to adjust the tab index of the fields

17. lastly, create a file in /admin/_templates/ and save it as googlemaps.tpl and paste the code below


<pre lang="php">
{include file="header.tpl"}
<div id="content">
<h3 class="page-heading">{$translations.googlemaps.settings}</h3>
<form action="{$smarty.server.REQUEST_URI}" id="publish_form" method="post" name="settings">
<fieldset>
<table border="0" cellpadding="2" cellspacing="2">
{if $success}
<tr>
	<td colspan="2">
		<img alt="" src="{$BASE_URL_ADMIN}img/icon_accept.gif"/> {$success}
	</td>
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
	<td>{$translations.googlemaps.is_active}:</td>
	<td>
	<select name="is_active">
		<option $smarty.const.google_maps_active="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.google_maps_active="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if Goole Maps is active (Yes) or not (No)</span>
	</td>
</tr>
<tr>
	<td colspan="2">Options below applies only if Google Maps is set to Active.</td>
	<td>
</td></tr>
<tr>
	<td>{$translations.googlemaps.api_key}:</td>
	<td><input maxlength="100" name="api_key" size="60" type="text" value="{if $gm.api_key != ''}{$gm.api_key}{else}{$smarty.const.GOOGLE_MAPS_APIKEY}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.googlemaps.height}:</td>
	<td><input maxlength="10" name="height" size="20" type="text" value="{if $gm.height != ''}{$gm.height}{else}{$smarty.const.GOOGLE_MAPS_H}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.googlemaps.width}:</td>
	<td><input maxlength="10" name="width" size="20" type="text" value="{if $gm.width != ''}{$gm.width}{else}{$smarty.const.GOOGLE_MAPS_W}{/if}"/></td>
</tr>
<tr>
	<td>{$translations.googlemaps.sidebar}:</td>
	<td>
	<select name="sidebar">
		<option $smarty.const.google_maps_sidebar="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.google_maps_sidebar="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if Sidebar is active (Yes) or not (No)</span>
	</td>
</tr>
<tr>
	<td>{$translations.googlemaps.direction}:</td>
	<td>
	<select name="direction">
		<option $smarty.const.google_maps_direction="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.google_maps_direction="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if Direction is active (Yes) or not (No)</span>
	</td>
</tr>
<tr>
	<td>{$translations.googlemaps.type}:</td>
	<td>
	<select name="type">
		<option $smarty.const.google_maps_typecontrol="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.google_maps_typecontrol="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if Type is active (Yes) or not (No)</span>
	</td>
</tr>
<tr>
	<td>{$translations.googlemaps.scale}:</td>
	<td>
	<select name="scale">
		<option $smarty.const.google_maps_scalecontrol="1}" if}="" selected="selected" value="1" {="" {if="">Yes</option>
		<option $smarty.const.google_maps_scalecontrol="0}" if}="" selected="selected" value="0" {="" {if="">No</option>
	</select>
	<span class="suggestion">Specify if Scale is active (Yes) or not (No)</span>
	</td>
</tr>
<tr>
	<td colspan="2">
		<input id="submit" name="submit" type="submit" value="{$translations.googlemaps.submit}"/>
	</td>
</tr>
{/if}
</table>
</fieldset>
</form>

<p>&nbsp;</p>
<p>&nbsp;</p>

</div><!-- #job-details -->
</pre>