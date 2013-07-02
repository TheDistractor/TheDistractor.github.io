###

# RF12Registry #

This module exports a single class 'RF12RegistryManager' which itself manages the singleton RF12Registry
(which is the real class we actually use)

RF12Registry implements a simple 'Registry' to enable
a number of 'RF12Demo.10+' (compatible) 'driver' clients to register their
services for 'write' actions. The registration is a simple act whereby
the driver registers a set of 1 or more 'patterns' relating to radio band / radio group
pairs that it is willing to act on behalf of.

A full registration pattern takes the form <band>/<group>|<writemask>

e.g if a driver registered with '868/200|%1' it would represent the fact that this
driver is willing to handle 'write' requests for communications on 868Mhz group 200 
using the default 'RF12demo.10' direct write format (band,group,node,hdr,byte1,byte2,byten>)

The <band> parameter may take the following forms:
%b - the home band of the underlying device the driver manages. i.e. %b/200|%1
*  - any band i.e. *\/200|%1

/  - required - seperates <band> and <group>

The <group> parameter may take the following forms:
%g - the home group of the underlying device the driver manages. i.e. 868/%g|%1
*  - any group i.e. 868/*|%1 

|  - required - seperates <band/group> from <writemask>

The <writemask> parameter may be made up of the following tokens:
 
%1 - use the default RF12Demo.10 'write' format = '%b,%g,%i,%h,%s>'
%b - the band parameter - this is aliased to the band code 3,4,8 or 9 for RF12 Driver, or see %B
%B - passes the band through unchanged - 868 does NOT get changed to 8
%g - the group parameter
%i - the destination node parameter
%h - the header byte parameter
%s - the actual data parameter that is the real information to be sent over the air.

Drivers are registered in a 'last registration wins' scenario, but drivers are matched
for writes on a 'first match is used' scenario.
e.g. if two drivers both register for 868/212, the last driver is the one that is used.
e.g  if one driver registers for 868/* and other for 868/212, and a write request
come for 868/212, the first pattern (868/*) will be picked.
In reality this contention may not matter for 90% use cases.
(this approach may be updated)

The 'writemask' allows multiple different 'RF12Demo' drivers to have a flexible write
structure whilst keeping a single 'write' interface.

As an example:

Assuming a write request was as follows:
  
  registry.write(868,200,7,0,"1,1,1,1,1,1,1,1,1,1")

A Normal 'RF12Demo.10' driver handling /dev/ttyAMA0 device could register as '868/200|%1'
whereby a the above write request would equate to the following:
'868,200,7,0,1,1,1,1,1,1,1,1,1,1>'
which will send 10 * 1s to node 7 on band 868 group 200

Another driver which simulates 'RF12Demo.10' but uses a different protocol
could register the following pattern: '868/199|PUMP %s\r'
And if sent
  registry.write(868,200,7,0,"ON")
would equate to:
'PUMP ON\r' (note that the driver did not need band/goup etc as it has its own protocol)

###
