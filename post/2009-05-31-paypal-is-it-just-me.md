### PayPal website down, is it just me?

May 31, 2009

is it just me or is paypal.com inaccessible somehow? i first checked the site today (May 31) at 9:30AM (GMT+8) and could not pull up the paypal.com website. between this morning, i checked the website again several times but still to no avail. now it's 9:48PM (GMT+8) and i still could not access their website.

based on www.who.is

http://www.who.is/tools/ping/paypal.com/

<pre lang="php">
PING paypal.com (66.211.169.2) 56(84) bytes of data.

Request timed out.
Request timed out.
Request timed out.
Request timed out.

Ping statistics for 66.211.169.65:
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

--- paypal.com ping statistics ---
10 packets transmitted, 0 received, 100% packet loss, time 4582ms
</pre>

and on network-tools.com

<pre lang="php">
66.211.169.65 is from United States(US) in region North America

TraceRoute to 66.211.169.65 [paypal.com]

Hop	(ms)	(ms)	(ms)		IP Address	Host name
1	18	20	17		72.249.134.177	-
2	7	6	6		8.9.232.73	xe-5-3-0.edge3.dallas1.level3.net
3	9	10	8		4.68.19.140	ae-3-89.edge2.dallas3.level3.net
4	8	18	9		4.68.63.50	qwest-level3-xe.dallas3.level3.net
5	35	31	25		67.14.24.38	dvr-edge-11.inet.qwest.net
6	32	30	41		67.148.54.10	67-148-54-10.dia.static.qwest.net
7	27	27	28		10.1.1.182	-
8	Timed out	Timed out	Timed out			-
9	Timed out	Timed out	Timed out			-
10	Timed out	Timed out	Timed out			-
11	Timed out	Timed out	Timed out			-

Trace aborted.</pre>

i also checked the PayPal Developer Community's [Live Site Status](http://www.pdncommunity.com/t5/blogs/blogpage/blog-id/mts_updates) and there was a reported intermittent timeout issue on PayPal today but it was already marked as Resolved.

so what's really going on?