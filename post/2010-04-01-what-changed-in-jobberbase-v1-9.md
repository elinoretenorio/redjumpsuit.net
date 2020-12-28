### What changed in jobberBase v1.9?

Apr 01, 2010

i'm a little out of the loop and <a href="http://code.google.com/p/jobberbase/downloads/list" target="_blank">jobberBase v1.9</a> has actually already come out a few days ago. i'm sure everyone's question now is, "how can we actually migrate to the new version?"

well, here's the first step to answering your question...know what files were changed from the last version and the new one! i don't really have time to enumerate the files one by one and tell you what changed, what's new or what disappeared. so instead here's a screenshot for your reference. the changes are indicated by colors:

__<span style="color: #000000;">black = nothing has changed
</span><span style="color: #ff0000;">red = file was changed in the new version</span>__
<span style="color: #0000ff;">__blue = new file that did not exist in the previous version__</span>

this should help guide you migrating your code from previous version to the new one. do let me know if you did find this useful!

the config file (config.php) was moved on its own folder
![config](http://www.redjumpsuit.net/wp-content/uploads/2010/04/config.PNG "config")

the root folder
![root](http://www.redjumpsuit.net/wp-content/uploads/2010/04/root.PNG "root")

the includes folder
![includes](http://www.redjumpsuit.net/wp-content/uploads/2010/04/includes.PNG "includes")

the templates folder
![templates](http://www.redjumpsuit.net/wp-content/uploads/2010/04/templates.PNG "templates")

note that aside from the default template, hireme theme was also included in this release.

the cron job was moved to its own folder
![tools](http://www.redjumpsuit.net/wp-content/uploads/2010/04/tools.PNG "tools")

the js uploads api folder
![js](http://www.redjumpsuit.net/wp-content/uploads/2010/04/js-uploads-api.PNG "js")

the admin folder
![admin](http://www.redjumpsuit.net/wp-content/uploads/2010/04/admin.PNG "admin")

the admin js folder
![js](http://www.redjumpsuit.net/wp-content/uploads/2010/04/admin-js.PNG "js")

the admin templates folder
![admin-templates](http://www.redjumpsuit.net/wp-content/uploads/2010/04/admin-templates.PNG "admin-templates")