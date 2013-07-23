---
layout: housemon

title: RF12Registry-installation by TheDistractor
subtitle: Updated 2013-07-22 14:50:02
---

{% raw %}

# RF12Registry Upgrade & Installation

The **RF12Registry** update to HouseMon was included (merged) into the 'develop' branch on 2013-07-18. The pull request for this upgrade can be found [here](https://github.com/jcw/housemon/pull/70). Note that this pull request is currently at time of writing inside the 'develop' branch, so after you 'clone' the housemon repo you should switch to the 'develop' branch or merge the changes into your own work.

This upgrade installs the following Briqs:

**RF12Registry**

This briq relies upon the rf12registrymanager to supply all its services. rf12registrymanager is also installed into the briqs folder, but it will not appear itself as an available briq.   

**RF12Demo-ReadWrite**

This is a functional replacement for the **RF12Demo-serial** briq. Its does all the same original stuff, listening for incomming packets etc, but also has some extras to allow it to be communicated with from the outside world.

**RF12Input**

This briq provides a number of input mechanisms that you can harness. It also provides a simple web page GUI that you can use immediately once you have configured your system.


## Suggested updates:
A patch was recently submitted to jeelib for a small upgrade to the *rf12demo* sketch. It adds another 'command' to the existing *rf12demo* commands. Specifically it adds a command to obtain the 'version' string of the sketch. From this we can more easily determine the sketches capability. i.e *rf12demo.10* can do more than *rf12demo.9* when it comes to sending out commands. Details of the update are [here](https://github.com/jcw/jeelib/pull/51). *This patch did not increment the rfdemo version number displayed on script startup*.
  
You can tell if you have this feature by sending the character 'v' to your jeenode connected serial port. If the sketch replies with **\[RF12Demo.10\]** you have the feature. If your script replies with the configuration/help screen, you do not have the feature and an upgrade is suggested.
 
*Note:If you cannot upgrade, you may still be able to make use of the* **RF12Registry** *using earlier versions of RF12Demo but this requires additional steps that will be added shortly. Additionally you can also still make use of the* **RF12Registry** if your connected node does not use* \[**RF12Demo.xx**\] *at all, but again this will require additional steps I will add shortly.*


Once you have updated you HouseMon installation and confirmed you have the latest RF12Demo.10 sketch flashed onto your Jeelink, you can proceed to install the relevant components.
  
Technically the installation order is not that important, and until HouseMon gets a dependancy loader we cannot really confirm the order the briqs get loaded the next time HouseMon starts, but the order below is considered the most logical.


1. From your updated HouseMon Admin screen, load the new **RF12Registry** Briq. There is nothing additional to configure in this initial version of the RF12Registry Briq.

2. Now go ahead and load the **RF12Input** Briq from the Admin panel. There is nothing additional to configure so we can go straight to 
the 'Install' button. One of the things **RF12Input** does is register itself with the socket 3334 (the next one along from the default HouseMon install of 3333). If you have anything competing for this port you may want to correct it. (by the time you read this there may well be an upgrade to allow it to be changed without editing code, but until I get the chance to apply a patch to make this a configuration parameter, you can edit the source if you find a conflict). 
Finally **RF12Input** installs a new Web page in the HouseMon menu bar, called 'RF12Input'.

  
3. **Unload** any existing *RF12demo-serial* briq's or other Briq's **bound the the serial port of the Jeelink** you want to work with *(ttyAMA0 in this example)*. You can mix and match **rf12demo-readwrite** and **rf12demo-serial** (on different serialports of course) as they coexist in the same installation, however to take advantage of write capability you can install two or more instances of **rf12demo-readwrite**

3. Load an instance of **rf12demo-readwrite**, and on the initial installation screen enter your devices serialport (*ttyAMA0 in this example*). If your devices baud rate is different than the standard 57600 (e.g as with JNu) adjust the baud rate accordingly. Finally if you are running a sketch of \[RF12Demo.10+\] *(the very latest - see above)* you need not adjust the 'Shell Version' parameter.  
If you are *sure* you are running \[RF12Demo.10\] **without** the patch mentioned above, please enter '10' in the 'Shell Version' field. *(This basically provides hints to the* **RF12Registry** *for your devices capability where they may not easily determined)*.  
  
Click on the Install button to install the briq. 
  
At this point your Jeelink device should now carry on being used by **rf12demo-readwrite** as if it was a standard **rf12demo-serial** briq in control, it will however be 'write-aware' but not yet configured to do so.

The **rf12demo-readwrite** Briq has additional parameters that can be provided 'after' installation that give it these additional 'write' capabilities.  
Clicking opon the newly 'installed' Briq for **rf12demo-readwrite:ttyAMA0** you will see these additional parameters:
   - Add/Override default CLI commands:
       - *This parameter allows you to override the commands given to obtain version and configuration data from your connected sketch*. 
          *defaults of 'v' for version and '?' to obtain configuration are pre-set*. 
          *If you supplied a specific 'Shell Version' then the version command is bypassed*.
   - Initial commands sent on startup:
       - *This is a carry-over from rf12demo-serial for compatability and allows specific one-time commands to be sent to the sketch every time the Briq is initialised.* **Note:** *you do not need to put the '?' here, as it is already specified in the CLI commands above*.
   - write mask(s) [see here](rf12registry-writemasks.html):
        - *This parameter is the one that turns on the ability to accept 'write' requests*. 











{% endraw %}
