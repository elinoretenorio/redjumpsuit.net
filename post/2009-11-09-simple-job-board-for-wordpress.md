### Simple Job Board for WordPress

Nov 09, 2009

I recently launched a WordPress website called __<a href="http://www.awesomejobsites.com" target="_blank">AwesomeJobSites</a>__

<p style="text-align: center;"><a href="http://www.awesomejobsites.com" target="_blank"><img alt="AJS Logo" class="aligncenter" src="http://www.redjumpsuit.net/wp-content/uploads/2009/11/ajs-logo.png"/></a></p>



and I wanted to add a simple job board in the site for those who are looking for developers and designers specializing on job board customizations. Of course the first thing I thought of was to use jobberBase (why wouldn't I, right?) but then again that will mean managing multiple admins since it will be a separate installation and customizing the templates, etc. So even if I have never tried customizing a WordPress, I ventured into researching how I could implement a very simple job board for my site. The first things I tried to resolve are the following:

1) Allow people to submit a job advertisement without registering for an account
2) Create a PayPal payment link so that job posters can pay for their ads
3) Moderate the submitted job ads and approve the job post when payment is received from PayPal (no IPN integration or anything, just manual approval)
4) Display the content separately from all the website content (meaning the job post should not appear on any of the posts or categories)

After spending several hours researching on the web, I was able to figure out the 4 things I wanted to do for the simple job board that I wanted (for the most part of this customization I used <a href="http://wordpress.org/extend/plugins/tdo-mini-forms/" target="_blank">TDO Mini Forms</a>, <a href="http://wordpress.org/extend/plugins/advanced-category-excluder/" target="_blank">Advanced Category Excluder</a> and hacking some of the PHP files in the theme that I was using.) There were a few other things I want to do after this, but so far I was pretty pleased with how the job board turned out.

Here's how the __<a href="http://awesomejobsites.com/post-a-job/" target="_blank">Post a Job</a>__ page looks like:

<p style="text-align: center;"><img alt="Post a Job" class="aligncenter" src="http://www.redjumpsuit.net/wp-content/uploads/2009/11/post-a-job.png"/></p>