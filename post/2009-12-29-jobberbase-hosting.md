### jobberBase Hosting Tips

Dec 29, 2009

the last few weeks i have been working a lot with people who have had difficulties and issues installing jobberBase on their hosting services. supposedly, jobberBase should work as it is without a hitch. i have done several hundred different installations and i haven't encountered many major issues than with the last few days.

i am here to save you all the headaches of installing jobberBase by recommending the Top 2 Hosting services that I have personal experience with installing numerous jobberBase websites without a problem.

<a href="http://www.awesomejobsites.com/recommend.php?who=inmotion" target="_blank">__InMotion__</a><a href="http://www.awesomejobsites.com/recommend.php?who=inmotion"><img alt="In Motion" class="alignnone" height="66" src="http://www.redjumpsuit.net/wp-content/uploads/2009/12/inmotion.gif" title="In Motion" width="210"/></a>

*   Fast email with SPAM blocking
*   Different levels of hosting so you can grow
*   90 day money back guarantee (best I know of)
*   Very reliable with 99.9% Uptime
*   Easy to use control panel and a premium web site builder
*   Unlimited disk space and Unlimited monthly traffic (Business plan)
*   Very helpful, probably best known for top technical support

<a href="http://www.awesomejobsites.com/recommend.php?who=justhost" target="_blank">__JustHost__</a>
<a href="http://www.awesomejobsites.com/recommend.php?who=justhost"><img alt="Just Host" height="62" src="http://www.redjumpsuit.net/wp-content/uploads/2009/12/justhost.png" title="Just Host" width="256"/></a>

*   FREE Domain For Life &amp; FREE Instant Setup
*   No Hidden Fees
*   Unlimited GBs of Web Space
*   Unlimited GBs of Transfer
*   Unlimited Domain Hosting
*   Unlimited E-Mail Accounts
*   Unlimited MySQL Databases
*   SSL, FTP, Stats CGI, Ruby (RoR), Perl, PHP, MySQL


if you do sign-up on any of these hosting services, let me know and i will even do a free installation for you<span style="color: #ff0000;">\*</span>
<span style="color: #000000; font-weight: bold; font-size:1.2em;">Installing jobberBase on a sub-directory called '/jobs'</span>
this has been a very common issue on people installing jobberBase on a sub-directory called '/jobs', this is an issue because jobberBase uses the '/jobs' URL (not directory) when publishing jobs and this gets in conflict when the folder also has the same name of 'jobs'. to resolve the issue you can try the following:

on your site's __/jobs/.htaccess__ file, update the content to this:


<pre lang="php">
# AddType x-mapp-php5 .php
# AddHandler x-mapp-php5 .php

RewriteEngine on
Options +FollowSymlinks

RewriteBase /jobs

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php [L]

ErrorDocument 404 /page-unavailable/

<files "\.tpl$"="" ~="">
order deny,allow
allow from none
deny from all
</files>
</pre>



and on your site's __/jobs/admin/.htaccess__ file, update the content to this:


<pre lang="php">
RewriteEngine on
Options +FollowSymlinks

RewriteBase /jobs/admin

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php [L]

ErrorDocument 404 /page-unavailable/
</pre>



personally, i __DO NOT__ recommend installing jobberBase on a '/jobs' sub-directory as the URL the jobs will be on will be pretty redundant, like:

<code><strong>http://www.somewebsite.com/jobs/job/1/web-developer-needed-at-foo-inc/</strong></code>

instead if you really want to use the 'jobs' word, i'd recommend installing jobberBase on a sub-domain instead, like:

<code><strong>http://jobs.somewebsite.com/job/1/web-developer-needed-at-foo-inc/</strong></code>
_<span style="color: #ff0000;">\*</span> free installation is offered only when the links from this site is used to sign-up for a hosting plan._