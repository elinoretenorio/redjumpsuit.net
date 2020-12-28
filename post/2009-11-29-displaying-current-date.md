### Displaying Current Date

Nov 29, 2009

if you want to display the current date on your website (as shown on <a href="http://www.obitwaryo.net" target="_blank">obitwaryo.net</a>) you can use this script.


<pre lang="php">
{$smarty.now|date_format:"%A, %B %e, %Y"}
</pre>



and it will show:


<pre lang="php">
Sunday, November 29, 2009
</pre>



you can place it on header.tpl or footer.tpl or sidebar.tpl whichever you prefer.
