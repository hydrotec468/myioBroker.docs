| titel | last change | from |
| -------- | -------- | -------- |
| MQTT | 10.02.2022 | [@hydrotec](https://forum.iobroker.net/user/hydrotec) |

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

[<https://mqtt.org/>][]  
[<https://de.wikipedia.org/wiki/MQTT>][]  

###### [zurück zu Inhalt](#Inhalt)

## Protokoll

?> **Hinweis**:  
     In diesem Abschnitt wird nur auf den gängigsten Weg, MQTT zu nutzen, eingegangen.  
	 Es bestehen viel mehr Möglichkeiten, wie man MQTT nutzen kann.  
	 Hier alle Möglichkeiten aufzuzählen, würde den Rahmen dieser Dokumentation überschreiten.  


![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_01.drawio.png)  
  
Der MQTT-Broker ist die zentrale Anlaufstelle für die MQTT-Clients. 
Ein Client muss an dem Broker angemeldet sein, damit dieser seine *Payloads* 
über die *Topics* dem Broker mitteilen (*Publish*) oder sie empfangen (*Subsribe*) kann. 
Der Broker akzeptiert generell sämtliche *Payloads* welche ein Client über die *Topics* mitteilt. 
Sofern zu einem *Topic* eine *Subscripton* eines Clients existiert wird die Nachricht 
(*Topic* und *Payload*) an diesen Client übermittelt. 
Damit ein Client die von anderen Clients zur Verfügung gestellten *Payloads* 
empfangen kann, muss er zuerst die zugehörigen *Topics* abonnieren.  

### Begriffe

Was steckt hinter den Begriffen *Topic*, *Payload* und *abonnieren*?  

**Topic**

*Topics* können zur Vereinfachung hierarchisch, ähnlich wie Verzeichnisstrukturen, aufgebaut sein. 
Diese Struktur wird vom Client vorgegeben. 
Die Hierarchien sind dabei nicht vorgegeben oder standardisiert. 
So kann es z.B. `<Rubrik>/<Gerät>/<Sparte>/<Attribut>` genau so geben, wie `<Gerät>/<Rubrik>/<Sparte>/<Attribut>` 
Hersteller von Geräten geben diese Struktur (angepasst an ihre Produktpalette) vor. 
In Situationen bei der die Kommunikation durch den Anwender festgelegt wird, übernimmt dieser die Rolle des Herstellers. 

?>Tipp
Eine einheitliche Struktur erleichtert die spätere Übersicht und Weiterverarbeitung.
Üblicherweise wird bei den Herstellern `<Rubrik>/<Gerät>/<Sparte>/<Attribut>` verwendet.
z.B. `myhome/MultiSensor1/sensors/temperature`


  
**Payload**

*Payload* bezeichnet den Inhalt der Nachricht. 
Es gibt für den Inhalt keine festen Vorgaben wie dieser strukturiert werden soll. 
Zwei Methoden haben sich zu einem "quasi Standard" etabliert. 
Bei strukturiertem *Payload* liegt zumeist eine Formatierung in *JSON* vor, 
so das in einem Satz mehrere Informationen übergeben werden können. 
Wogegen das Format *attribute* nur eine Information beinhaltet, 
und mehrere Informationen über die Struktur der *Topics* verteilt werden.  

Beispiele für verschiedene *Payloads* sind

- Format *JSON*: `{"state": "online"}`  
- Format *atribute*:  `online`  


  
**Publish**

Der Client veröffentlicht über diesen Kanal seine Informationen an den Broker. 
Die gesendete Information beinhaltet *Topics* und *Payloads*. 


  
**Subscription** / **abonnieren**

Damit der Broker weiß, welche Nachrichten ein Client bekommen möchte, 
muss der Client die Nachrichten anfordern (*abonnieren*). 
Das geschieht durch die Übermittlung des gewünschten *Topic* in dem Kanal *Subscribe*. 
Bei der Bezeichnung des *Topics* sind Wildcards erlaubt, so das mit einer Nachricht vom Client 
eine Gruppe von *Topics* *subscribed* werden können. 
Jedes mal wenn der Broker eine Nachricht zu einem dieser *Topics* erhält, 
leitet er dieses an den entsprechenden Client weiter. 
Die zwei gängigsten Wildcards sind ***#*** und ***+***. 
Die Wildcard ***#*** steht dabei für beliebige *Topics* unterhalb des fest vorgegebenen Anteils, 
während ***+*** eine einzelne Ebene im Topic markiert.  
Dieses ist am Besten an einigen Beispielen zu erklären:  

Angenommen, der Client 'MultiSensor1' *publisht* mehrere *Topics* nach dem folgenden Muster:
```
myhome/MultiSensor1/sensors/temperature
myhome/MultiSensor1/sensors/humidity
myhome/MultiSensor1/sensors/pressure
myhome/MultiSensor1/status
```  

Durch einen gezielten Einsatz von Wildcards kann man mehrere *Topics* zusammengefasst abonnieren. 

**Wildcard** ***#***  
Alle *Topics* welche unter "myhome" gesendet werden.  
`myhome/#`

Nur den Client "myhome/MultiSensor1" abonnieren.  
`myhome/MultiSensor1/#`

Eingegrenzt auf die Sensoren.  
`myhome/MultiSensor1/sensors/#`

**Wildcard** ***+***  
Um die Temperaturwerte von mehreren MultiSensoren die nach dem oben angegebenen Schema 
ihre Werte *publishen* zu erhalten, ist folgender *subscribe* der einfachste Weg.  
`myhome/+/sensors/temperature`

**Wildcard Kombination** ***#*** und ***+***  
Alle Werte (Temperatur, Luftfeuchtigkeit, Luftdruck) von allen MultiSensoren empfangen.  
`myhome/+/sensors/#`

Der Aufbau der *Topics* im Detail und die Verwendung von Wildcards wie sie innerhalb des Adapters 
verwendet werden, kann - soweit sie vom Adapter vorgegeben werden - der jeweiligen Adapterreferenz entnommen werden.  

Zum besseren Verständnis, sind unter [Best Practice / Tutorial](#best-practice--tutorial) 
ein paar Beispiele aufgeführt.  

###### [zurück zu Inhalt](#Inhalt)

## Adapter
Aktuell stehen in ioBroker drei Adapter unter dem Repository "*default*", 
und vier Adapter unter dem Repository "*latest*" zur Verfügung. 
Mit diesen Adaptern können Geräte, welche das *MQTT-Protokoll* sprechen, 
zu ioBroker angebunden werden können.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_01.png)  

1.) [hass.mqtt](https://github.com/smarthomefans/ioBroker.hass-mqtt#readme "https://github.com/smarthomefans/ioBroker.hass-mqtt#readme")  

| **Repository** | [![](http://iobroker.live/badges/hass-mqtt-stable.svg) ![](http://img.shields.io/npm/v/iobroker.hass-mqtt.svg)](https://www.npmjs.com/package/iobroker.hass-mqtt "https://www.npmjs.com/package/iobroker.hass-mqtt")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/smarthomefans/ioBroker.hass-mqtt/graphs/code-frequency "https://github.com/smarthomefans/ioBroker.hass-mqtt/graphs/code-frequency") |

 - wird in Verbindung zu *Home Assistant* genutzt  
 - unterstützt nur Schalter und Sensoren
 - baut auf den Adapter "MQTT Broker/Client" auf  

2.) [Sonoff](https://github.com/ioBroker/ioBroker.sonoff#readme "https://github.com/ioBroker/ioBroker.sonoff#readme")

| **Repository** | [![](http://iobroker.live/badges/sonoff-stable.svg) ![](http://img.shields.io/npm/v/iobroker.sonoff.svg)](https://www.npmjs.com/package/iobroker.sonoff "https://www.npmjs.com/package/iobroker.sonoff")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/ioBroker/ioBroker.sonoff/graphs/code-frequency "https://github.com/ioBroker/ioBroker.sonoff/graphs/code-frequency") |

 - wird in Verbindung zu *Tasmota* und *ESP* genutzt  
 - kann **nicht** als MQTT-Broker für andere Geräte verwendet werden
 - der intern gestellte Broker akzeptiert nur vorgegebene Topics  
   (können durch Anfragen erweitert werden)  

3.) [MQTT Broker/Client](https://github.com/ioBroker/ioBroker.mqtt#readme "https://github.com/ioBroker/ioBroker.mqtt#readme")

| **Repository** | [![](http://iobroker.live/badges/mqtt-stable.svg) ![](http://img.shields.io/npm/v/iobroker.mqtt.svg)](https://www.npmjs.com/package/iobroker.mqtt "https://www.npmjs.com/package/iobroker.mqtt")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/ioBroker/ioBroker.mqtt/graphs/code-frequency "https://github.com/ioBroker/ioBroker.mqtt/graphs/code-frequency") |

 - kann sowohl als *Client*, wie auch als *Broker* konfiguriert werden  
 - der *Broker* übernimmt auch *Client* Aufgaben  
 - in der *Client* Version, wird ein vorhandener *Broker* (z.B. [mosquitto](https://mosquitto.org "https://mosquitto.org") ) vorausgesetzt  
 - spricht grundsätzlich mit allen Geräten, welche über das MQTT-Protokoll kommunizieren  
 
4.) [MQTT-Client](https://github.com/Pmant/ioBroker.mqtt-client#readme "https://github.com/Pmant/ioBroker.mqtt-client#readme")

| **Repository** | [![](http://iobroker.live/badges/mqtt-client-stable.svg) ![](http://img.shields.io/npm/v/iobroker.mqtt-client.svg)](https://www.npmjs.com/package/iobroker.mqtt-client "https://www.npmjs.com/package/iobroker.mqtt-client")|
| -------- | -------- |
| **Code frequency** | [GitHub Insights](https://github.com/Pmant/ioBroker.mqtt-client/graphs/code-frequency "https://github.com/Pmant/ioBroker.mqtt-client/graphs/code-frequency") |

 - reiner MQTT Client  
 - es wird ein vorhandener *Broker* (z.B. [mosquitto](https://mosquitto.org "https://mosquitto.org") ) vorausgesetzt  
 - spricht grundsätzlich mit allen Geräten, welche über das MQTT-Protokoll kommunizieren  

### Resümee

Alle Adapter sind für das wozu sie erstellt wurden geeignet. Die Entscheidung, 
welcher Adapter für einen der Richtige ist, kommt auf die gewünschten Anforderungen an. 
Wer neben ioBroker noch Home Assistant mit *MQTT Discovery* nutzt, sollte **hass-mqtt** näher betrachten. 
Wenn Tasmota oder ESP Geräte eingebunden werden, dann ist **Sonoff** die logische Wahl. 
Um allgemein MQTT zu nutzen ist **MQTT Broker/Client** der empfohlene Adapter. 
**MQTT Broker/Client** nimmt eine Sonderstellung unter den Adaptern ein. 
Er kann sowohl als Broker, wie auch als Client konfiguriert werden. 
Sollte noch keine MQTT Umgebung vorhanden, bzw. noch kein Broker (z.B. mosquitto) eingerichtet sein, 
dann empfiehlt es sich den Adapter als Broker zu nutzen. 
Ansonsten, wenn ioBroker in eine bestehende MQTT Landschaft integriert werden soll, 
ist der Client die bessere Auswahl. 
Damit die Status von Objekten anderer Adapter in ioBroker per MQTT kommunizieren können, 
der ist mit dem Adapter **MQTT-Client** gut bedient. 
Es ist auch möglich mehrere MQTT Adapter/Instanzen parallel zu betreiben. 
Das erfordert allerdings genauerer Beachtung der Einstellungen, 
damit die Adapter/Instanzen untereinander nicht im Konflikt stehen.  

?> **Hinweis**:  
 - Werden mehrere MQTT-Broker parallel betrieben, ist es notwendig das jeder Broker auf einen anderen Port hört.  
 - Möchte man mehrere MQTT-Clients einsetzen, muss jeder Client seine eigene, unverwechselbare Client-ID besitzen.  
 - bei der Nutzung mehrerer Instanzen darauf achten, das die Topics sich nicht überschneiden.  

###### [zurück zu Inhalt](#Inhalt)

## Best Practice / Tutorial
* [Broker/Server](#BrokerServer)
* [Client/Subscriber](#ClientSubscriber)
* [Tools](#Tools)

### Beispiel Konfigurationen

Am Beispiel des Adapters **MQTT Broker/Client** folgend zwei Ersteinrichtungen zu den Konfigurationen *Broker* und *Client*. 
Die Einstellungen basieren auf der Standard Ersteinrichtung der Instanz. 
Genaueres zu den Einstellungsmöglichkeiten kann unter der Adapter Referenz 
[MQTT Broker/Client Konfiguration](https://github.com/ioBroker/ioBroker.mqtt#configuration "https://github.com/ioBroker/ioBroker.mqtt#configuration") 
nachgelesen werden.

#### Broker/Server

Zur Installation des Adapters **MQTT Broker/Client** auf der Admin Oberfläche unter *Adapter* nach *mqtt* filtern. 
Innerhalb des Adapters auf das **+** klicken, um eine Instanz zu installieren.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_02.png)  
Nachdem die Instanz erfolgreich installiert wurde, 
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_03.png) 
wird automatisch die Konfigurationsseite der Instanz geöffnet. 
Bis auf *"Typ"* und *"Authentifizierungseinstellungen"* bleibt alles unverändert.  
Bei Typ, durch klick auf den Menüpfeil rechts, *"Server/Broker"* auswählen. 
Und unter den Authentifizierungseinstellungen den Benutzer und das zugehörige Passwort eintragen.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_04.png)  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_05.png)  
Nachdem die Einstellungen mit *"Speichern und Schliessen"* übernommen wurden, muss man die Instanz noch starten.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_06.png)  
Nach dem ersten Start der Instanz wird noch ein Warnhinweis angezeigt. 
Das bedeutet lediglich, das noch kein Client an dem Broker angemeldet ist.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_07.png)  
Die gestartete Instanz legt gleich die ersten Objekte an.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_08.png)  
Die Einstellungen zu dem Broker sind hiermit abgeschlossen. Die Clients können nun an dem Broker angemeldet werden. 
In diesem Beispiel wird das MQTT Tool *mqtt.fx* als Client verwendet. Damit wird ein Temperatursensor simuliert, 
um zu verdeutlichen, wie *Topic* und *Payload* des Clients unter den Objekten angelegt wird.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_09.png)  
Der Client ist erfolgreich an dem Broker angemeldet, und er kann die Daten an den Broker übertragen. 
Beispielsweise der Temperatursensor sendet den Payload "*{"temperature": "22", "unit": "°C"}*" 
in dem Topic "*myhome/TestClient/sensors/temperature*".  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_10.png)  
Dann werden die Objekte in ioBroker folgendermaßen angelegt.  
![](https://raw.githubusercontent.com/hydrotec468/test-md.docs/main/docs/de/media/mqtt_screenshot_11.png)  

###### [zurück zu Best Practice / Tutorial](#best-practice--tutorial)

#### Client/Subscriber

Beispiel einfügen

###### [zurück zu Best Practice / Tutorial](#best-practice--tutorial)

#### Tools

Tools erwähnen

###### [zurück zu Best Practice / Tutorial](#best-practice--tutorial)

###### [zurück zu Inhalt](#Inhalt)

## Issues
?> todo:  
 - auf derzeitige Probleme hinweisen  

###### [zurück zu Inhalt](#Inhalt)


[<https://mqtt.org/>]: https://mqtt.org/  
[<https://de.wikipedia.org/wiki/MQTT>]: https://de.wikipedia.org/wiki/MQTT  
