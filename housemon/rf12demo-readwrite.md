---
layout: housemon

title: RF12Demo-ReadWrite by TheDistractor

subtitle: Updated 2013-07-22 14:50:17 

---

{% raw %}

## RF12Demo-ReadWrite.
The **RF12Demo-readwrite** briq is an alternative to the **rf12demo-serial** briq where the user wishes to allow for input to be sent to the device the briq controls.

**RF12Demo-readwrite** v0.1.0 can handle the following sketches as 'client devices' via a serial port:
RF12Demo.9 (restrictions apply - see below)  

RF12Demo.10 (without version command - restrictions apply - see below)  

RF12Demo.10+ (with 'version' command)

RF12Demo.10 [compatible] versions (see restrictions).

**RF12Demo-readwrite** can coexist with **RF12Demo.serial** within the same HouseMon installation so long as they are using different serialports.

**RF12Demo-readwrite** v0.1.5 removes the direct relationship with the serialport, and implements a replaceable stream based transport system, this allows for instance to bind the driver to other transports such as sockets, message queues, I2C drivers etc. *(this will be made available as seperate patch)*  


**rf12demo-readwrite** is installed as per a normal briq, but has a 2 stage configuration. 

### The first step is basic installation:  

**1**\. Load an instance of **rf12demo-readwrite**, and on the initial installation screen enter your devices serialport (*ttyAMA0 in this example*). If your devices baud rate is different than the standard 57600 (e.g as with JNu which is default of 38400) adjust the baud rate accordingly. Finally if you are running a sketch of \[RF12Demo.10+\] *(the very latest - see above)* you need not adjust the 'Shell Version' parameter.  
If you are *sure* you are running \[RF12Demo.10\] **without** the patch mentioned above, please enter '10' in the 'Shell Version' field. *(This basically provides hints to the* **RF12Registry** *for your devices capability where they may not easily determined)*.  

If you simply *MUST** use RF12Demo.9 please enter the value '9' in the 'Shell Version' field.
  
**2**\. Click on the Install button to install the briq. 
  
At this point your Jeelink device should now carry on being used by **rf12demo-readwrite** as if it was a standard **rf12demo-serial** briq in control, it will however be 'write-aware' but not yet configured to do so.  

### The second step is writer configuration:  

The **rf12demo-readwrite** Briq has additional parameters that can be provided 'after' installation that give it these additional 'write' capabilities.  
Clicking upon an 'installed' Briq **rf12demo-readwrite:ttyAMA0** (as used in the examples) you will see these additional parameters:  

+  Add/Override default CLI commands:

	+  test

		-  *This parameter allows you to override the        commands given to obtain version and             configuration data from your connected           sketch*. 
		-  *defaults of 'v' for version and '?' to obtain configuration are pre-set*. 
		-  *If you supplied a specific 'Shell Version' then the version command is bypassed*.

+ Initial commands sent on startup:

    + *This is a carry-over from rf12demo-serial for compatability and allows specific one-time commands to be sent to the sketch every time the Briq is initialised.* **Note:** *you do not need to put the '?' here, as it is already specified in the CLI commands above*.

+ write mask(s) [see here](rf12demo-writemasks.html):

    + *This parameter is the one that turns on the ability to accept 'write' requests*.  


**3**\. For those following with the basic installation guide, set the writemask field to: ``{%b}/{%g}|{%1}`` and continue with the installation guide.



## Sketch Restrictions

If you use *RF12Demo.9* sketch you **MUST** specify the 'Shell Version' field as '9'.  

If the writemask for *RF12Demo.9* uses {%1} then the internal mask of {%s} will be used. This means the entire 'data' portion of the request is sent to the sketch untouched and you **MUST** use the native sketch syntax, such as:  
``0,0,0,0,0,26s`` send ``0,0,0,0,0`` to node 26. 
or  
``0,0,0,0,0,26a`` send ``0,0,0,0,0`` to node 26 with ack.

Using this sketch and write format restricts you to the configured band/group unless you want to get very technical!

If you know you will always send out data requiring ACKS, you *could* change your writemask to:  
{%s},{%h}a  

in this case your data portion would only need to contain the packet data, and the header field will be used to build up the command.  
where 0,0,0,0,0 is the data portion and the header field will contain 26.







{% raw %}
