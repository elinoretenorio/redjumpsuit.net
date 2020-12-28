### protect your jobberBase site from XSS and SQL Injection attacks!

Dec 16, 2012

just want to get the word out that there are scanner hacking sites out there that are able to penetrate jobberBase's uploads and other writeable directories.

here are some ways you can prevent your site from being the next target:

1) lock down your uploads directory (if you opted to save the resumes when your applicants apply.) you can add an .htaccess on your /uploads dir with these lines:

`` 
Order Deny,Allow 
Deny from All
&lt;Files "index.php"&gt; 
     Order Deny,Allow 
     Allow from All 
&lt;/Files&gt;
&lt;FilesMatch "\.(rtf|odt|txt|docx|doc|pdf)$"&gt;
    Order Deny,Allow 
    Allow from All
&lt;/FilesMatch&gt;
 ``

This will ensure that only index.php and the listed file extensions above are allowed to be accessed on that directory.

2) DON'T ever use a MySQL root account as your database login account in the config settings. Create a new database user with limited privileges (no alter, execute, drop)

3) if your phpMyAdmin is accessible through your domain, find a way to password protect (via .htaccess and .htpasswd) before the phpMyAdmin is reached.

4) One of the most important: check with your hosting provider and confirm (or ask them) to disable the eval() language construct on your hosting account. majority of the nasty hacking attacks happening everywhere are done through eval() execution of base64 encoded shell commands.