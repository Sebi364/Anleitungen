# DNS auf Fedora installieren und konfigurieren
## Installation & Konfiguration
Ein Teil von dieser Doku basiert auf [dieser](https://fedoramagazine.org/how-to-setup-a-dns-server-with-bind/) Anleitung von [Curt Warfield](https://fedoramagazine.org/author/rcurtiswarfield/).

1. Installiere die nötigen Pakete
    ```bash
    sudo dnf install bind bind-utils -y
    ```
2. Installiere den nano Text editor (optional)
    ```bash
    sudo dnf install nano -y
    ```
3. Öffne die Config-Datei vom dns Server
    ```bash
    sudo nano /etc/named.conf
    ```
4. Füge zu der Linie `listen-on port 53 { 127.0.0.1; };` die statische IP von deinem DNS Server hinzu.
    ```bash
    listen-on port 53 { 127.0.0.1; 192.168.1.10; };
    ```
5. Füge zu der Linie `listen-on port 53 { localhost; };` die IP Range von deinem Netzwerk hinzu.
    ```bash
    allow-query  { localhost; 192.168.1.0/24; };
    ```
6. füge direkt über dieser: `include "/etc/named.rfc1912.zones"` linie den folgenden Text hinzu: <br>
    ```conf
    zone "mydomain.local" IN {
        type master;
        file "forward.mydomain.local";
        allow-update { none; };
    };

    zone "1.168.192.in-addr.arpa" IN {
        type master;
        file "reverse.mydomain.local";
        allow-update { none; };
    };
    ```

7. Öffne die Datei mit der forward lookup zone.
    ```bash
    sudo nano /var/named/forward.mydomain.local
    ```
8. füge den folgenden Text hinzu:
    ```conf
    $TTL 86400
    @   IN  SOA     server.mydomain.local. root.mydomain.local. (
            2011071001  ;Serial
            3600        ;Refresh
            1800        ;Retry
            604800      ;Expire
            86400       ;Minimum TTL
    )
    @	    IN  NS  server.mydomain.local.
    @	    IN  A   192.168.1.10
    server  IN  A   192.168.1.10
    client  IN  A   192.168.1.5
    ```


9. Öffne die Datei mit der reverse lookup zone.
    ```bash
    sudo nano /var/named/reverse.mydomain.local
    ```
10. füge den folgenden Text hinzu
    ```conf
    $TTL 86400
    @   IN  SOA     server.mydomain.local. root.mydomain.local. (
            2011071001  ;Serial
            3600        ;Refresh
            1800        ;Retry
            604800      ;Expire
            86400       ;Minimum TTL
    )
    @	    IN  NS   server.mydomain.local.
    @	    IN  PTR  mydomain.local.
    server  IN  A    192.168.1.10
    client  IN  A    192.168.1.5
    10	    IN  PTR  server.mydomain.local.
    5	    IN  PTR  client.mydomain.local.

    ```

11. bearbeite die Berechtigungen der Dateien:
    ```bash
    sudo chgrp named -R /var/named
    sudo chown -v root:named /etc/named.conf
    sudo restorecon -rv /var/named
    sudo restorecon /etc/named.conf
    ```
12. Konfiguriere die firewall
    ```bash
    sudo firewall-cmd --add-service=dns --perm
    sudo firewall-cmd --reload
    ```
13. Falls du eine Linux distribution verwendest die systemd benutzt musst du systemd-resolved ausschalten, sonst wird er den port 53 besetzen.
    ```
    systemctl disable --now systemd-resolved
    rm /etc/resolv.conf
    systemctl restart NetworkManager
    ```
14. Starte den DNS Service
    ```
    systemctl enable --now named
    ```

## DNS Server Testen
Der dns Server kann mit dig getestet werden <br>
Domain zu IP: <br>
```bash
dig @192.168.1.10 server.mydomain.local
```
Output:
```
; <<>> DiG 9.16.33-RH <<>> @192.168.1.10 server.mydomain.local
; (1 server found)
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37418
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: d24ff102987442620100000063bacfa64e90f87e914d38a2 (good)
;; QUESTION SECTION:
;server.mydomain.local.		IN	A

;; ANSWER SECTION:
server.mydomain.local.	86400	IN	A	192.168.1.10

;; Query time: 0 msec
;; SERVER: 192.168.1.10#53(192.168.1.10)
;; WHEN: Sun Jan 08 15:13:58 CET 2023
;; MSG SIZE  rcvd: 94

```

IP zu Domain
```bash
dig @192.168.1.10 -x 192.168.1.10
```

Output:
```
; <<>> DiG 9.16.33-RH <<>> @192.168.1.10 192.168.1.10
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 12719
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: b014cd4188b9dc230100000063bacfc300d2529435775517 (good)
;; QUESTION SECTION:
;192.168.1.10.			IN	A

;; AUTHORITY SECTION:
.			10800	IN	SOA	a.root-servers.net. nstld.verisign-grs.com. 2023010800 1800 900 604800 86400

;; Query time: 144 msec
;; SERVER: 192.168.1.10#53(192.168.1.10)
;; WHEN: Sun Jan 08 15:14:27 CET 2023
;; MSG SIZE  rcvd: 144

```

## (Bonus) DNS Betrieb mit Wireshark untersuchen
wen man eine domain auflöst wird die IP Adresse auf dem DNS Server im cache gespeichert. Dass kann man mithilfe von Wireshark beobachten.

1. Um Wireshark zu benutzen muss man auf dem Server ein Desktop Enviroment , Display Manager und Wireshark installieren.
    ```
    sudo dnf install gdm wireshark gnome-terminal -y
    ```

2. Starte den display manager damit du dich in den desktop einlogen kannst.
    ```
    systemctl start gdm
    ```
3. Melde dich mit dem Benutzername "root" und deinem Passwort an.

4. füge zu der config Datei vom DNS Server die folgende Linie dazu:
    ```
    max-cache-ttl 30;
    ```
    diese Option setzt die zeit, wie lange der DNS Server eine IP im Cache behaltet auf 30 Sekunden.

5. Starte den DNS Server neu:
    ```
    systemctl restart named
    ```

6. Öffne Wireshark

7. Normalerweise wäre es praktisch nicht möglich den dns vorgang zu beobachten weil Wireshark jedes einzelne packet abfängt, daher brauchen wir einen Filter. <br><br> Gebe auf der oberen leiste mit dem text "Apply a Display Filter" den Folgenden Text ein
    ```
    dns.qry.name contains "google.com"
    ```
    Dieser Filter sorgt dafür das nur die Packete die uns interesieren angezeigt werden. Weitere DNS Wireshark Filter findest du [hier](https://www.wireshark.org/docs/dfref/d/dns.html).

8. Starte den capture und mache einen dns request für google.com <br>
    ![](/Dateien/Bilder/DNS_Fedora/1.png)
    In Wireshark wirst du sehen wie der DNS Server die Domain auflöst.

9. Starte einen weiteren dns request für google.com <br>
    ![](/Dateien/Bilder/DNS_Fedora/2.png)
    dieses mal sind es deutlich weniger Pakete weil der dns server die IP aus dem Cache bezieht.

10. Warte eine halbe Minute und starte einen weiteren dns Request für google.com. <br>
    ![](/Dateien/Bilder/DNS_Fedora/3.png)
    Jetzt löst der dns server die IP wieder auf weil die TTL abgelaufen ist.