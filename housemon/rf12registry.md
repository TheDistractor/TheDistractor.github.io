---
layout: housemon

title: RF12Registry by TheDistractor
subtitle: Updated 2013-07-22
---

{% raw %}

# RF12Registry 
  
<br/>

## Background
Originally, I just needed a way to write data to one Jeenode running \[RF12demo.9\] using node.js. I also only ever needed to write to nodeids' that were on the same 'home' band/group as my listening jeenode.  

This was simple, I just provided my own rf12demo-serial Briq with a way to pick up write requests *(I chose to add a small unix domain socket listener inside my version of rf12demo-serial)*.

So....

Time moves on, and my network of nodes expanded beyond what I ever thought. What I do with those nodes has also expanded far beyond simple reporting of roomnodes etc. I now need to take input and output from and to lots of devices, (not just jeenodes). I also needed to simplify (if you could call it that) my rf12demo-serial variant(s). 

Enter the **RF12Registry**.

## Overview ##
Note: *First a word of caution, this update/submission is only v0.1.0. I am currently using v0.2.0 which is slightly mode capable but is also dependant upon infrastructure that is absent in the current HouseMon 0.6.x, so I have separated out a v0.1.0 from my v0.2.0 that seems to function ok - YMMV until I can submit v0.2.0 with all its extra baggage. Still, it may provide valuable benefit.*
  
The **RF12Registry** Briq (and its supporting services) serves to achieve a rather limited set of goals, but it looks complicated as it does this via a set of loosely coupled components. 
  
**RF12Registry** acts as a central switchboard and allows participating *rf12demo-xxxxx* compatable services to register their ability to handle 'write' requests using specific *band/group* patterns. 

It then allows other *'input mechanisms'* to provide data to be written to one of these registered services by matching the registered *band/group* of the supporting service to the *band/group* of the input making the write request. If a match is found, the data to be written is passed onto the matching service to perform the write request.
  
One important point is that the **RF12Registry** is able to use a *format string* provided by the service upon registration to help to format the data to be written. This allows a somewhat simple abstraction mechanism for writing data to different *rf12demo-xxxx* 
capable services.


To take advantage of this Briq and the services it can offer you need the following:

  - This Briq ( **RF12Registry** ) and its supporting files.
  - An *rf12demo-xxxx* compatable Briq that supports the **RF12Registry**
    (like *rf12demo-readwrite*).
  - A way to obtain input and route it to the **RF12Registry** (like the associated **RF12Input** Briq).

***As an example:*** 

You have the requirement to send a 1byte packet of data to a remote node on network 868/100. 

The 1byte packet will contain an integer whos' value represents the number of times an onboard LED will flash.

You have a master jeenode connected to your HouseMon computer running the latest **\[RFDemo.10\]** script connected to ttyAMA0. 


NB: Please be sure you are using the very latest version of the **RFDemo.10** script, as it has an important enhancement for version determination.
  
A patch has been submitted to include this enhancement in the standard Jeelib \[RF12Demo.10\]. This patch was merged into the main repo as at 2013-07-18 - please look [here for the pull request](https://github.com/jcw/jeelib/pull/51). This patch did not increment the rfdemo version number displayed on script startup.
  
You can tell if you have this feature by sending the character 'v' to your jeenode serial port. If the script replies with **\[RF12Demo.10\]** you have the feature. If your script replies with the configuration/help screen, you do not have the feature and an upgrade is suggested.
 
*Note:If you cannot upgrade, you may still be able to make use of the RF12Registry using earlier versions of RF12Demo but this requires additional steps that will be added shortly. Additionally you can also still make use of the RF12Registry if your connected 
node does not use \[RF12Demo.xx\] at all, but again this will require additional steps I will add shortly.*



#### Solution:

**STOP** - Did you read the above note about the latest version of RF12Demo script? 

You did? OK so you now have an upgraded jeenode/jeelink running the very latest version of \[RF12Demo.10\] from the patch link above.

*(I'll oftern refer to this script as RF12Demo.10+ as opposed to RF12Demo.10 which does not have this feature.)*

1. Install the RF12Registry upgrade, containing RF12Registry, RF12Demo-ReadWrite and RF12Input briq's and support files from [here](https://github.com/jcw/housemon/pull/70). Note that this pull request is currently at time of writing inside the 'develop' branch, so after you 'clone' the housemon repo you should switch to the 'develop' branch or merge the changes into your own work.

2. From your updated HouseMon Admin screen, load the new **RF12Registry** Briq. There is nothing additional to configure in this initial version of the RF12Registry Briq.
3. **Unload** any existing *RF12demo-serial* briq's or other Briq's **bound the the serial port of your master jeenode** you want to work with *(ttyAMA0 in example above)*. If you have two+ master nodes, you can leave the ones **NOT** bound to master node we are dealing with (ttyAMA0), and leave them connected via the existing mechanism. **rf12demo-readwrite** will cooexist quite happily with **rf12demo-serial** (on different serialports of course)
4. Load an instance of **rf12demo-readwrite**, and on the initial installation screen enter your devices serialport. If your devices baud rate is different than the standard 57600 (e.g as with JNu) adjust the baud rate accordingly. 

Finally if you are running a sketch of \[RF12Demo.10+\] *(the very latest - see above)* you need not adjust the 'Shell Version' parameter.  

If you are *sure* you are running \[RF12Demo.10\] **without** the patch mentioned above, please enter '10' in the 'Shell Version' field. *(This basically provides hints to the* **RF12Registry** *for your devices capability where they may not easily determined)*.  
  
Click on the Install button to install the briq. 
  

At this point your device should now carry on being used by **rf12demo-readwrite** as if it was a standard **rf12demo-serial** briq in control, it will however be 'write-aware' but not yet configured to do so.

5. The **rf12demo-readwrite** Briq has additional parameters that can be provided 'after' installation that give it these additional capabilities.  
Clicking opon the newly 'installed' Briq for **rf12demo-readwrite:ttyAMA0** you will see these additional parameters:
      - Add/Override default CLI commands: 
        - *This parameter allows you to override the commands given to obtain version and configuration data from your connected sketch*. 
          *It is configured with the defaults of 'v' for version and '?' to obtain configuration*. 
          *If you supplied a specific 'Shell Version' then the version command is bypassed*.
      - Initial commands sent on startup:
       - *This is a carry-over from rf12demo-serial for compatability and allows specific one-time commands to be sent to the sketch every time the Briq is initialised.* **Note:** *you do not need to put the '?' here, as it is already specified in the CLI commands above*.
      - write mask(s) [see here](#writemasks):
        - *This parameter is the one that turns on the ability to accept 'write' requests*. 
        
        **Going back to our scenario above, we want 
          to write to other nodes on the same band/group as our master jeenode is broadcasting on - 868/100. 
          We therefore set this parameter to the following: {%b}/{%g}|{%1}**
                                                                                                                                                                                                                                                  
6. When you have confirmed all is correct you can press the 'Update Settings' button. The Briq will now register this pattern with the **RF12Registry**, effectivly telling the **RF12Registry** that for 868Mhz, Group 100, this Briq instance will handle write requests. 
The format for these 'write' requests will be based upon the format string {%1} - see explanation about format strings below.

7. Your Briq handling ttyAMA0 is now processing packets and additionally waiting for 'write' requests for 868Mhz Group 100. 

Time to send it some input.

8. Above we mentioned that we were going to send a 1byte packet to nodeid 4 on 868/100. The **RF12Input** briq provides many ways to do this but we will stick with the simplest. We will use **RF12Inputs** web page.
9. Go ahead and load the **RF12Input** Briq from the Admin panel. There is nothing additional to configure so we can go straight to 
the 'Install' button. After a few seconds **RF12Input** will hopefully show as installed. One of the things **RF12Input** does is register 
itself with the socket 3334 (the next one along from the default HouseMon install of 3333). If you have anything competing for this port you may want to correct it. (until I get the chance to apply a patch to make this a configuration parameter, you can edit the source if you find a conflict). 
Finally **RF12Input** installs a new Web page in the HouseMon menu bar, called 'RF12Input'. Head over to this page, as it provides us an input form to use for write requests.
10. Once you are in the **RF12Input** web page, you will see a simple data entry form. Make sure the correct 'Band' is selected 
(868 in above scenario), then check 'Group' is 100 (*the default is 212*). If you remember our target nodeid was going to be 4, So for a node listening only for its own packets we leave NodeID as 0 and we enter the result of (0x40 | 4) (which is 68) in the 'Header' field, and finally enter the 1byte integer that represents the number of LED blinks you want to send to the device, lets say '6'. Then press the 'Send' button.

All being well, you have sent the byte representing 6 to the target node. If you needed to send 2 bytes, you would use:
<br/>

        0,0

and 3 bytes would be:

        0,0,0

___

<a name="writemasks"></a>
Explanation of **'writemasks'**:


A full registration pattern takes the form `<band>/<group>|<writemask>`

e.g if a driver registered with `868/200|{%1}` it would represent the fact that this
driver is willing to handle 'write' requests for communications on 868Mhz group 200 
using the default 'RF12demo.10' direct write format `band,group,node,hdr,byte1,byte2,byteN>`


The `<band>` parameter may take the following forms:
`{%b}` - the home band of the underlying device the driver manages. i.e. `{%b}/200|{%1}`
`*`  - any band i.e. `*/200|{%1}`

`/`  - required - seperates `<band>` and `<group>`

The `<group>` parameter may take the following forms:
`{%g}` - the home group of the underlying device the driver manages. i.e. `868/{%g}|{%1}`
`*`  - any group i.e. `868/*|{%1}` 

`|`  - required - seperates `<band/group>` from `<writemask>`

The `<writemask>` parameter may be made up of the following tokens:
 
`{%1}` - use the default RF12Demo.10 'write' format = `{%B},{%g},{%i},{%h},{%s}>`
       nb: if the shell version is set at 9, the `{%s}` format will be used
`{%B}` - the band parameter - this is aliased to the band code 3,4,8 or 9 for RF12 Driver, or see `{%b}`
`{%b}` - passes the band through unchanged - 868 does NOT get changed to 8
`{%g}` - the group parameter
`{%i}` - the destination node parameter
`{%h}` - the header byte parameter
`{%s}` - the actual data parameter that is the real information to be sent over the air.
`{\r}` - carrage return
`{\n}` - line feed


Drivers are registered in a 'last registration wins' scenario, but drivers are matched
for writes on a 'first match is used' scenario.
e.g. if two drivers both register for 868/212, the last driver is the one that is used.
e.g  if one driver registers for 868/\* and other for 868/212, and a write request
come for 868/212, the first pattern (868/\*) will be picked.
In reality this contention may not matter for 90% use cases.
(this approach may be updated)

The 'writemask' allows multiple different 'RF12Demo' drivers to have a flexible write
structure whilst keeping a single 'write' interface.

As an example:

Assuming a write request was as follows:
  
  `registry.write(868,200,7,0,"1,1,1,1,1,1,1,1,1,1")`

A Normal 'RF12Demo.10' driver handling /dev/ttyAMA0 device could register as `868/200|%1`
whereby the above write request would equate to the following:
`'868,200,7,0,1,1,1,1,1,1,1,1,1,1>'`
which will send `10` * `1s` to node 7 on band 868 group 200


Another driver which simulates 'RF12Demo.10' but uses a different protocol
could register the following pattern: `868/199|PUMP {%s}{\r}`
And if sent
  `registry.write(868,199,7,0,"ON")`
would equate to:
`PUMP ON\r` (note that the driver did not need band/goup etc as it has its own protocol)





{% endraw %}