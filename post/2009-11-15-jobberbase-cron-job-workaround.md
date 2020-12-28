### jobberBase cron job workaround

Nov 15, 2009

if you find yourself wondering why the heck the jobberBase cron job doesn't work on a linux hosting, you are not alone! i've struggled with this cron job issue for a while now. well, finally i was able to sit down and try to figure out how to work around this bug. yes i say it's a bug coz if i use an online cron job service, the php file executes just fine but if it's run through the cron job on a linux hosting, it just flatly fails.

the solution is in the config.php file. i would recommend creating another instance of the config.php (i renamed mine to config2.php) and look for the lines below.

replace this

<pre lang="php">
die('[config.php] Cannot determine APP_MAIN_DIR, please set manual and comment this line');
</pre>



with this:

<pre lang="php">
//die('[config.php] Cannot determine APP_MAIN_DIR, please set manual and comment this line');
$app_main_dir = rtrim(dirname($_SERVER['PHP_SELF']),'/\\');	
define('_APP_MAIN_DIR', $app_main_dir);
</pre>



then replace this:

<pre lang="php">
die('[config.php] Cannot determine BASE_URL, please set manual and comment this line');
</pre>



with this:

<pre lang="php">
//die('[config.php] Cannot determine BASE_URL, please set manual and comment this line');
define ('BASE_URL', 'http://yourwebsitename.com/');
</pre>



now how's that for a workaround? :)
