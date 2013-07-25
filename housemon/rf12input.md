---
layout: housemon
title: RF12Input by TheDistractor (aka lightbulb)
subtitle: Updated 2013-07-22 14:50:34
---

{% raw %}

# RF12Input 

**Change log:**   
+  2013-07-23 v0.1.2 - Added missing UDP Listener.  


## Overview

**RF12Input** is installed by loading the **RF12Input** briq from the HouseMon admin screen. Version 0.1.0 has no additional configuration within the Admin panel.


**RF12Input** is an implementation of an **RF12Registry** service. In installs four simple input handlers to allow you to send information into HouseMon to be processed by the **RF12Registry** and ultimately sent to a listening **RF12Demo-readwrite** compatible service.  

The following Input Handlers are are installed by this Briq:  

-  A web page to allow you to enter and submit data manually.  
-  A UDP Listener on Port ``3334``
-  A TCP Listener on Port ``3334``
-  A Unix domain socket listener for ``/tmp/rf12input.sock``

The **RF12Input** web page is fairly self explanitory. You enter the required parameters for 'Band', 'Group', 'NodeID' (Source Node), 'Header' (Destination node), and 'Data' the bytes to send in the format specific to your driver *(this is most probably comma delimited data for simple RF12Demo devices)*  
When you press send, you will see a numerical 'result' to the left of the send button if your request found its way to a listening service that handles writes for those parameters you entered.  

If you do not see a numerical result, you have probably not registered the correct information in your RF12Demo-readwrite service. More information can be found [here](rf12demo-writemasks.html)  

Additionally you will see a log panel below the send button. This will show all requests that passed through the **RF12Registry** you are connected to since you loaded the page. The list contains the most recent 20 write requests.  

To use the other three mechanisms (TCP/UDP/UDS) you will need some integration tools. You can write your own tools/scripts or you can use something as simple as netcat (on linux).  

You will want a version of netcat that supports both TCP,UDP AND Unix domain sockets. A good debian package is netcat-openbsd, as this supports all three.






{% endraw %}
