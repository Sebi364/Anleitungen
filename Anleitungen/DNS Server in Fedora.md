# DNS auf Fedora installieren und konfigurieren
## Installation & Konfiguration
1. Installiere die nötigen Pakete
    ```bash
    sudo dnf install bind bind-utils -y
    ```
2. Installiere den nano Text editor
    ```bash
    sudo dnf install nano -y
    ```
3. Öffne die die config Datei vom dns Server
    ```bash
    sudo nano /etc/named.conf
    ```
4. Füge zu der Linie `listen-on port 53 { 127.0.0.1; };` die statische IP von deinem DNS Server hinzu.
    ```bash
    listen-on port 53 { 127.0.0.1; 192.168.1.10; };
    ```
4. Füge zu der Linie `listen-on port 53 { localhost; };` die IP Range von deinem Netzwerk hinzu.
    ```bash
    allow-query  { localhost; 192.168.1.0/24; };
    ```
5. füge direkt über dieser: `include "/etc/named.rfc1912.zones"` linie den folgenden Text hinzu: <br>
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

6. Öffne die Datei mit der forward lookup zone.
    ```bash
    sudo nano /var/named/forward.mydomain.local
    ```
7. füge den folgenden Text hinzu:
    ```conf
    $TTL 86400
    @   IN  SOA     server.mydomain.local. root.mydomain.local. (
            2011071001  ;Serial
            3600        ;Refresh
            1800        ;Retry
            604800      ;Expire
            86400       ;Minimum TTL
    )
    @	IN  NS          server.mydomain.local.
    @	IN  A           192.168.1.10
    server          IN  A   192.168.1.10
    client          IN  A   192.168.1.5
    ```


8. Öffne die Datei mit der reverse lookup zone.
    ```bash
    sudo nano /var/named/reverse.mydomain.local
    ```
9. füge den folgenden Text hinzu
    ```conf
    $TTL 86400
    @   IN  SOA     server.mydomain.local. root.mydomain.local. (
            2011071001  ;Serial
            3600        ;Refresh
            1800        ;Retry
            604800      ;Expire
            86400       ;Minimum TTL
    )
    @	IN  NS          server.mydomain.local.
    @	IN  PTR         mydomain.local.
    server           IN  A   192.168.1.10
    client          IN  A   192.168.1.5
    10	IN  PTR         server.mydomain.local.
    5	IN  PTR         client.mydomain.local.

    ```

10. bearbeite die Berechtigungen der Dateien:
    ```bash
    sudo chgrp named -R /var/named
    sudo chown -v root:named /etc/named.conf
    sudo restorecon -rv /var/named
    sudo restorecon /etc/named.conf
    ```
11. Konfiguriere die firewall
    ```bash
    sudo firewall-cmd --add-service=dns --perm
    sudo firewall-cmd --reload
    ```
## DNS Server Testen
Der dns Server kann mit nslookup getested werden <br>
Domain zu IP: <br>
```bash
nslookup server.mydomain.local
```
IP zu Domain
```bash
nslookup 192.168.1.5
```

## DNS Cache mit Wireshark testen
wen man eine domain auflöst wird die IP adresse auf dem DNS Server im cache gespeichert. Dass kann man mithilfe von Wireshark beobachten.
1. Um wireshark zu benutzen muss man auf dem Server einen Desktop, Display Manager und Wireshark installieren.
    ```
    sudo dnf install gdm wireshark open-vm-tools open-vm-tools-desktop gnome-terminal -y
    ```
2. Starte den display manager damit du dich in den desktop einlogen kannst.
    ```
    systemctl start gdm
    ```
3. Melde dich mit dem Benutzername "root" und deinem Passwort an.
4. Öffne Wireshark
