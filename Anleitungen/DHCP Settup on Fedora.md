# DHCP Server auf Fedora (VM) installieren

## Vorbereitung
Lade die Folgenden Dateien / Programme herunter und installier sie.
- [Fedora Linux Desktop](https://getfedora.org/en/workstation/) (ISO Datei)
- [Fedora Linux Server](https://getfedora.org/en/server/) (ISO Datei)
- [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) (Programm + Extension Pack)
---
## VirtualBox Netzwerk Configuration
Um einen DHCP Server zu installieren brauchen wir zuerst ein Netzwerk. Es ist möglich in VirtualBox ein "NAT Network" zu erstellen, dass fast alle eigenschaften von einem echten Netzwerk hatt.
1. öffne VirtualBox und gehe auf "Tools" <br>
    ![](/Dateien/Bilder/DHCP_Setup/1.png)
2. wähle "NAT Networks" und erstelle mit "Create" ein neues Netzwerk Ich empfehle bei dem "IPv4 Prefix" "192.168.1.0/24" einzugeben weil alle weiteren Configurationen in dieser Anleitung von dieser IP Range ausgehen <br>
    ![](/Dateien/Bilder/DHCP_Setup/2.png)
    stelle sicher das "DHCP" ausgeschaltet ist.

---

## VM für Server Configurieren

In den folgenden Schritten wird eine neue VM kreiert und zu unserem NAT Netzwerk hinzugefügt.
1. Erstelle eine neue virtuelle Maschiene Gieb ihr irgendeinen Namen und Wähle bei "ISO Images" das Server ISO dass du hoffentlich vorher heruntergeladen hast. <br>
![](/Dateien/Bilder/DHCP_Setup/3.png)

2. Klicke "Next" bis sich das Fenster Schliest.
3. öffne die Einstellungen der VM und wähle den "Netzwerk" Teil<br>
    ändere "Attached to" zu "NAT Network" und wähle unter "Name" das NAT das du vorher gemacht hast.
    ![](/Dateien/Bilder/DHCP_Setup/4.png)
4. Bestätige die änderungen indem su auf "OK" drückst.

---

## OS auf Server installieren
1. Starte die VM, die du gerade gemacht hast und warte bis es bei dier ungefähr wie im Bild unten aussieht. <br>
![](/Dateien/Bilder/DHCP_Setup/5.png)
2. folge diesem video um Fedora zu installieren
![Video](/Dateien/Videos/1.webm)
---

## DHCP Server configurieren


--- 
