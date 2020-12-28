### Extend Ads in jB 1.7 (w/ notification)

May 29, 2009

the extend job function actually already exists since 1.6 (and even earlier i think) but i haven't seen anyone post a tutorial on how to use the function to actually extend jobs. when i say extend, what the function does is just to update the "created_on" field to "NOW()" on the jobs table. the code looks like this in /_includes/class.Job.php

<pre lang="php">// Extend a post for 30 days
public function Extend()
{
    global $db;
    $sql = 'UPDATE jobs SET created_on = NOW(), is_active = 1 WHERE id = ' . $this-&gt;mId;
    if ($db-&gt;query($sql))
    {
        return true;
    }
    else
    {
        return false;
    }
}</pre>

this is how it will look like on the admin page

<img alt="extend" class="aligncenter size-full wp-image-293" height="359" src="http://www.redjumpsuit.net/wp-content/uploads/2009/05/extend.png" title="extend" width="463"/>

so let me show you how to use this function. first is to extract this file and save the images in your /img folder

<a href="http://www.redjumpsuit.net/wp-content/uploads/2009/05/extend.zip" target="_blank">extend.zip</a>

then, open up config.php on the root folder and add this anywhere

<pre lang="php">// number of days til extension is active
define('EXTEND_DAYS', 27);</pre>

open up /_includes/translations.ini and add under [js]

<pre lang="php">extend_job_confirmation_question = "Are you sure you want to extend this post?"</pre>

open up /js/functions.js and before the line

<pre lang="php">Delete: function(url, job_id)</pre>

add the following (watch out for the comma (,) at the end of this code)

<pre lang="php">Extend: function(url, job_id)
{
    if(confirm(Jobber.I18n.js.extend_job_confirmation_question))
    {
        $.ajax({
          type: "POST",
          url: url,
          data: "job_id=" + job_id,
          success: function(msg) {
            if (msg != "0")
                {
                    var currentJobId = 'item'+job_id;
                    $("#"+currentJobId).css({ display: "none" });
                }
          }
        });
    }
    else
        return false;
},</pre>

next is to open up /admin/_templates/posts-loop.tpl and update this file. look for the line that contains the following

<pre lang="php"><a href="javascript:void(0);" onclick="Jobber.Delete('{$BASE_URL_ADMIN}delete/', {$job.id});" title="delete"><img alt="delete" src="{$BASE_URL}img/icon-delete.png"/></a></pre>

before this line, add the following code

<pre lang="php">{if $job.days_old &gt;= $smarty.const.EXTEND_DAYS &amp;&amp; $job.is_active == 1}
    <a href="javascript:void(0);" onclick="Jobber.Extend('{$BASE_URL_ADMIN}extend/', {$job.id});" title="extend"><img alt="extend" src="{$BASE_URL}img/extend_yes.png"/></a>
{else}
    <img alt="not for extension yet" src="{$BASE_URL}img/extend_no.png" title="not for extension yet"/>
{/if}</pre>

on /admin/index.php, under this block

<pre lang="php">case 'home':
    if(!isset($_SESSION['AdminId']))
    {
        redirect_to(BASE_URL);
        exit;
    }
    require_once 'page_home.php';
    $flag = 1;
    break;</pre>

add these lines

<pre lang="php">// extend ads
case 'extend':
    if(!isset($_SESSION['AdminId']))
    {
        redirect_to(BASE_URL);
        exit;
    }
    require_once 'page_extend.php';
    $flag = 1;
    break;</pre>

next is to create a file called page_extend.php on the /admin folder and add the following code

<pre lang="php">
<?php
    $j = new Job($_POST['job_id']);
    if ($j->Extend())
    {
        $m = new Postman();
        $m-&gt;MailAdExtend($j-&gt;GetInfo(), BASE_URL_ORIG);
        echo 1;
    }
    else
    {
        echo "0";
    }
    exit;
?&gt;
</pre>

and finally to send notification when the ad is extended, open up
/_includes/class.Postman.php and add this function before the last }

<pre lang="php">// Send mail when ad is extended
public function MailAdExtend($data, $url=BASE_URL)
{
    $subject = 'Your ad on ' . SITE_NAME . ' was extended';
    $msg = "Hello! :)\n\n";
    $msg .= "Your ad was extended and is available at: " . $url . "job/" . $data['id'] . "/" . $data['url_title'] . "/";
    $msg .= "\n\n---\nEdit it: " . $url . "post/" . $data['id'] . "/" . $data['auth'] . "/";
    $msg .= "\nDeactivate it: " . $url . "deactivate/" . $data['id'] . "/" . $data['auth'] . "/";
    $msg .= "\n\n---\n\nThank you for using our service!\nThe " . SITE_NAME . " Team";

    if ($data['poster_email'] != '' &amp;&amp; validate_email($data['poster_email']))
    {
        mail($data['poster_email'], $subject, $msg, "From: " . SITE_NAME . " &lt;" . NOTIFY_EMAIL . "&gt;");
    }
}
</pre>