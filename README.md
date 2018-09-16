# Blynk + Node-RED

- The most popular mobile app for the IOT. Works with anything: ESP8266, Arduino, Raspberry Pi, SparkFun and others....from [Blynk](https://www.blynk.cc/)
- Flow-based programming for the Internet of Things ...from [Node-RED](https://nodered.org/)

<p align="center">
<img src="https://github.com/phyunsj/blynk-node-red/blob/master/blynk-node-red.png" width="500px"/>
</p>


## Getting Started

- [Blynk Getting Started](https://www.blynk.cc/getting-started/)
- [Node-RED Getting Started](https://nodered.org/#get-started)

## Example : LCD, Gauge, Live Chart

### Blynk App to monitor IoT sensor data

<p align="center">
<img src="https://github.com/phyunsj/blynk-node-red/blob/master/blynk-example1.gif" width="300px"/>
</p>

<p align="center">
  <img src="https://github.com/phyunsj/blynk-node-red/blob/master/lcd_vpin_2.png" width="150px"/>
  <img src="https://github.com/phyunsj/blynk-node-red/blob/master/gauge_vpin_4.png" width="150px"/>
  <img src="https://github.com/phyunsj/blynk-node-red/blob/master/chart_vpin_3_4.png" width="150px"/>
</p>

###  Node-RED to collect and push IoT sensor data

All data are simulated from Node-RED function node. Must acquire `AUTH_TOKEN` to work.

> npm install node-red-contrib-blynk-ws --global

<p align="center">
<img src="https://github.com/phyunsj/blynk-node-red/blob/master/node-red-flow-example1.png" width="600px"/>
</p>

**Node-RED flow**

```
[{"id":"68b37c86.451a84","type":"blynk-ws-out-lcd","z":"158f41f0.3c873e","name":"V2 LCD Greeting","pin":"2","client":"86fe6b36.cbe288","x":680,"y":566,"wires":[]},{"id":"a2f6bcfd.13cab","type":"function","z":"158f41f0.3c873e","name":"Message Position Adjustment","func":"\nvar count=context.get('count') || 0;\nvar rcount=context.get('rcount') || 15;\n\n\nif (count >= 15) count = 0;\nelse count++;\n\nif (rcount <= 0) rcount = 15;\nelse rcount--;\n\n// text for the top row\nmsg.clear = 1;\nmsg.text = \"Okie Dokie\";\nmsg.x = count ;\nmsg.y = 0;\n// text1 for the bottom row\nmsg.text1 = \"Welcome Back!\";\nmsg.x1 = rcount ;\nmsg.y1 = 1;\n\ncontext.set('count', count) ;\ncontext.set('rcount', rcount) ;\n\nreturn msg;","outputs":1,"noerr":0,"x":406,"y":566,"wires":[["68b37c86.451a84"]]},{"id":"bdff2d1a.a7d5a","type":"comment","z":"158f41f0.3c873e","name":"V2: LCD Push Event","info":"","x":674,"y":520,"wires":[]},{"id":"9c47c417.0968d8","type":"function","z":"158f41f0.3c873e","name":"Measure Current Temperature","func":"\nvar value = Math.floor(Math.random()*40+ 60);\nvar new_msg = {payload : value};\n\nreturn new_msg;","outputs":1,"noerr":0,"x":407,"y":669,"wires":[["7e3a5b36.8f8d54"]]},{"id":"7e3a5b36.8f8d54","type":"blynk-ws-out-write","z":"158f41f0.3c873e","name":"V4 Report Current Temperature","pin":"4","pinmode":0,"client":"86fe6b36.cbe288","x":720,"y":671,"wires":[]},{"id":"b1a9fe44.8efbf","type":"inject","z":"158f41f0.3c873e","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":139,"y":640,"wires":[["9c47c417.0968d8","a101f80.db42f08","a2f6bcfd.13cab"]]},{"id":"a101f80.db42f08","type":"function","z":"158f41f0.3c873e","name":"Measure Current Humidity","func":"var value = Math.floor(Math.random()*30+ 40);\nvar new_msg = {payload : value};\n\nreturn new_msg;\n","outputs":1,"noerr":0,"x":393,"y":706,"wires":[["f498aeeb.0d232"]]},{"id":"f498aeeb.0d232","type":"blynk-ws-out-write","z":"158f41f0.3c873e","name":"V3 Report Current Humidity","pin":"3","pinmode":0,"client":"86fe6b36.cbe288","x":710,"y":710,"wires":[]},{"id":"1ae35dd4.a35ba2","type":"comment","z":"158f41f0.3c873e","name":"V3,V4 : Gauge Push Event","info":"","x":698,"y":624,"wires":[]},{"id":"86fe6b36.cbe288","type":"blynk-ws-client","z":"","name":"","path":"ws://blynk-cloud.com/websockets","key":"**********************","dbg_all":true,"dbg_read":true,"dbg_write":true,"dbg_notify":false,"dbg_mail":false,"dbg_prop":false,"dbg_sync":true,"dbg_bridge":false,"dbg_low":false,"dbg_pins":"","multi_cmd":true,"proxy_type":"no","proxy_url":""}]
```

##  node-red-contrib-blynk-ws nodes

Review `msg` format of each [node-red-contrib-blynk-ws/nodes](https://github.com/gablau/node-red-contrib-blynk-ws/tree/master/nodes).

As of 9/16/2018, 

- [lcd node](https://github.com/gablau/node-red-contrib-blynk-ws/blob/master/nodes/blynk-ws-out-lcd.js),

text, x, y for the first row display. text1, x1, y1 for the second row display. No `payload`. 
```
msg = { clear : 1, text : "Okie Dokie", x : count, y : 0, text1 : "Welcome Back!", x1 : rcount , y1 : 1};
return msg;
```
- JoyStick if `MERGE` Output is selected. 

`write event` node returns `msg.arrayOfValues` for [`x`,`y`].

<p align="center">
<img src="https://github.com/phyunsj/blynk-node-red/blob/master/joystick_write_event_vpin10.png" width="600px"/>
</p>

- Pull Request from Virtual Pin 3 (Blynk App)

`read event` node returns a pin number. 

<p align="center">
<img src="https://github.com/phyunsj/blynk-node-red/blob/master/pull_read_event_vpin3.png" width="600px"/>
</p>
