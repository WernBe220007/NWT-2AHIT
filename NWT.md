# Netzwerktechnik 2AHIT 2023/24

## Netzwerkadressierung

### Adresskonzept IPV4

IPV4 -> 4Byte, jedes Byte 8bit

00000000.00000000.00000000.00000000 zu 11111111.11111111.11111111.11111111

In Dezimal dargestellt: 0.0.0.0 zu 255.255.255.255

Adressklassen A-E, A ist größte, C ist kleinste netzwerk, DEF ist reserviert

### Adressbereiche

| Klasse | Adressbereich               | Netze       | Adressen |
| ------ | --------------------------- | ----------- | -------- |
| A      | 0.0.0.0 - 127.255.255.255   | 128 (0-127) | 16777216 |
| B      | 128.0.0.0 - 191.255.255.255 | 16384       | 65536    |
| C      | 192.0.0.0 - 223.255.255.255 | 2097152     | 256      |

Vergabe der IP adressen im öffentlichen bereich macht die IANA

| Klasse | Adressbereich               | multicast adresses |
| ------ | --------------------------- | ------------------ |
| D      | 224.0.0.0 - 224.0.0.255     | well-known         |
| E      | 224.0.1.0 - 238.255.255.255 | globally-scoped    |
| F      | 239.0.0.0 - 239.255.255.255 | local              |

Private Adressbereiche

| Klasse | Adressbereich                 | Netze | Adressen |
| ------ | ----------------------------- | ----- | -------- |
| A      | 10.0.0.0 - 10.255.255.255     | 1     | 16581375 |
| B      | 172.16.0.0 - 172.31.255.255   | 15    | 65025    |
| C      | 192.168.0.0 - 192.168.255.255 | 256   | 256      |

### Netzmaske

Die Netzmaske teilt das netwerk in Host und Netzwerkanteil

| Klasse | Maske         |
| ------ | ------------- |
| A      | 255.0.0.0     |
| B      | 255.255.0.0   |
| C      | 255.255.255.0 |

CIDR Schreibweise ist die Anzahl von 1ern an die Netzwerkadresse geschrieben (eg: 192.168.1.0/24)

IP-Adresse und Subnetzmaske werden in Binär logisch addiert und resultierend ist wo Host und wo Netzteil ist.

|            | 192.168.0.21/24                   |          |          |          |
| ---------- | --------------------------------- | -------- | -------- | -------- |
| IP Adresse | 11000000                          | 10101000 | 00000000 | 00010101 |
| Netzmaske  | 11111111                          | 11111111 | 11111111 | 00000000 |
| 1+1=1      | 11000000                          | 10101000 | 00000000 |          |
| Netzwerk   | 192                               | 168      | 0        | 0        |
| Hosts      | $2⁸$ = 256 &rarr; (0..255) 1..254 |          |          |          |

BSPB:

| Klasse C:    | 192.12.50.0/24  |
| ------------ | --------------- |
| Netzmaske:   | 255.255.255.0   |
| Netzadresse: | 192.12.50.0     |
| Gateway:     | 192.12.50.1     |
| IP-Adressen: | 192.12.50.2-254 |
| BC-Adresse:  | 192.12.50.255   |

1. Teilnetz
   
   | Netzwerkadresse: | 192.12.50.0     |
   | ---------------- | --------------- |
   | Subnetzmaske:    | 255.255.255.128 |
   | Gateway:         | 192.12.50.1     |
   | IP-Adressen:     | 192.12.50.2-126 |
   | BC-Adresse:      | 192.12.50.127   |

2. Teilnetz

| Netzwerkadresse: | 192.12.50.128     |
| ---------------- | ----------------- |
| Subnetzmaske:    | 255.255.255.128   |
| Gateway:         | 192.12.50.129     |
| IP-Adressen:     | 192.12.50.130-254 |
| BC-Adresse:      | 192.12.50.255     |

- Netzwerkadresse:    192.12.50.128
  Subnetzmaske:    255.255.255.128
  Gateway:    192.12.50.129
  IP-Adressen:    192.12.50.130-254
  BC-Adresse:    192.12.50.255Subnetze verrringert die Anzahl von zuordnungsfähigen Hostadressen (-3 Adressen)

- Alle Rechner eines Netzwerksegmentes müssen dessen Subnetzmaske verwenden

- Übergeordnete Netze sehen diese Adressen als ganz normale IP-Adressen

### Link-Local (Autoconfiguration)

Selbstständige konfiguration von Clients im einem Netzwerk ohne DHCP server, statischer Configuration oä.

| IP bereich: | 169.254.0.0 - 169.254.255.255 |
| ----------- | ----------------------------- |
| Netzmaske:  | /16                           |

## Netzwerkprotokolle ARP/DHCP/Ping

### ARP → Adress Resolution Protocol → Layer 2 (OSI)

- Übersetzt IP-Adressen in Hardwareadressen

- Hardwareadresse des Ziels muss bekannt sein

- Feste Hardwareadresse; Mac adresse

###### Um zu senden ist eine Adress auflösung erforderlich:

Absender sendet ARP-Request mit (Ziel)Adresse FF-FF-FF-FF-FF-FF an alle (in der Broadcast domain, abgeschrenkt durch Bridge bzw Router), jeder nimmt diese request entgegen und wertet sie aus (Schaut sich die IP Adresse im Ethernet frame an), sollte dieser Host gemeint sein (identische IP) schickt dieser eine ARP-Reply mit eigener zurück, die Mac adresse wird im cache des absenders gespeichert (Dient zur schnelleren ARP auflösung), dieser cache hat auch ein verfalls datums

#### Ablauf des Protokolls

1. Broadcast mit eigener Mac adresse, Ziel IP Adresse und einer Dummy Mac adresse (irgendwas muss als ziel drinstehen, auch wenns an alle gesendet wird)

2. Antwort des Zielrechners ((Ziel)IP adresse ist die eigene) mit eigener IP-Adresse, eigener Mac-Adressse, Quell IP Adresse und Quell Mac adresse

### DHCP → Dynamic Host Configuration Protocol → Layer 3 (OSI)

- Zuweisung der Netzwerkkonfiguration an clients durch einen DHCP Server

- Definiert in RFC2131

- Verwendet die Ports 67-68/UDP

In einem Netzwerk benötigt jeder client eine IP Adresse, der DHCP server verwaltet automatisch IP-Adressen und schließt konflikte aus. Mit DHCP ist jeder Teilnehmer in der Lage sich selbstendig  und automatisch zu Konfigurieren.

###### DHCP verwaltet folgende Einstellungen:

- Vergabe einer eindeutigen IP Adresse

- Zuweisen der Subnetzmaske

- Zuweisen des zuständigen Gateways

Zur kommunikation sind jedoch nur IP Adresse und Subnetzmaske notwendig.

DHCP hat eine typische Client-Server-Architektur, der Client muss beim Server nachfragen. Der DHCP-Server merkt sich die Mac Adresse von jedem Client und bei erneutem verbinden wird die selbe IP Adresse zuerst angeboten (sovern verfügbar; also wenn das netzwerk voll ist wird die "reservierung" verloren). Nachdem die Lease Time abgelaufen ist, wird die Reservierung auch verworfen bzw, sollte der Client noch verbunden sein wird eine neue IP Adresse zugewiesen.

In größeren Netzwerken muss der DHCP-Server auch das Gateway und die verschiedenen Subnetze wissen. Für eine Erfolgreiche DHCP Architektur in einem Netzwerk muss mindestenens ein DHCP-Server verfügbar sein und es darf nur einen geben. (Außnamefall: Redundante Server)

In heimnetzwerken ist im selben gerät wo der DHCP Server rennt auch ein Router oder NAT.

DHCP wird über TCP/IP abgewickelt.

#### Ablauf des Protokolls

Der DHCP Client startet die Communication.

1. DHCP Discover (Client)
   
   Der DHCP-Client verschickt ein UDP-Paket mit der Ziel-Adresse 255.255.255.255 (Broadcast) und der Quelladresse 0.0.0.0. Diese request erreicht alle DHCP-Server im Netzwerk, im idealfall gibt es jedoch nur einen.

2. DHCP Offer (Server)
   
   Der DHCP-Server antwortet auf den Broadcast mit einer freien IP-Adresse und weiteren Parametern. Er schickt ein UDP mit den folgenden Daten zurück:
   
   - MAC-Adresse des Clients
   
   - mögliche IP-Adresse
   
   - Laufzeit der IP-Konfiguration (Lease Time)
   
   - zugehörige Subnetzmaske
   
   - IP-Adresse des DHCP-Servers

3. DHCP Request (Client)
   
   Der DHCP-Client entscheidet sich ob er die Konfiguration annimmt oder nicht. Wenn ja, verschickt er eine positive Meldung an den betreffenden DHCP-Server. Lehnt der Client ab, wird eine neue offer erzeugt. Diese request ist ein TCP-Paket.

4. DHCP Ack (Server)
   
   Anschließend wird die Vergabe der IP-Adresse vom DHCP-Server bestätigt. Kann der Client weitere Angaben auswerten, werden diese auch übermittelt (eg DNS). Dies erfolgt erneut über TCP. Dannach wird der TCP/IP Stack gestartet.

#### DHCP-Refresh:

In der DHCP-Ack Nachricht ist die Lease-Time angegeben, die gibt an, wie lange der Client die IP-Adresse verwenden darf. Bei der Hälfte der LeaseTime schickt der DHCP-Client eine erneute DHCP-Request and den Server, dieser antwortet meist mit einem DHCP-ACK mit identischen Daten und neuer Leasetime. (Verlängert die Nutzdauer der IP-Adresse). Antwortet der DHCP-Server nicht, ist die Konfiguration nicht verlängert und der Client verwendet die Adresse bis zum Ende der LeaseTIme, bis zum Ablauf wird jedoch immerwieder versucht die Adresse zu verlängern.

#### DHCP-Not Acknowledged

Sollte der DHCP-Server  keine Adressen mehr zur Verfügung haben oder wird die Adresse während des vorgangs an einen anderen Client vergeben, so sendet der Server ein DHCP- Not Acknowledged (DHCP-NACK). Diese antwort ist auch TCP.

#### Übergabeparameter

Ein DHCP-Server kann einem Client unter anderem auch folgende einstellungen zuweisen:

- IP-Adresse und Netzmaske

- Reservierte IP-Adressen

- Default-Gateway

- DNS-Server

- NTP-Server

- Proxy-Server

- Domainname (für Windows Clients)

- WINS-Server (für Windows Clients)

- TFTP-Server

- PXE-Server

- ...

### Ping/ICMP

Ping wird zum Prüfen der Erreichbarkeit von anderen Rechnern im Netzwerk verwendet. Ping nutzt das ICMP Protokoll. Dieses Protokoll ist ein Hilfsprotokoll und ist auf Layer 3 angesiedelt.

Der angepingte Netzwerkteilnehmer beantwortet eine kurze Anfrage durch eine ebenso kurze Antwort. Somit ist gezeigt, dass dieser grundsätzlich erreichbar ist.

Ping ist ein grundlegender Befehl und fast überall verfügbar.

#### Ablauf des Protokolls

Ping sendet ein ICMP-Paket namens Echo-Request an die Zieladresse. Der empfänger muss ein ICMP-Paket, der ECHO-Reply (pong), zurücksenden. Ist der Host nicht erreichbar, muss der zuständige Router mit "Network unreachable" oder "Host unreachable" antworten. 

Keine Antwor kann jedoch nicht ausschließen, dass die Gegenstelle erreichbar ist. Manche hosts sind so konfiguriert, dass die ICMP Pakete ignorieren. (Firewall, etc)

## Bridging

Eine Bridge trännt kollisionsdomänen. Arbeiter auf Layer 2 und liest Mac Adressen aller Rechner, welche beider seiten der Bridge angeschlossen sind. Die Bridge merkt sich auf welcher seite der Rechner sich befindet. Sie entscheidet ob ein Paket auf die andere Seite weitergeileitet wird, oder nicht. Brodcasts (eg ARP) müssen weitergeleitet werden. Somit wird die Anzahl der Collisionen verringert. 

Jede Seite der Bridge bekommt ein eigenes CSMA/CD. Um ein Paket weiter zu leuten muss auch die Brdige warten, bis dass, das Medium frei ist. Die Bridge muss daher Puffern können. Dadurch kann eine Bridge auch zwischen verschiedenen Zugriffsverfahren vermitteln, eg TokenRing, Busse, etc.

Durch die Aktivierung von Filterfunktionen in der Bridge ist es möglich, für bestimmte Stationen den Zugang zu anderen Subnetzen zu sperren. Oft lassen sich auch Filter für bestimmte Steuerinformationen setzen.

### Local Bridges:

Local Bridges verfügen über Zwei Anschlüsse für LANs. Sie verbunden damit auf direketem Weg zb Zwei Lans innerhalb eines Unternehmens. Jede Seite verfügt über ein eigenes CSMA/CD. Sie liest die MAC-Adressen der Hosts und erkennt so, auf welcher Seite welche Hosts angeschlossen sind. Daten dieser Hosts leitet die Bridge in das Zielsegment weiter. Broadcast Pakete werden jedoch immer weitergeleitet.

#### MAC-Bridge

Die MAC-Bridge verbindet Subnetze mit gleicher MAC-Ebene, zb Ethernet und Ethernet oder Ethernet und Tokenring, also Netze mit gleichem Zugriffsverfahren. Eine MAC-Bridge wird hauptsächlich eingesetzt um eine Netzwerk in mehrer Collisionsdomänen einzuteilen. So kann die Paketlast in großen Netzwerken vermindern, da jeder Strang nur daten transportiert, dessen Empfänger auch am Strang ist.

#### LLC-Bridge

Eine LLC-Bridge (Logical Link Control) wird verwendet, um zwei Teilnetze mit verschiedenen Zugriffsverfahren (zb LAN und WLAN) zu koppeln. Sie besteht aus zwei Teilen, die miteinander verbunden sind, wobeu das Medium zwischen beiden Teilen nicht beliebig ist. Innerhalb der LLC-Bridge findet daher eine Umsetzung statt. Bei dieser Umsetzung müssen alle Parameter des Quellnetzes (wie MAC-Adresse, Größe, Afbau des MAC-Frames, etc) an das Zielnetz angepasst werden. Bei Inkompatabilität der Parameter kann keine Bridge verwendet werden.

### Remote Bridges:

Remote Bridges koppeln Subnetze über eine Weitverkehrsstrecke (WAN) miteinander. Sie sind daher mit einem Lan und einem Wan Port ausgestattet. Auf beide Seiten der WAN-Strecke muss dazu je eine Brücke mit gleichen Funktionen, am besten Bridges des gleichen Herstellers, installiert sein.

### Multiport Bridges

Vielschichtige Vernetzungen erfordern Brücken mit Anschlussmöglichkeit für mehrere Netze. Diese Multiport Bridges ermöglichen die sternförmige Kopplung mehrerer LANs. Zur Erhöhung der Anpassungsfähigkeit haben sie häufig einen modularen Aufbau. Multiport Bridges können auch mit Anschlüssen für Weitverkehrsnetze sein.

### Schleifenbildung

In einem mit Brücken aufgebauten Datennetz dürfen keine Schleifen bzw paralelle Brücken gebildet werden. Hierdurch können endlos kreisende Datenpakete entstehen. Ist aus Verfugbarkeitsgründen eine Schleifenbildung durch redundante Pfade erforderlich, sind Brücken mit einem eingebauten Schleifenunterdrückungsverfahren zu verwenden.

Dieses Verfahren wird auch Spanning Tree Protokoll (STP) gennant. Die Brücken tauschen nach dem Einschalten und später in einem einstellbaren Zeitintervall Informationen über den Netzzustand aus. Wird hierbei festgestallt, dass eine schleife vorhanden ist, wird eine der paralell liegenden Brücken gesperrt. Dabei nimmt diese aber weiterhin am Spanning Tree Verfahren teil und kann beim Ausfall einer anderen Brücke wieder aktiviert werden. Die Funktion des Netzes bleibt dabei erhalten.

## Switching

Der Switch ist ein Gerät, das ein Netzwerk in viele einzelne Kossionsdomänen segmentiert. Ein Switch kommt vom Hub, hat aber auf jedem Port und intern eine Bridge verbaut. Intern ist ein matrixartiges Bussystem eingebaut, das die elektrischen verbundgen realisiert. Da so alle angeschlossenen Geräte über eine eigene Bridge kommunizieren, hat jeder Anschluss des Gerätes eine eigene CSMA/CD Umgebung. An jedem Port eines Switches ist die volle länge des Kabels verfügbar (100m).

##### Vorteile:

- Massive Abnahme an kollisionen (Broadcasts müssen jedoch weitergeleitet werden)

- ARP Requests werden nur noch aus dem Zielport ausgegeben (Da wo der Zielhost ist)

- Ist ein Gerät an einem Switch angeschlossen, kann Collison Detection abgeschaltet werden &rarr; Umschalten auf Vollduplex möglich da alle Drähte frei sind

##### Funktionsweise

Nach der anfänglichen Kommunikation zweier Geräte weiß die Bridge, auf welchen Seiten der Sender und empfänger angeschlossen ist. Ein Switch hat viele interne Bridges, dieser funktioniert ähnlich

##### Unterschiede

Switch ist nicht gleich Switch! Ein switch kann sich unterscheiden im Layer (Layer 2, Layer3) . Layer 3 Switches bringen oft Routing oder DHCP. Die Größe des MAC-Adressen Tables kann sich unterscheiden. Ist er VLAN fähig....
