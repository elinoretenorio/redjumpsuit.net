### Experimenting on Full-Text Search

Jul 15, 2009

today i had time to look closely in to the Search() function on 'class.Job.php', and i wanted to see how Full-Text Search can improve the search performance of jobberBase when searching for a couple thousand jobs. i don't have that much data to test yet so i am hoping the good people reading my posts can help me with testing and measuring any improvement.

first things first. back up your 'jobs' and 'cities' table, you'll have a fall back if anything breaks.

so let's start with running these sql lines on your 'jobs' and 'cities' tables in your phpmyadmin:

<pre lang="php">
ALTER TABLE `jobs` ADD FULLTEXT (
`title` ,
`description` ,
`company` ,
`outside_location`
);

ALTER TABLE `cities` ADD FULLTEXT (
`name`
);
</pre>

then open up /_includes/class.Job.php and comment out the whole 'public function Search($keywords) {}' block.

then add this below it:

<pre lang="php">
// Search for jobs
public function Search($keywords)
{
     global $db;
     $jobs = array();

     $spchars = array(",",";",":");
     $keywords = str_replace($spchars," ", trim($keywords));

     $sql = 'SELECT a.id AS id
                    FROM jobs a, cities b
                    WHERE a.city_id = b.id
                    AND a.is_temp = 0 AND a.is_active = 1
                    AND MATCH (a.title, a.description, a.company, a.outside_location, b.name)
                    AGAINST ("'. $keywords .'" IN BOOLEAN MODE )';
        $result = $db-&gt;query($sql);

        while ($row = $result-&gt;fetch_assoc())
        {
            $current_job = new Job($row['id']);
            $jobs[] = $current_job-&gt;GetBasicInfo();
        }
        return $jobs;
    }
</pre>

i would like to hear your feedback! post a comment whether or not this actually improved the search performance in your jobberBase website. do note that this is merely an experiment. you've been warned! ;)