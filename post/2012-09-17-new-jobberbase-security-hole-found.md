### New jobberBase security hole found

Sep 17, 2012

<img alt="danger" class="alignleft size-thumbnail wp-image-1785" height="150" src="http://www.redjumpsuit.net/wp-content/uploads/2012/09/danger-150x150.jpg" title="danger" width="150"/>

someone from the jobberBase forum recently reported a security hole that i was able to validate as 100% accurate. 

i will not go into details (in case someone takes advantage of this vulnerability before the job board owners can update their sites) and will just provide a temporary fix so you can immediately protect your site. note that this is a temporary solution and not the official fix released by jobberBase.

open /_templates/default/publish-confirmation.tpl and look for this block:


<pre lang="php">
<h4>{$translations.publish.options_title}</h4>
<p>
    {$translations.publish.options_info}:
</p>
<ul>    
    <li><a href="{$BASE_URL}post/{$CURRENT_ID}/{$auth}/" title="{$translations.publish.edit}">» {$translations.publish.edit}</a></li>
    <li><a href="{$BASE_URL}deactivate/{$CURRENT_ID}/{$auth}/" title="{$translations.publish.deactivate}">» {$translations.publish.deactivate}</a></li>
</ul>
</pre>



this is the offending block and should be removed temporarily. you should backup this file and store it somewhere so that when the official fix is released, you can restore the file you modified.
