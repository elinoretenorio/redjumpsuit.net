### Allow Social Bookmarking on Jobs

Jan 29, 2009

first, download this social bookmarking image then save it on your /img folder.

<img alt="socializer" class="size-full wp-image-45 alignnone" height="20" src="http://www.redjumpsuit.net/wp-content/uploads/2009/02/socializer.png" title="socializer" width="263"/>

then on /css/screen.css, add anywhere:

<pre lang="php">#socialjob {
width:263px;
height:20px;
margin-bottom:5px;
padding:5px;
padding-left:0px;
background:url(../img/socializer.png) no-repeat;
}

#socialjob a {
text-decoration: none;
}

#socialjob a:hover {
text-decoration: none;
background-color: transparent
}

.sociallink {
display:block;
float:left;
margin-left:5px;
height:24px;
text-decoration:none;
border:0px;
width:9px;
}</pre>

on /page_job.php, add before $template = 'job.tpl';

<pre lang="php">
$joburl = BASE_URL . 'job/' . $id . '/' . $info['url_title'] . '/';
$jobtitle = ucwords($info['title']);

$smarty-&gt;assign('socialurl', $joburl);
$smarty-&gt;assign('socialtitle', $jobtitle);
</pre>

and on /_templates/job-details.tpl, under

<pre lang="php">
<div id="job-description">
{$job.description}
</div>
</pre>

add the following lines

<pre lang="php">
{if $socialurl != NULL}
<br/>
          <div id="socialjob">
          <a class="sociallink" href="http://www.blinklist.com/index.php?Action=Blink/addblink.php&amp;Description=&amp;Url={$socialurl}&amp;Title={$socialtitle}" target="_blank" title="blinklist"></a>
          <a class="sociallink" href="http://del.icio.us/post?url={$socialurl}&amp;title={$socialtitle}" target="_blank" title="delicious"></a>
          <a class="sociallink" href="http://digg.com/submit?phase=2&amp;url={$socialurl}" target="_blank" title="digg"></a>
          <a class="sociallink" href="http://www.facebook.com/sharer.php?u={$socialurl}" target="_blank" title="facebook"></a>
          <a class="sociallink" href="http://www.furl.net/storeIt.jsp?u={$socialurl}&amp;t={$socialtitle}" target="_blank" title="furl"></a>
          <a class="sociallink" href="http://www.google.com/bookmarks/mark?op=edit&amp;bkmk={$socialurl}&amp;title={$socialtitle}" target="_blank" title="googlebookmarks"></a>
          <a class="sociallink" href="http://ma.gnolia.com/bookmarklet/add?url={$socialurl}&amp;title={$socialtitle}" target="_blank" title="magnolia"></a>
          <a class="sociallink" href="http://www.mixx.com/submit?page_url={$socialurl}" target="_blank" title="mixx"></a>
          <a class="sociallink" href="http://myweb2.search.yahoo.com/myresults/bookmarklet?u={$socialurl}&amp;t={$socialtitle}" target="_blank" title="yahoo! my web"></a>
          <a class="sociallink" href="http://reddit.com/submit?url={$socialurl}&amp;title={$socialtitle}" target="_blank" title="reddit"></a>
          <a class="sociallink" href="http://www.stumbleupon.com/submit?url={$socialurl}&amp;title={$socialtitle}" target="_blank" title="stumbleupon"></a>
          <a class="sociallink" href="http://technorati.com/faves/?add={$socialurl}" target="_blank" title="technorati"></a>
          <a class="sociallink" href="http://twitter.com/home?status={$socialurl}" target="_blank" title="twitter"></a>
          <a class="sociallink" href="https://favorites.live.com/quickadd.aspx?marklet=1&amp;mkt=en-us&amp;url={$socialurl}&amp;top=1" target="_blank" title="windows live"></a>
          </div>
{/if}
</pre>

that should do it.