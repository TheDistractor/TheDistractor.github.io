---
layout: housemon

title: RF12Registry by TheDistractor
subtitle: Updated 2013-07-22 14:50:05
---
 
{% raw %}

# RF12Registry 
  


## Overview
**Note:** *First a word of caution, this update/submission is only v0.1.0. I am currently using v0.2.0 which is slightly more capable but is also dependant upon infrastructure that is absent in the current HouseMon 0.6.x, so I have separated out a v0.1.0 from my v0.2.0 that seems to function ok - YMMV until I can submit v0.2.0 with all its extra baggage. Still, it may provide valuable benefit, and has also undergone some reasonable testing*
  
The **RF12Registry** Briq (and its supporting services) serves to achieve a rather limited set of goals.  

**a)** Provide infrastructure to allow others to develop modules that connect with RF12Demo compatible 'devices'.  

**b)**. Provide some abstraction from the physical devices themselves in order that upgrades to either the device (sketch) or HouseMon (briq) in the future are flexibly managed.  

It may look complicated to some as it does this via a set of loosely coupled components. Do not panic, just imagine this as a set of Briqs that rely on each other.
  
**RF12Registry** acts as a central switchboard and allows participating *rf12demo-xxxxx* compatable devices to register their ability to handle 'write' requests via a management briq, using specific *band/group* patterns. 

By using a registry acting as a sort of proxy device allows other *'input mechanisms'* to provide data to be written to one of these registered services. The Registry does this by matching the registered *band/group* of the supporting service to the *band/group* of the *'input mechanism'* making the write request. If a match is found, the data to be written is passed onto the matching service to perform the write request.
  
One important point is that the **RF12Registry** is able to use a *format string* provided by the service upon registration to help to format the data to be written. This allows a somewhat simple abstraction mechanism for writing data to different *rf12demo-xxxx* 
capable devices.

If you want to read some background please try [here](rf12registry-background.html)



To take advantage of this Briq and the services it can offer you need the following:  

  - This Briq ( **RF12Registry** ) and its supporting files.  
  - An *rf12demo-xxxx* compatable Briq that supports the **RF12Registry**
    (like *rf12demo-readwrite*).  
  - A way to obtain input and route it to the **RF12Registry** (like the associated **RF12Input** Briq). Its also very easy to write your own.  
  

***As an example:*** 
  

You have the requirement to send a 1byte packet of data to a remote node on network 868/100. 

The 1byte packet will contain an integer whos' value represents the number of times an onboard LED will flash.

You have a jeenode connected to your HouseMon computer running the latest **\[RFDemo.10\]** script connected to ttyAMA0. 


**NB:** Please be sure you are using the very latest version of the **RFDemo.10** script, as it has an important enhancement for version determination.

Details of **RF12Registry** Installation can be found [here](rf12registry-installation.html)

  

#### Solution:

**STOP** - Did you read the above note about the latest version of RF12Demo script? 

You did? OK so you now have an upgraded jeenode/jeelink running the very latest version of **\[RF12Demo.10\]**.

*(I'll oftern refer to this script as RF12Demo.10+ as opposed to RF12Demo.10 which does not have this feature.)*

You have also installed the Updated components and loaded the relevant Briqs as per [installation](rf12registry-installation.html)  

<a name="example-scenario"> </a>  

Going back to our scenario above, we want to write to other nodes on the same band/group as our master jeenode is broadcasting on - 868/100.  

We therefore set the **rf12demo-readwrite** writemask parameter to the following: ``{%b}/{%g}|{%1}``  

These parameters are detailed within the [writemasks](rf12demo-writemasks.html), however as a recap:  
The ``{%b}`` will get replaced with the 'Band' the Jeelink is configured with *(as reported by its configuration string)* - 868.  
The ``{%g}`` will get replaced with the 'Group' the Jeelink is configured with *(as reported by its configuration string)* - 100  
The ``{%1}`` gets replaced with the default 'write' pattern for the relevant version of RF12Demo. (again - see [writemasks](rf12demo-writemasks.html) ).
  
When you have confirmed all is correct you can press the 'Update Settings' button. The Briq will now register this pattern with the **RF12Registry**, effectivly telling the **RF12Registry** that for 868Mhz, Group 100, this Briq instance will handle write requests. (*in other words Jeelink on ttyAMA0 will be sent all write requests sent to 868/100*)

Your Briq handling ttyAMA0 is now processing packets and additionally waiting for 'write' requests for 868Mhz Group 100. 
  
Time to send it some input.  
  
Above we mentioned that we were going to send a 1byte packet to nodeid 4 on 868/100. The **RF12Input** briq provides many ways to do this but we will stick with the simplest. We will use **RF12Inputs** web page.  

Head over to the **RF12Input** page that was installed when you installed the **RF12Input** briq, as it provides us an input form to use for write requests.  

Once you are in the **RF12Input** web page, you will see a simple data entry form. Make sure the correct 'Band' is selected (868 in above scenario), then check 'Group' is 100 (*the default is 212*).   

If you remember our target nodeid was going to be 4, so for a node listening only for its own packets we leave NodeID *(source node)* as 0 and we enter the result of (0x40 | 4) (which is 68) in the 'Header' field *(for more into about how to construct the header byte go to [writemasks](rf12demo-writemasks.html) )*, and finally enter the 1byte integer that represents the number of LED blinks you want to send to the device, lets say '6'. Then press the 'Send' button.
*So, our full description of the fields now is:  
Send the digit 6 to destination node 4 on 868/100 from source node 0 on 868/100*. 

All being well, you have sent the byte representing 6 to the target node. If you needed to send 2 bytes, you would use:
<br/>

        0,0

and 3 bytes would be:

        0,0,0

**A few more examples:**  

If you had set your writemasks within **rf12demo-readwrite** to:  

         {%b}/*|{%1}

You would have been able to use this Jeelink and **RF12Input** to send bytes to *any* node on *any* group within the 868 band.



{% endraw %}