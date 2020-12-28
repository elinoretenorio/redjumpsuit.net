### Control Application Interval Limit

Feb 17, 2009

Hi, here is a short trick to control application interval limit on your jobberBase website.

on /config.php, add anywhere:

<pre lang="php">
// control application interval limit
define('APPLY_INTERVAL', 1);
</pre>

on /_includes/class.JobApplication.php, comment these lines:

<pre lang="php">
$sql = 'SELECT id
          FROM job_applications
          WHERE ip = "' . $ip . '" AND NOW() &lt; DATE_ADD(created_on,INTERVAL 10 MINUTE)';
</pre>

then replace with this:

<pre lang="php">
// set application interval using APPLY_INTERVAL from the /config.php
$sql = 'SELECT id
         FROM job_applications
         WHERE ip = "' . $ip . '" AND NOW() &lt; DATE_ADD(created_on,INTERVAL ". APPLY_INTERVAL ." MINUTE)';</pre>

Now, anytime you want to change the interval limit of applications sent through your site, you can do so by just updating the /config.php file