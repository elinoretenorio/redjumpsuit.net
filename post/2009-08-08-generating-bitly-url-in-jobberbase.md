### Generating bit.ly URL in jobberBase

Aug 08, 2009

i was searching for a php class that interacts easily with the bit.ly API and found a really good one here: <http://bit.ly/oDwGX>

after studying the code, i thought of a way to integrate this with jobberBase and dynamically create a bit.ly URL when jobs are posted using the PHP class that i found.

just follow the instructions below.

1) create a bit.ly account (if you do not have yet) and grab your API key

2) download the bit.ly API interaction class found in the website i mentioned <http://bit.ly/oDwGX> and save it as 'bitly.php' on your /_includes/ folder

3) run this sql script on your 'jobs' table (be sure to make a backup of your table first!)

<pre lang="php">
ALTER TABLE `jobs` ADD `bitly` VARCHAR( 100 ) NULL;
</pre>

4) open up your root's 'config.php' and below this line:

<pre lang="php">
require_once '_includes/smarty/libs/Smarty.class.php';
</pre>

add this line:

<pre lang="php">
// bit.ly class
require_once '_includes/bitly.php';
</pre>

5) open up the bitly.php class you saved on /_includes/ folder and look for this function:

<pre lang="php">
public function shorten($url, $returnHash = false)
{
...
}
</pre>

below it, paste this new code:

<pre lang="php">
/**
Shorten URL for jobberBase
author: RedJumpsuit <myredjumpsuit@gmail.com>
url: www.redjump.co.cc
**/
public function shorten_jobber($id,$url,$returnHash=false)
{
	global $db;
	$sql = 'SELECT bitly
			FROM jobs
			WHERE id = '. $id;
	$result = $db-&gt;query($sql);
	$row = $result-&gt;fetch_assoc();
	if ($row['bitly'] == '')
	{
		$bitlyurl = 'http://api.bit.ly/shorten?version='.$this-&gt;version.'&amp;longUrl='.$url.'&amp;login='.$this-&gt;login.'&amp;apiKey='.$this-&gt;apikey.'&amp;format='.$this-&gt;format;
		if ( $this-&gt;getResult($bitlyurl) !== false )
		{
			$sqlu = 'UPDATE jobs SET bitly = "'. $this-&gt;results['shortUrl'] .'" WHERE id = '. $id;
			$db-&gt;execute($sqlu);
			if ( $returnHash == true ) {
				return array('shortUrl' =&gt; $this-&gt;results['shortUrl'], 'hash' =&gt; $this-&gt;results['hash']);
			} else {
				return $this-&gt;results['shortUrl'];
			}
		}
		return false;
	}
}
</myredjumpsuit@gmail.com></pre>

6) open up 'page_verify.php' on your root folder and add this:

<pre lang="php">
// generate bit.ly URL for jobberBase
// author: RedJumpsuit <myredjumpsuit@gmail.com>
// url: www.redjump.co.cc
$joburl = BASE_URL . 'job/' . $id . '/';
$bitly = new bitly('redjumpsuit', 'your API key goes here');
$bitly-&gt;shorten_jobber($id,$joburl);
</myredjumpsuit@gmail.com></pre>

before this block:

<pre lang="php">
$smarty-&gt;assign('job', $jobs);
$html_title = stripslashes($jobs['title']) . ' at ' . stripslashes($jobs['company']) . ' / ' . SITE_NAME;
$template = 'publish-verify.tpl';
</pre>

7) to enable this in the admin panel, open up page_edit_post.php on your /admin/ folder and replace this line:

<pre lang="php">
$job-&gt;Create($data);
</pre>

with this:

<pre lang="php">
$id = $job-&gt;Create($data);

// generate bit.ly URL for jobberBase
// author: RedJumpsuit <myredjumpsuit@gmail.com>
// url: www.redjump.co.cc
$joburl = BASE_URL_ORIG . 'job/' . $id . '/';
$bitly = new bitly('redjumpsuit', 'your API key goes here');
$bitly-&gt;shorten_jobber($id,$joburl);
</myredjumpsuit@gmail.com></pre>

now it's up to you to grab the bit.ly url from the 'jobs' table, should be easy from this point on right?

if you like my post, use the donate button below :)