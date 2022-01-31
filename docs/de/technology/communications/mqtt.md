| titel | last change | from |
| -------- | -------- | -------- |
| MQTT | 31.01.2022 | [@hydrotec](https://forum.iobroker.net/user/hydrotec) |

# MQTT

## Inhalt
* [Allgemein](#Allgemein)
* [Protokoll](#Protokoll)
* [Adapter](#Adapter)
* [Best Practice / Tutorial](#best-practice--tutorial)
* [Issues](#Issues)

## Allgemein
Bei MQTT (Message Queuing Telemetry Transport)  
handelt es sich um ein Netzwerkprotokoll, welches offen kommuniziert,  
und auch bei minimaler Netzwerkbandbreite eingesetzt werden kann.  
MQTT eignet sich sehr gut für das Internet der Dinge (IoT).  
In Fachkreisen spricht man auch von  Machine-to-Machine-Kommunikation (M2M).  
Es werden Daten (payloads) von Client zu Client über einen Broker ausgetauscht.  
Ausführliche Informationen können unter den folgenden Links aufgerufen werden.  

[<https://mqtt.org/>](https://mqtt.org/)  
[<https://de.wikipedia.org/wiki/MQTT>](https://de.wikipedia.org/wiki/MQTT)  

###### [zurück](#Inhalt)

## Protokoll
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/Doku_mqtt_02.png)  

Der MQTT-Broker ist die zentrale Anlaufstelle für die MQTT-Clients.  
Ein Client muss an dem Broker angemeldet sein, damit dieser seine *Payloads*,  
über die *Topics*, dem Broker mitteilen, oder sie empfangen kann.  
Der Broker akzeptiert erst einmal alle gesendeten *Topics* der Clients,  
und stellt sie weiteren Clients zur Verfügung.  
Damit ein Client, die eines anderen Clients zur Verfügung gestellten *Payloads*  
empfangen kann, muss er zuerst die zugehörigen *Topics* abonieren.


?> todo:  
 - etwas ausführlicher beschreiben  
 - in der Adapterbeschreibung sind Beispiele  
 - eventuell Grafik mit einbinden  

###### [zurück](#Inhalt)

## Adapter
Aktuell stehen in ioBroker drei Adapter unter dem Repository "*default*",  
und vier Adapter unter dem Repository "*latest*" zur Verfügung.  
Mit diesen Adaptern können Geräte, welche das *MQTT-Protokoll* sprechen,  
zu ioBroker angebunden werden können.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/Doku_mqtt_01.png)  

1.) [hass.mqtt](https://github.com/smarthomefans/ioBroker.hass-mqtt#readme "https://github.com/smarthomefans/ioBroker.hass-mqtt#readme")  

| **Repository** | [![](http://iobroker.live/badges/hass-mqtt-stable.svg) ![](http://img.shields.io/npm/v/iobroker.hass-mqtt.svg)](https://www.npmjs.com/package/iobroker.hass-mqtt "https://www.npmjs.com/package/iobroker.hass-mqtt")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/smarthomefans/ioBroker.hass-mqtt/graphs/code-frequency "https://github.com/smarthomefans/ioBroker.hass-mqtt/graphs/code-frequency") |

 -  wird in Verbindung zu *Home Assistant* genutzt  

2.) [Sonoff](https://github.com/ioBroker/ioBroker.sonoff#readme "https://github.com/ioBroker/ioBroker.sonoff#readme")

| **Repository** | [![](http://iobroker.live/badges/sonoff-stable.svg) ![](http://img.shields.io/npm/v/iobroker.sonoff.svg)](https://www.npmjs.com/package/iobroker.sonoff "https://www.npmjs.com/package/iobroker.sonoff")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/ioBroker/ioBroker.sonoff/graphs/code-frequency "https://github.com/ioBroker/ioBroker.sonoff/graphs/code-frequency") |

 -  wird in Verbindung zu *Tasmota* und *ESP* genutzt  

3.) [MQTT Broker/Client](https://github.com/ioBroker/ioBroker.mqtt#readme "https://github.com/ioBroker/ioBroker.mqtt#readme")

| **Repository** | [![](http://iobroker.live/badges/mqtt-stable.svg) ![](http://img.shields.io/npm/v/iobroker.mqtt.svg)](https://www.npmjs.com/package/iobroker.mqtt "https://www.npmjs.com/package/iobroker.mqtt")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/ioBroker/ioBroker.mqtt/graphs/code-frequency "https://github.com/ioBroker/ioBroker.mqtt/graphs/code-frequency") |

 - kann sowohl als *Client*, wie auch als *Broker* konfiguriert werden  
 - in der *Client* Version, wird ein externer *Broker* (z.B. [mosquitto](https://mosquitto.org "https://mosquitto.org") ) vorausgesetzt  
 - spricht grundsätzlich mit allen Geräten, welche über das MQTT-Protokoll kommunizieren  
 
4.) [MQTT-Client](https://github.com/Pmant/ioBroker.mqtt-client#readme "https://github.com/Pmant/ioBroker.mqtt-client#readme")

| **Repository** | [![](http://iobroker.live/badges/mqtt-client-stable.svg) ![](http://img.shields.io/npm/v/iobroker.mqtt-client.svg)](https://www.npmjs.com/package/iobroker.mqtt-client "https://www.npmjs.com/package/iobroker.mqtt-client")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/Pmant/ioBroker.mqtt-client/graphs/code-frequency "https://github.com/Pmant/ioBroker.mqtt-client/graphs/code-frequency") |

 - reiner MQTT Client  
 - es wird ein externer *Broker* (z.B. [mosquitto](https://mosquitto.org "https://mosquitto.org") ) vorausgesetzt  
 - spricht grundsätzlich mit allen Geräten, welche über das MQTT-Protokoll kommunizieren  

?> todo:  
 - Hilfestellung wann welcher Adapter verwendet werden sollte  
 - Entscheidungsmatrix wann mqtt als Server, wann als client  

###### [zurück](#Inhalt)

## Best Practice / Tutorial
?> todo:  
 - Beispiel Konfiguration Server/Client  
 - eventuell Link zu einem Tutorial  
 - Tools erwähnen 

###### [zurück](#Inhalt)

## Issues
?> todo:  
 - auf derzeitige Probleme hinweisen  

###### [zurück](#Inhalt)
