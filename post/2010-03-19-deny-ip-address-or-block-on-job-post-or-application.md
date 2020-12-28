### Deny IP address or IP block on job post or application

Mar 19, 2010

<img alt="anti-spam" class="alignleft size-thumbnail wp-image-1205" height="150" src="http://www.redjumpsuit.net/wp-content/uploads/2010/03/anti-spam-150x150.png" title="anti-spam" width="150"/>

i haven't posted in a while (months actually) but as a come back i am posting one of the most requested hack lately. this Deny IP Manger does the following:

denies IP address
denies IP block (using wildcard)
lets you add IP address or block
lets you delete address or block
can be set to either on or off
very straightforward IP deny function

please consider donating :) 
__Deny IP Link__
![anti-spam](http://www.redjumpsuit.net/wp-content/uploads/2010/03/denyip-link.png "anti-spam")
__Deny IP Manager__
![anti-spam](http://www.redjumpsuit.net/wp-content/uploads/2010/03/denyip-list.png "anti-spam")

let's begin.

first create the ip deny table:


<pre lang="php">
CREATE TABLE IF NOT EXISTS `denyip` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `ip_address` varchar(255) NOT NULL,
  `created_on` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;
</pre>



1) /config.php

after line


<pre lang="php">
require_once '_includes/smarty/libs/Smarty.class.php';
</pre>



add


<pre lang="php">
// deny IP class
require_once '_includes/class.DenyIP.php';
</pre>



after line


<pre lang="php">
define('CAPTCHA_PRIVATE_KEY', $settings['captcha_private_key']);
</pre>



add


<pre lang="php">
// set if ip deny is on
define('DENY_IP', 1);
</pre>



this is where you set whether the deny ip function is on (1) or not (0)

2) /index.php

after lines


<pre lang="php">
if(!isset($_SERVER['HTTP_REFERER'])) {
       $_SERVER['HTTP_REFERER'] = '';
    }
</pre>


   
add below


<pre lang="php">
$clientip = $_SERVER['REMOTE_ADDR'];
$ipclass = new DenyIP();
</pre>



after line


<pre lang="php">
case 'apply-online':
</pre>



add below


<pre lang="php">
if(DENY_IP == 1 &amp;&amp; $ipclass-&gt;blockip($clientip))
{
    redirect_to(BASE_URL);
    exit;
}
</pre>



this will block any online applications from your denied IP list

after lines


<pre lang="php">
// create/edit a job post
case 'post':
</pre>



add below


<pre lang="php">
if(DENY_IP == 1 &amp;&amp; $ipclass-&gt;blockip($clientip))
{
    redirect_to(BASE_URL);
    exit;
}
</pre>



this will block any job posts from your denied IP list

3) /_includes/function.php

before the last 

<pre>?&gt;</pre>

 add this


<pre lang="php">
// musik at krapplack dot de
// 04-Jun-2006 12:52
function in_array_wildcard($needle, $haystack) {
    # this function allows wildcards in the array to be searched
    foreach ($haystack as $value) {
        if (true === fnmatch($value, $needle)) {
            return true;
        }
    }
    return false;
}
</pre>



this is a modified in_array function that allows for searching needle (string) with wildcard on a haystack (list)

4) inside /_includes/, create a file called 'class.DenyIP.php' and copy code below


<pre lang="php">
<?php
/**
 * jobber job board platform
 *
 * @author  RedJumpsuit <myredjumpsuit@gmail.com>
 * @web     http://www.redjumpsuit.net
 *
 * Deny IP class handles denying job post and applications by IP address
 */

class DenyIP
{

   
    public function __construct()
    { }
   
    public function addip($ip)
    {
        global $db;
        $sql1 = 'SELECT *
                       FROM '.DB_PREFIX.'denyip
                       WHERE ip_address = "'. $ip .'"';
        $result1 = $db-&gt;query($sql1);
        $row1 = $result1-&gt;fetch_assoc();          
        if (!$row1) {
            $sql = 'INSERT INTO '.DB_PREFIX.'denyip (ip_address, created_on)
                            VALUES ("'. $ip .'", NOW())';
            if ($db-&gt;query($sql)) {
                return true;
            } else {
                return false;
            }
        } else {
            return false;
        }
    }
   
    public function deleteip($ip)
    {   
        global $db;
        $sql = 'DELETE FROM '.DB_PREFIX.'denyip WHERE ip_address = "' . $ip .'"';
        if ($db-&gt;query($sql)) {
            return true;
        } else {
            return false;
        }
    }
   
    public function listip()
    {
        global $db;
        $ips = array();
        $sql = 'SELECT *
                       FROM '.DB_PREFIX.'denyip
                       ORDER BY created_on ASC';
        $result = $db-&gt;query($sql);
        while ($row = $result-&gt;fetch_assoc())
        {
            $ips[] = array('id' =&gt; $row['id'], 'ip_address' =&gt; $row['ip_address'], 'created_on' =&gt; $row['created_on']);
        }
        return $ips;
    }
   
    public function blockip($ip)
    {
       
        global $db;
        $deny = array();
        $sql = 'SELECT ip_address AS ip_address FROM '.DB_PREFIX.'denyip';
        $result = $db-&gt;query($sql);
        while ($row = $result-&gt;fetch_assoc())
        {
            $deny[] = trim($row['ip_address']);
        }
       
        if (in_array_wildcard($ip, $deny)) {
            return true;
        } else {
            return false;
        }

    }
}
?&gt;
</pre>



5) /admin/index.php, after the lines


<pre lang="php">
case 'delete':
    if(!isset($_SESSION['AdminId']))
    {
        redirect_to(BASE_URL);
        exit;
    }
    require_once 'page_delete.php';
    $flag = 1;
    break;
</pre>


   
add


<pre lang="php">
case 'denyip':
    if(!isset($_SESSION['AdminId']))
    {
        redirect_to(BASE_URL);
        exit;
    }
    require_once 'page_denyip.php';
    $flag = 1;
    break;
</pre>


   
6) inside /admin/, create a file called 'page_denyip.php' and copy code below


<pre lang="php">
<?php
$ipclass = new DenyIP();
$smarty->assign('current_category', 'denyip');

if ($extra == 'remove')
{
    $ipclass-&gt;deleteip($id);
}

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    if (empty($_POST['ip_address'])) {
        $smarty-&gt;assign('error', 'The IP Address is empty. Please enter an IP Address.');
    } else {
        if ($ipclass-&gt;addip($_POST['ip_address'])) {
            $smarty-&gt;assign('success', 'IP Address has been added!');
        } else {
            $smarty-&gt;assign('error', 'The IP Address already exists.');
        }
    }
}

$smarty-&gt;assign('ips', $ipclass-&gt;listip());
$template = 'denyip.tpl';
?&gt;
</pre>



7) /admin/_templates/header.tpl

after the line


<pre lang="php">
<li $current_category="settings" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}settings/">Settings</a></li>
</pre>



add below


<pre lang="php">
<li $current_category="denyip" if}="" {="" {if="" }class="selected"><a href="{$BASE_URL_ADMIN}denyip/">Deny IP</a></li>
</pre>



8) inside /admin/_templates, create a file called 'denyip.tpl' and copy code below


<pre lang="php">
{include file="header.tpl"}
<div id="content">
<h3 class="page-heading">Deny IP Manager</h3>

<form action="{$smarty.server.REQUEST_URI}" id="publish_form" method="post">
    <fieldset>
        <table border="0" cellpadding="2" cellspacing="2">
            {if $error}
            <tr>
                <td colspan="2">
                    <img alt="" src="{$BASE_URL_ADMIN}img/exclamation.png"/> {$error}
                </td>
            </tr>
            {/if}
            {if $success}
            <tr>
                <td colspan="2">
                    <img alt="" src="{$BASE_URL_ADMIN}img/icon_accept.gif"/> {$success}
                </td>
            </tr>
            {/if}
            <tr>
                <td>IP Address/Block:</td>
                <td><input name="ip_address" size="30" type="ip_address"/></td>
            </tr>
        </table>
    </fieldset>
    <p>
        <button class="submit_button" type="submit">Add IP Address</button>
    </p>
</form>

<h2>List of Denied IP Addresses</h2>
<table cellspacing="0" class="job-posts" id="job-posts">
<tr class="alt">
    <td>ID</td>
    <td>IP Address</td>
    <td>Date Added</td>
    <td>Action</td>                   
</tr>
{foreach from=$ips item=ips}
<tr>
    <td class="center">{$ips.id}</td>
    <td class="center">{$ips.ip_address}</td>
    <td class="center">{$ips.created_on}</td>
    <td class="center"><a href="{$BASE_URL_ADMIN}denyip/{$ips.id}/remove">Remove</a></td>
</tr>
{/foreach}
</table>


</div><!-- #content -->

{include file="footer.tpl"}
</pre>