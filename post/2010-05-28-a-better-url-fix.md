### A Better URL Fix

May 28, 2010

this is my second take to fixing the URL problem in jobberBase. i originally posted a fix back in November called [The elusive http://](http://www.redjumpsuit.net/2009/11/26/the-elusive-http/) but later on realized that it would be tiring to keep adding the script/code everytime you have a URL field that you want to fix. so now i created a function instead, that you would just call everytime you want a URL field fixed.

open /_includes/functions.php and add this function


<pre lang="php">
function fixurl($url)
{
	if ($url == '') {
		$fixedurl = '';
	} elseif ((substr($url,0,7) == "http://" OR substr($url,0,7) == "https://") AND strpos($url, ".")) {
		$fixedurl = $url;
	} elseif (substr($url,0,7) != "http://" AND strpos($url, ".")) {
		$fixedurl = 'http://'. $url;
	}
	return $fixedurl;
}
</pre>



how to use this? as an example, you can open /_includes/class.Job.php and look for the blocks like


<pre lang="php">
public function Create($params)
{
...
}
</pre>



and use it like this


<pre lang="php">
"' . fixurl($params['url']) . '",
</pre>



and in


<pre lang="php">
public function Edit($params)
{
...
}
</pre>



and you will use the function like this


<pre lang="php">
url = "' . fixurl($params['url']) . '",
</pre>



note: this should work for __all__ jobberBase versions.


<a href="http://www.pledgie.com/campaigns/11082" target="_blank">

<img alt="Support RedJumpsuit and make a donation at www.pledgie.com !" border="0" src="http://www.pledgie.com/campaigns/11082.png?skin_name=chrome"/>

</a>