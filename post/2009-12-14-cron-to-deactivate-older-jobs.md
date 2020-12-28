### Cron to Deactivate Older Jobs

Dec 14, 2009

if you are using the cron maintenance job default on jobberBase, you will find out that the active jobs are actually not deleting from the system. and the reason for that is the cron job is only deleting temporary jobs (is_temp=1) and not the active jobs.


<pre lang="php">
// delete temporary posts older than 2 days
$janitor-&gt;DeleteTemporaryJobs();
</pre>



To automatically delete older jobs using cron, just create another function in the class.Maintenance.php file with this:


<pre lang="php">
// delete active posts older than 40 days
public function DeleteActiveJobs()
{
    global $db;
    $sql = 'DELETE FROM jobs WHERE DATEDIFF(NOW(), created_on) &gt; 40 AND is_temp = 0 AND is_active = 1';
    $db-&gt;Execute($sql);
}
</pre>



and add this function on the cron_maintenance.php file


<pre lang="php">
// delete active posts older than 40 days
$janitor-&gt;DeleteActiveJobs();
</pre>



Check out my related post to automatically expire jobs with notification [here](http://www.redjumpsuit.net/2009/01/21/send-notification-when-ads-are-expiring/).
