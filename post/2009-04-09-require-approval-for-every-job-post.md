### Require Approval for Every Job Post

Apr 09, 2009

on /_includes/class.Job.php, comment a line in this function:

<pre lang="php">
public function Publish()
{
   global $db;
   if ($this-&gt;CheckPosterEmail())
     {
      // $sql = 'UPDATE jobs SET is_temp = 0, is_active = 1 WHERE id = ' . $this-&gt;mId;
     }
   else
      {
      $sql = 'UPDATE jobs SET is_temp = 0, is_active = 0 WHERE id = ' . $this-&gt;mId;
      }
    $db-&gt;query($sql);
}
</pre>

and replace it with:

<pre lang="php">
$sql = 'UPDATE jobs SET is_temp = 0, is_active = 0 WHERE id = ' . $this-&gt;mId;
</pre>

This would force all job postings to require admin approval instead of checking whether the poster's email has been approved before.