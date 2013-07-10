---
layout: housemon

title: RF12Registry by TheDistractor
subtitle: TODO- Information about HouseMon RF12Registry here......
---

{% raw %}

# RF12Registry 
  
<br/>

##Background
Originally, I just needed a way to write data to one Jeenode running \[RF12demo.9\] using node.js. I also only ever needed to write 
to nodeids' that were on the same 'home' band/group as my listening jeenode.  
This was simple, I just provided my own rf12demo-serial Briq with a way to pick up write requests (I chose to add a small unix 
domain socket listener inside my version of rf12demo-serial).

So....

Time moves on, and my network of nodes expanded beyond what I ever thought. What I do with those nodes has also expanded far beyond 
simple reporting of roomnodes etc. I now need to take input and output from and to lots of devices, (not just jeenodes). I also needed 
to simplify (if you could call it that) my rf12demo-serial variant(s). 

Enter the **RF12Registry**.

##Overview##
Note: *First a word of caution, this update/submission is only v0.1.0. I am currently using v0.2.0 which is slightly mode capable 
but is also dependant upon infrastructure that is absent in the current HouseMon 0.6.x, so I have separated out a v0.1.0 from my 
v0.2.0 that seems to function ok - YMMV until I can submit v0.2.0 with all its extra baggage. Still, it may provide valuable benefit.*

The *RF12Registry* Briq (and its supporting services) serves to achieve a rather limited set of goals, but it looks complicated 
as it does this via a set of loosely coupled components. 

RF12Registry acts as a central switchboard and allows participating *rf12demo-xxxxx* compatable services to register their ability 
to handle 'write' requests using specific *band/group* patterns. 

It then allows other 'input mechanisms' to provide data to be written to one of these registered services by matching the registered 
*band/group* of the supporting service to the *band/group* of the input making the write request. If a match is found, the data to 
be written is passed onto the matching service to perform the write request.

One important point is that the RF12Registry is able to use a *format string* provided by the service upon registration to help to 
format the data to be written. This allows a somewhat simple abstraction mechanism for writing data to different *rf12demo-xxxx* 
capable services.


To take advantage of this Briq and the services it can offer you need the following:

  - This Briq (*RF12Registry*) and its supporting files.
  - An *rf12demo-xxxx* compatable Briq that supports the RF12Registry
    (like *rf12demo-readwrite*).
  - A way to obtain input and route it to the *RF12Registry* (like the associated *RF12Input* Briq).

***As an example:*** 

You have the requirement to send a 1byte packet of data to a remote node 4 on network 868/100. 

The 1byte packet will contain an integer whos' value represents the number of times an onboard LED will flash.

You have a master jeenode connected to your HouseMon computer running the **\[RFDemo.9\]** or earlier script connected to ttyAMA0. 

####Solution:

**STOP** - Step 1 is to upgrade your master jeenode sketch (if possible) to the very latest \[RF12Demo.10\] or later sketch. A patch 
has been submitted to slightly enhance the standard Jeelib \[RF12Demo.10\] and this may well increment the version to \[RF12Demo.11\] 
if accepted - please look [here for the pull request](https://github.com/jcw/jeelib/pull/51).
 
*Note:If you cannot upgrade, you may still be able to make use of the RF12Registry using earlier versions of RF12Demo but this 
requires additional steps that will be added shortly. Additionally you can also still make use of the RF12Registry if your connected 
node does not use \[RF12Demo.xx\] at all, but again this will require additional steps I will add shortly.*

So, you now have an upgraded jeenode/jeelink running the very latest version of \[RF12Demo.10+\] from the patch link above.


1. Install the RF12Registry upgrade, containing RF12Registry, RF12Demo-ReadWrite and RF12Input briq's and support files from [here]() or [here](). 
2. From your updated HouseMon Admin screen, load the new RF12Registry Briq. There is nothing additional to configure in this initial 
version of the RF12Registry Briq.
3. **Unload** any existing RF12demo-serial briq's or other Briq's **bound the the serial port of your master jeenode** 
*(ttyAMA0 in example above)* you want to work with.
If you have two+ master nodes, you can leave the ones **NOT** bound to master node we are dealing with (ttyAMA0), and leave them 
connected via the existing mechanism. rf12demo-readwrite will cooexist quite happily with rf12demo-serial (on different serialports 
of course)
4. Load an instance of RF12Demo-readwrite and on the initial installation screen enter your devices serialport. If your devices 
baud rate is different than the standard 57600 (e.g as with JNu) adjust the baud rate accordingly. 

Finally if you are running a sketch of \[RF12Demo.10+\] (the very latest - see above) you need not adjust the 'Shell Version' 
parameter. If you are sure you are running \[RF12Demo.10\] without the patch mentioned above, please enter '10' in the 'Shell Version' 
field. (This basically provides hints to the RF12Registry for your devices capability where they may not easily determined). Click 
on Install button. 
<br/>

At this point your device should now carry on being used by rf12demo-readwrite as if it was a standard rf12demo-serial briq in 
control, it will however be 'write-aware' but not yet configured to do so.

5. The rf12demo-readwrite Briq has additional parameters that can be provided 'after' installation that give it these additional 
capabilities. Clicking opon the newly 'installed' Briq for rf12demo-readwrite:ttyAMA0 you will see these additional parameters.
      - Add/Override default CLI commands: 
        - This parameter allows you to override the commands given to obtain version and configuration data from your connected sketch. 
          It is configured with the defaults of 'v' for version and '?' to obtain configuration. 
          If you supplied a specific 'Shell Version' then the version command is bypassed.
      - Initial commands sent on startup:
       - This is a carry-over from rf12demo-serial for compatability and allows specific one-time commands to be sent to the 
         sketch every time the Briq is initialised.
      - write mask(s) \[see documentation link above\]:
        - This parameter is the one that turns on the ability to accept 'write' requests. Going back to our scenario above, we want 
          to write to other nodes (specifically node 4) on the same band/group as our master jeenode is broadcasting on - 868/100. 
          We therefore set this parameter to the following: {%b}/{%g}|{%1}
                                                                                                                                                                                                                                                  
6. When you have confirmed all is correct you can press the 'Update Settings' button. The Briq will now register this pattern with 
the RF12Registry, effectivly telling the RF12Registry that for 868Mhz, Group 100, this Briq instance will handle write requests. 
The format for these 'write' requests will be based upon the format string {%1} - see explanation about format strings below.

7. Your Briq handling ttyAMA0 is now processing packets and additionally waiting for 'write' requests for 868Mhz Group 100. 

Time to send it some input.


--



{% endraw %}