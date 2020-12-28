### PayPal donate button on Drupal block

Jan 10, 2013

i was working on one of my pet projects and needed a simple PayPal donate button that i can put on a block in my site. i searched around on the drupal.org site and saw <a href="http://drupal.org/project/paypal_donate" target="_blank">PayPal Donate</a> module. However, this module creates a page and I need another module to display it as a block instead. so i wrote a small PayPal Donate Block module that i can configure with the information i need.

with this module, i can:

*   specify my donation email
*   the purpose of the donation (and when the user is logged in, it will show in the username in PayPal's donation page as well, and i can keep track which of the users has made a donation)
*   specify the currency i want to use
*   add some arbitrary text that i can add in the block


here is a screenshot of the block:


<img alt="Screen shot 2013-01-11 at 6.16.20 AM" class="aligncenter size-full wp-image-1833" height="202" src="http://www.redjumpsuit.net/wp-content/uploads/2013/01/Screen-shot-2013-01-11-at-6.16.20-AM.png" title="Screen shot 2013-01-11 at 6.16.20 AM" width="226"/>



and here is the block configuration:


<img alt="Screen shot 2013-01-11 at 6.25.37 AM" class="aligncenter size-medium wp-image-1834" height="282" src="http://www.redjumpsuit.net/wp-content/uploads/2013/01/Screen-shot-2013-01-11-at-6.25.37-AM-300x282.png" title="Screen shot 2013-01-11 at 6.25.37 AM" width="300"/>



You can download it on GitHub:

<a href="https://github.com/redjumpsuit/drupal-paypal-donate-block" target="_blank">https://github.com/redjumpsuit/drupal-paypal-donate-block</a>