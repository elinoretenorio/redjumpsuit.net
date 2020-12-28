### jobberBase Templating Technique

Jul 20, 2009

As promised, here's a templating technique I created to allow for switching from templates to templates in jobberBase. This came up when I started working on importing CSS/xHTML templates to jobberBase lately and started thinking of a way to switch easily from the default to the template I was working on.

This is fairly simple and I am assuming a lot of you are familiar with jobberBase already.

1. Open your 'config.php' file on your root folder.

Under this block:

<pre lang="php">
    // MySQL + misc settings for local environment
    if ($_SERVER['SERVER_NAME'] == 'localhost')
    {
        define('DB_HOST', 'localhost');
        define('DB_USER', 'root');
        define('DB_PASS', '');
        define('DB_NAME', 'jobberbase');
        define('LOCATION', 'local');
        define('ENVIRONMENT', 'dev');
    }
    // MySQL + misc settings for production environment
    else
    {
        define('DB_HOST', 'localhost');
        define('DB_USER', 'root');
        define('DB_PASS', '');
        define('DB_NAME', 'jobberbase');
        define('LOCATION', 'online');
        define('ENVIRONMENT', 'prod');
    }
</pre>

Add this:

<pre lang="php">
define('SKIN', ''); // if empty, the default jobberBase template will be used
</pre>

Then replace this block:

<pre lang="php">
    // Setup Smarty
    $smarty = new Smarty();
    $smarty-&gt;template_dir = APP_PATH . '_templates/';
    $smarty-&gt;compile_dir = APP_PATH . '_templates/_cache/';
</pre>

With this block:

<pre lang="php">
    // Setup Smarty
    $smarty = new Smarty();

    if (SKIN == '')
    {
        $smarty-&gt;template_dir = APP_PATH . '_templates/';
        $smarty-&gt;compile_dir = APP_PATH . '_templates/_cache/';
    }
    else
    {
        $smarty-&gt;template_dir = APP_PATH . '_templates/'. SKIN .'/';
        $smarty-&gt;compile_dir  = APP_PATH . '_templates/'. SKIN .'/_cache/';
    }
</pre>

2. Save your new template under /_templates/

3. Update the line on config.php:

<pre lang="php">
    define('SKIN', '');
</pre>

With the new template you created or already have. You MUST use the name of the folder as your 'skin' name.

For example:

<pre lang="php">
define('SKIN', 'emplode'); // the files of this theme is saved on /_templates/emplode/
</pre>

That's the part that takes care of pointing to the correct template.

The second part is the conventions I suggest you use. I am again assuming you are starting your new template off of the default jobberBase template (what I'd do is copy all the files inside the /_templates folder including the /_templates/cache/ folder and save it to a new folder inside /_templates/__my new template__)

1. The file location and naming convention I would suggest (for your new template) are:

__Stylesheet__
- name it style.css and put it on /_templates/__your template name__/style.css

__Images__
- put it inside the folder on /_templates/__your template name__/img/
- call it from your stylesheet as ('img/...')

__Header template__
-  Open up /_templates/your template name/header.tpl and replace these lines:

<pre lang="php">
<link href="{$BASE_URL}css/screen.css" media="screen" rel="stylesheet" type="text/css"/>
<link href="{$BASE_URL}css/print.css" media="print" rel="stylesheet" type="text/css"/>
</pre>

with these lines:

<pre lang="php">
<link href="{$BASE_URL}_templates/{$smarty.const.SKIN}/style.css" media="screen" rel="stylesheet" type="text/css"/>
<link href="{$BASE_URL}_templates/{$smarty.const.SKIN}/print.css" media="print" rel="stylesheet" type="text/css"/>
</pre>

I found this technique very helpful in developing new templates for jobberBase and having the option to revert back to the default template by the changing the line in the config file.

Go ahead and try it for yourself!