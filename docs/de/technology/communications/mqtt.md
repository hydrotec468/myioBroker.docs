| titel | last change | from |
| -------- | -------- | -------- |
| MQTT | 01.02.2022 | [@hydrotec](https://forum.iobroker.net/user/hydrotec) |

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

?> **Hinweis**:  
     In diesem Abschnitt wird nur auf den gängigsten Weg,  
	 MQTT zu nutzen, eingegangen.  
	 Es bestehen viel mehr Möglichkeiten, wie man MQTT nutzen kann.  
	 Hier alle Möglichkeiten aufzuzählen,  
	 würde den Rahmen dieser Dokumentation überschreiten.  

  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/Doku_mqtt_02.png)  
  
Der MQTT-Broker ist die zentrale Anlaufstelle für die MQTT-Clients.  
Ein Client muss an dem Broker angemeldet sein, damit dieser seine *Payloads*,  
über die *Topics*, dem Broker mitteilen (*Publish*),  
oder sie empfangen (*Subsribe*) kann.  
Der Broker akzeptiert erst einmal alle gesendeten *Payloads*,  
in den entsprechenden *Topics* der Clients,  
und stellt sie weiteren Clients zur Verfügung.  
Damit ein Client, den eines anderen Clients zur Verfügung gestellten *Payload*  
empfangen kann, muss er zuerst den zugehörigen *Topic* abonnieren.  

Was steckt hinter den Begriffen *Topic*, *Payload* und *abonnieren*.  
Unter *Topic* kann man sich eine Art Verzeichnisstruktur vorstellen.  
Diese Struktur wird meistens von den Herstellern vorgegeben,  
kann jedoch ebenso von Anwendern definiert werden.  
Kommt darauf an, um was für eine Art von Gerät es sich handelt.  
Grundsätzlich wird in einer Pfadangabe definiert, unter welcher Rubrik,  
von welchem Gerät und in welcher Sparte sich die Information (*Payload*) befindet.  

Beispiel  

     <Rubrik>/<Gerät>/<Sparte>/<Attribut>  

Bei *Payload* ist die eigentliche Nachricht gemeint.  
Der Inhalt einer Nachricht, kann sich folgendermaßen zusammensetzen.  
Wobei es da keine direkten Richtlinien gibt, wie sich so eine Nachricht aufbaut.  
In den meisten Fällen wird das Format *json* verwendet.  
Genauso ist auch nur ein *attribute* möglich.  

Beispiele  
Format *json*  

     {"state": "online"}  

Format *atribute*  

     online  

Damit der Broker weiß, welche Nachrichten ein Client bekommen möchte,  
muss der Client die Nachrichten anfordern (*abonnieren*).  
Das geschieht in der Form, das der Client unter dem Kanal *Subscribe*  
den gewünschten *Topic* an den Broker übermittelt.  
Innerhalb dieser *Topics* kann mit Wildcards gearbeitet werden.  
Die zwei gängigsten Wildcards sind ***#*** und ***+***.  
Angenommen, das *Topic* ist folgendermaßen aufgebaut.  

     myhome/client/sensors/temperature

Dann kann man anschließend die angeforderten *Topics* mit den Wildcards anpassen.  
  
Hier werden alle Attribute,  
welche unter "myhome/client/sensors" gesendet werden, empfangen.  

     myhome/client/sensors/#  

Hier werden alle Sparten (inkl. aller Attribute),  
welche unter "myhome/client" gesendet werden, empfangen.  

     myhome/client/#  

Hier wird alles unterhalb der Rubrik,  
welche unter "myhome" gesendet werden, empfangen.  

     myhome/#  

Hier werden, von allen Clients, die *Payloads* mit der Rubrik,  
der Sparte und dem Attribut,  
welche unter "myhome/client(a/b/c/...)/sensors/temperature" gesendet werden,  
empfangen.  

     myhome/+/sensors/temperature  

Hier werden, von allen Clients, die *Payloads* mit der Rubrik,  
und der Sparte,  
welche unter "myhome/client(a/b/c/...)/sensors" gesendet werden, empfangen.  

     myhome/+/sensors/#  

Wie zu erkennen ist, kann man die Wildcards auch kombinieren.  

Der Aufbau der *Topics* und die Verwendung von Wildcards,  
wie sie innerhalb des Adapters verwendet werden,  
ist der jeweiligen Adapterreferenz zu entnehmen.  

Zum besseren Verständnis, sind unter [Best Practice / Tutorial](#best-practice--tutorial)  
ein paar Beispiele aufgeführt.  

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
 - Eindeutige IDs verwenden
 - Konfiguration des Ports beachten  
 - Tools erwähnen  
 - eventuell Link zu einem Tutorial  

###### [zurück](#Inhalt)

## Issues
?> todo:  
 - auf derzeitige Probleme hinweisen  

###### [zurück](#Inhalt)
