# DNS Server auf Windows Installieren
Diese Anleitung nimmt an, dass du bereits eine VM mit Windows Server 2019 und einer festen IP (192.168.1.10 bei mir) hast.
## DNS Installieren
1. Starte die Server VM und Melde dich als Administrator an.
2. Drücke auf `Manage` > `Add Roles and Features`
	![img](/Dateien/Bilder/DNS_Windows/Installation/1.png)

3. Wähle die Option `Skip this page bei default` und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Installation/2.png)

4. Wähle die Option `Role-based or feature-based installation` und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Installation/3.png)

5. Drücke einfach next (ausser du möchtest eine spezielle installation durchfüren)
	![img](/Dateien/Bilder/DNS_Windows/Installation/4.png)

6. Wähle die Option `DNS Server` und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Installation/5.png)

7. Bestätige deine Auswahl indem du `Add Features` drückst.
	![img](/Dateien/Bilder/DNS_Windows/Installation/6.png)

8. Drücke einfach `Next`
	![img](/Dateien/Bilder/DNS_Windows/Installation/7.png)

9. Drücke einfach `Next`
	![img](/Dateien/Bilder/DNS_Windows/Installation/8.png)

10. Drücke einfach `Next`
	![img](/Dateien/Bilder/DNS_Windows/Installation/9.png)

11. Wähle die Option `Restart the destination server automatically if required` und drücke `Install`
	![img](/Dateien/Bilder/DNS_Windows/Installation/10.png)

## DNS Konfigurieren
### Forward lookup zone hinzufügen
1. Wähle unter `Tools` die Option `DNS`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/1.png)

2. Drücke mit der Rechten Maustaste auf den Server und Füge eine neue Zone hinzu.
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/2.png)

3. Drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/3.png)

4. Wähle die Option `Primary Zone` und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/4.png)

5. Erstelle eine `Forward lookup zone`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/5.png)

6. Gebe deine Domäne an.
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/6.png)

7. Vergewissere dich das eine neue Datei erstellt wird und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/7.png)

8. Schalte dynamische updates aus und drücke `Next` 
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/8.png)

9. Beende die Konfiguration mit `Finish`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone/9.png)
### Reverse lookup zone hinzufügen
1. Füge eine neue `Reverse Lookup Zone` hinzu
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/1.png)

2. Drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/2.png)

3. Wähle die Option für eine `Primary Zone` und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/3.png)

4. Wähle die Option für eine `IPv4 reverse Lookup Zone` (Ausser du benutzt IPv6)
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/4.png)

5. Gebe deine Network ID ein und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/5.png)

6. Wähle die Erste Option und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/6.png)

7. Schalte Dynamische Updates aus und drücke `Next`
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/7.png)

8. Beende die Konfiguration indem du`Finish` drückst.
	![img](/Dateien/Bilder/DNS_Windows/Konfiguration/add_zone_reverse/8.png)

### Host hinzufügen
1. Füge zu deiner Zone einen neuen A Record hinzu
	![img](/Dateien/Bilder/DNS_Windows/Testen/1.png)

2. Füge die Daten von deinem Computer ein.
	![img](/Dateien/Bilder/DNS_Windows/Testen/2.png)
	- Im obersten Feld kommt der name rein.
		- `server` == `server.mydomain.local`
	- Das oberste Feld kann auch leer gelassen werden.
		- `` == `mydomain.local`
	- Wähle auch die unterste Checkbox, diese sorgt dafür das auch ein passender PTR Record (fü den reverse lookup) hinzugefügt wird.

## DNS Testen
Um den DNS Server zu testen muss man eine cmd auf dem server, oder auf einem anderen client im gleichen Netzwerk, öffnen. Es st einfach wichtig das der dns server auf die IP von unserem server manuel geändert wird.

Mit fiesem Befehl kann man einen normalen dns Request machen.
```
nslookup server.mydomain.local
```
Output:
```
Server: server.mydomain.local
Adress: 192.168.1.10

Server: server.mydomain.local
Adress: 192.168.1.10
```

Mit diesem Befehl kann man einen reverse lookup machen.
```
nslookup 192.168.1.10
```
Output:
```
Server: server.mydomain.local
Adress: 192.168.1.10

Server: server.mydomain.local
Adress: 192.168.1.10
```