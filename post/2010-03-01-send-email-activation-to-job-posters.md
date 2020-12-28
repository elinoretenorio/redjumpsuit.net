### Send Email Activation to Job Posters

Mar 01, 2010

here's a quick and easy way to send your first time job posters their own activation email links:

on /_includes/class.Postman.php, update the function


<pre lang="php">
public function MailPublishPendingToUser($poster_email) {
...
}
</pre>



to this


<pre lang="php">
// Send mail to user when posting first time (thus the post needs to be moderated)
    public function MailPublishPendingToUser($poster_email,$id=false,$auth=false)
    {
        $msg = "Hello! :)\n\n";
        $msg .= "We apologize for the inconvenience, but since this is the first time you post with this e-mail address, we need to manually verify it.";
        if (isset($id) &amp;&amp; $id &gt; 0)
        {
            $msg .= "\n\nVerify Ad: " . BASE_URL . "activate/" . $id . "/" . $auth . "/";
            $msg .= "\n";
        }
        $msg .= "\nThank you for your patience, as the ad should be published ASAP. We'll send you an e-mail when that happens!";
        $msg .= "\n\nFrom now on, every ad you post with this e-mail address will instantly be published.";
        $msg .= "\n\n---\n\nThank you for using our service!\nThe Team";
        
        $subject = "Your ad on " . SITE_NAME;
        
        if ($poster_email != '' &amp;&amp; validate_email($poster_email))
        {
            $mailer = $this-&gt;getConfiguredMailer();
            
            $mailer-&gt;SetFrom(NOTIFY_EMAIL, SITE_NAME);
            $mailer-&gt;AddAddress($poster_email);
            $mailer-&gt;Subject = $subject;
            $mailer-&gt;Body = $this-&gt;nl2br($msg);
            $mailer-&gt;AltBody = $msg;
            
            $mailer-&gt;Send();
        }
    }

</pre>



and on /page_publish.php, update this line


<pre lang="php">
$postMan-&gt;MailPublishPendingToUser($job-&gt;mPosterEmail); 
</pre>


to this

<pre lang="php">
$postMan-&gt;MailPublishPendingToUser($job-&gt;mPosterEmail,$id,$job-&gt;mAuth);
</pre>


this will send the first time job poster a link to activate his own ad.