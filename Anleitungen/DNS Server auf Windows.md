# Anleitung zum installieren vom DNS Server
---
## Virtual Box NAT Network erstellen
1. Öffne Virtual-box
2. Gehe auf "Tools"
	![](/Dateien/Bilder/DNS_Windows/02.png)
3. Wähle "NAT Network" und Erstelle ein neues Netzwerk
	![](/Dateien/Bilder/DNS_Windows/03.png)
4. Passe die Einstellungen vom Netzwert an.
	- Ändere den Namen 
	- Ändere den IPv4 Prefix
	- Schalte den DHCP Server aus
	![](/Dateien/Bilder/DNS_Windows/04.png)
---
## Windows Server VM erstellen
1. Erstelle eine neue VM
	![](/Dateien/Bilder/DNS_Windows/5.png)
2. Gebe der VM einen sinnvollen Namen und Wähle das ISO Image das du Heruntergeladen hast. Wähle auch die Option "Skip unattended installation"
	![](/Dateien/Bilder/DNS_Windows/6.png)
3. Gebe der VM eine sinnvolle anzahl an Ressourcen.
	![](/Dateien/Bilder/DNS_Windows/7.png)
4. Drücke "Next", "Next" und "Finish"
---
## Windows Server installieren
1. Starte die VM die du vorher erstellt hast.
	![](/Dateien/Bilder/DNS_Windows/8.png)
2. Wähle dein bevorzugtes Layout und drücke "Next".
	![](/Dateien/Bilder/DNS_Windows/9.png)
3. Drücke "Install now".
4. Gebe deinen Licence Key ein und drücke "Next".
	![](/Dateien/Bilder/DNS_Windows/10.png)
5. Wähle windows Server 2019 Standard (Desktop Experience) und drücke "Next".
	![](/Dateien/Bilder/DNS_Windows/11.png)
5. Drücke auf das Kästchen und dann "Next"
	![](/Dateien/Bilder/DNS_Windows/12.png)
6. Wähle die untere Option
	![](/Dateien/Bilder/DNS_Windows/13.png)
7. Drücke auf "Next"
	![](/Dateien/Bilder/DNS_Windows/14.png)
8. Wen alle diese schritte erfolgreich erledigt wurden wird sich der Server installieren.
---
## DNS Server installieren
1. Wähle auf dem "Dashboard" unter "Manage" die Option "Add Roles and Features".
	![](/Dateien/Bilder/DNS_Windows/15.png)
2. Drücke auf "Next"
	![](/Dateien/Bilder/DNS_Windows/16.png)
3. Wähle die Option "Role-based...." weil der DNS Server auf dem lokalen server installiert werden soll und drücke next
	![](/Dateien/Bilder/DNS_Windows/17.png)
4. Wähle deinen Server aus und drücke "Next".
	![](/Dateien/Bilder/DNS_Windows/18.png)
5. Wähle die Option für den DNS Server und drücke "Next"
	![](/Dateien/Bilder/DNS_Windows/19.png)
6. Drücke einfach "Next"
	![](/Dateien/Bilder/DNS_Windows/20.png)
7. "Next"
	![](/Dateien/Bilder/DNS_Windows/21.png)
8. Wähle die obere Option, sie startet den Server automatisch neu falls es nötig ist. Drücke auf "Instal"
	![](/Dateien/Bilder/DNS_Windows/22.png)
---
## DNS Server konfigurieren
1. Gehe unter "Tools" auf "DNS" um den DNS Manager zu öffnen.
	![](/Dateien/Bilder/DNS_Windows/23.png)
### DNS Forward lookup Zone
1. Drücke mit der Rechten Maustaste auf die "Forward Lookup Zone" und wähle die Option "New Zone..."
	![](/Dateien/Bilder/DNS_Windows/24.png)
3. Wähle im "New Zone Wizard" die option "Primary Zone"
	![](/Dateien/Bilder/DNS_Windows/25.png)
4. Gebe deine Domain ein und drücke "Next"
	![](/Dateien/Bilder/DNS_Windows/26.png)
5. Drücke "Next", "Next" und "Finish"
6. Füge einen neuen A record hinzu
	![](/Dateien/Bilder/DNS_Windows/27.png)
7. Gebe deine Daten ein
	![](/Dateien/Bilder/DNS_Windows/28.png)
---
### DNS Reverse lookup Zone
1. füge eine neue "Reverse Lookup Zone" hinzu.
2. "Zone Type" = Primary Zone
3. "Next"
4. Network ID = deine Network ID
5. "Next","Next","Next","Finish"
6.  [Video](/Dateien/Videos/add_reverse_zone.webm)
---