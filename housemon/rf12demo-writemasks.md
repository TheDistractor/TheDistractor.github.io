---
layout: housemon

title: RF12Registry-writemasks by TheDistractor
subtitle: Updated 2013-07-22 14:50:05
---

{% raw %}

# RF12Registry 

## WriteMasks

Explanation of **'writemasks'**:


A full registration pattern takes the form `<band>/<group>|<writemask>`

e.g if a driver registered with `868/200|{%1}` it would represent the fact that this driver is willing to handle 'write' requests for communications on 868Mhz group 200 using the default 'RF12demo.10' direct write format `band,group,node,hdr,byte1,byte2,byteN>`


The `<band>` parameter may take the following forms:
`{%b}` - the home band of the underlying device the driver manages as reported by its configuration string. i.e. `{%b}/200|{%1}`  

`*`  - any band i.e. `*/200|{%1}`

`/`  - required - seperates `<band>` and `<group>`  

The `<group>` parameter may take the following forms:
`{%g}` - the home group of the underlying device the driver manages as reported by its configuration string. i.e. `868/{%g}|{%1}`  

`*`  - any group i.e. `868/*|{%1}` 

`|`  - required - seperates `<band/group>` from `<writemask>`

The `<writemask>` parameter may be made up of the following tokens:
 
`{%1}` - use the default 'write' format for the sketch.  
RF12Demo.10 = `{%B},{%g},{%i},{%h},{%s}>`  
RF12Demo.9 = `{%s}`  


`{%B}` - the band parameter - this is aliased to the band code 3,4,8 or 9 for RF12 Driver, or see `{%b}`
`{%b}` - passes the band through unchanged - 868 does NOT get changed to 8
`{%g}` - the group parameter
`{%i}` - the source node parameter
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
which will broadcast `10` * `1s` from node 7 on band 868 group 200  

whereas:  
`'868,200,1,68,1,1,1,1,1,1,1,1,1,1>'`  
will send the same 10 bytes from source node 1 to destination node 4 `(DST | 4)`



Another driver which simulates 'RF12Demo.10' but uses a different protocol
could register the following pattern: `868/199|PUMP {%s}{\r}`
And if sent
  `registry.write(868,199,1,68,"ON")`
would equate to:
`PUMP ON\r` for destination node 4 (note that the driver did not need band/goup etc as it has its own protocol, and may even send this out as an InfraRed stream)

I will attempt to supply some sketches/examples that simulate this RF12Demo.10 compatibility, shortly.

{% endraw %}
