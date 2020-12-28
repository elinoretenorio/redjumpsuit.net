### Send Notification When Ads are Expiring

Jan 21, 2009

I know some of you are wanting to notify your job posters when their ads are about to expire (if you are offering a premium service of some sort) so I thought I'd share this simple mod with all of you.

On /_includes folder, on class.Maintenance.php, comment out the block

<pre lang="php">
    // deactivate jobs older than 30 days
    /**
    public function DeactivateJobs()
    {
         global $db;
         $sql = 'UPDATE jobs SET is_active = 0 WHERE DATEDIFF(NOW(), created_on) &gt; 30 AND is_active = 1';
         $db-&gt;Execute($sql);
    }
  **/
</pre>

then add below it:

<pre lang="php">
public function DeactivateJobs()
      {
      $postMan = new Postman();

      global $db;

      $notifmin = 27; //the day when you want notification to be sent prior to the ad expiring
      $notifmax = 30; //the day the ad actually expires
      $dayscount = $notifmax - $notifmin;

      $sql = 'SELECT * FROM jobs WHERE DATEDIFF(NOW(), created_on) &gt;= '. $notifmin .' AND is_active = 1';
      $row = $db-&gt;QueryArray($sql);
      print_r($row); // this will print the array of the ad that was deactivated
      foreach ( $row as $rows )
          {

          $ademail = trim(strtolower($rows['poster_email']));
          $adname = trim($rows['title']);
          $adcomp = trim($rows['company']);
          $adurl = BASE_URL . "job/" . $rows['id'] . "/";

          $sqlu = 'UPDATE jobs SET is_active = 0 WHERE DATEDIFF(NOW(), created_on) &gt; '. $notifmax .' AND is_active = 1 AND id = ' . $rows['id'] ;
          $db-&gt;Execute($sqlu);
          $postMan-&gt;MailExpiringAd($ademail, $adname, $adcomp, $adurl, $dayscount);

          }
  }
</pre>

on /_includes, on class.Postman.php, add:

<pre lang="php">
// mail client when ad is expiring
    public function MailExpiringAd($email, $name, $comp, $url, $days)
    {
        $msg = EMAIL_HEADER . "\n\nWe'd like to inform you that your ad &lt;". $name ."&gt;: \n\n". $url ."\n\nunder the Company &lt;". $comp ."&gt; is expiring in ". $days ." days.";
        $msg .= "\n\nThank you for using our service!";
        $msg .= "\n\n---\n" . EMAIL_FOOTER ;

        $subject = "Your ad in " . SITE_NAME . " is expiring in ". $days ." days.";

        if (mail($email, $subject, $msg, "From: " . SITE_NAME . " &lt;" . NOTIFY_EMAIL ."&gt;"))
        {
            return true;
        }
        else
        {
            return false;
        }
    }
</pre>

on /config.php add:

<pre lang="php">
define('EMAIL_HEADER','Welcome to RedJumpsuit\'s Site!');
define('EMAIL_FOOTER','http://www.redjumpsuit.net');
</pre>

on cron_maintenance.php add:

<pre lang="php">
require_once '_includes/class.Postman.php';
</pre>

and make sure that this line exists:

<pre lang="php">
    // deactivate jobs older than 30 days + notification
    $janitor-&gt;DeactivateJobs();
</pre>

and lastly, you must set-up a cron job to run the cron_maintenance.php file or else the script <del datetime="2009-02-27T12:54:45+00:00">wont work</del> (only partially true) it will still work but you have to access you cron_maintenance.php file from your browser.

the only real purpose of the cron job is to automate running scripts for you. the php way is to just actually run/access the php file. if your cron_maintenance.php is on your root folder, then you can just access it like http://yourjobberbase.site/cron_maintenance.php

would be good though to name your cron_maintenance.php file into something unique to you (like cron_maintenance_xxxxxx.php) where x represents values (like a code or pin) that only you would know, so that not just anybody could run your cron_maintenance.php file