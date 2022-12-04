# DHCP Server auf Fedora (VM) installieren

## Vorbereitung
Lade die Folgenden Dateien / Programme herunter und installier sie.
- [Fedora Linux Desktop](https://getfedora.org/en/workstation/) (ISO Datei)
- [Fedora Linux Server](https://getfedora.org/en/server/) (ISO Datei)
- [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) (Programm + Extension Pack)
---
## VirtualBox Netzwerk Configuration
Um einen DHCP Server zu installieren brauchen wir zuerst ein Netzwerk. Es ist möglich in VirtualBox ein "NAT Network" zu erstellen, dass fast alle eigenschaften von einem echten Netzwerk hat.
1. öffne VirtualBox und gehe auf "Tools" <br>
    ![](/Dateien/Bilder/DHCP_Setup/1.png)
2. wähle "NAT Networks" und erstelle mit "Create" ein neues Netzwerk Ich empfehle bei dem "IPv4 Prefix" "192.168.1.0/24" einzugeben weil alle weiteren Konfigurationen in dieser Anleitung von dieser IP Range ausgehen <br>
    ![](/Dateien/Bilder/DHCP_Setup/2.png)
    stelle sicher das "DHCP" ausgeschaltet ist.

---

## VM für Server Configurieren

In den folgenden Schritten wird eine neue VM kreiert und zu unserem NAT Netzwerk hinzugefügt.
1. Erstelle eine neue VM gib ihr irgendeinen Namen und Wähle bei "ISO Images" das Server ISO dass du hoffentlich vorher heruntergeladen hast. <br>
![](/Dateien/Bilder/DHCP_Setup/3.png)

2. Klicke "Next" bis sich das Fenster schliest.
3. öffne die Einstellungen der VM und wähle den "Netzwerk" Teil<br>
    ändere "Attached to" zu "NAT Network" und wähle unter "Name" das NAT das du vorher gemacht hast.
    ![](/Dateien/Bilder/DHCP_Setup/4.png)
4. Bestätige die Änderungen indem su auf "OK" drückst.

---

## OS auf Server installieren
1. Starte die VM, die du gerade gemacht hast und warte bis es bei dir ungefähr wie im Bild unten aussieht. <br>
![](/Dateien/Bilder/DHCP_Setup/5.png)
2. folge diesem video um Fedora zu installieren
[Video](https://user-images.githubusercontent.com/110155948/204562386-60c472fc-de38-4ef9-8a6f-019b3e49494b.mp4)
---

## DHCP Server configurieren
0. Entferne das installations iso von der VM.
1. Starte die VM und logge dich mit dem Username `root` und deinem Passwort ein.
2. Installiere den DHCP Server
    ```bash
    dnf install dhcp-server -y
    ```
    Dieser Befehl installiert den dhcp Server (das Programm) auf deinem PC

3. Füge den DHCP service zu der firewall hinzu
    ```bash
    firewall-cmd --add-service=dhcp --permanent <br>
    ```
    
    Dieser Befehl fügt dhcp zu der firewall hinzu, sonst würden dhcp-requests einfach blockiert werden. Der --permanent flag sorgt dafür das die Änderung auch nach einem neustart noch da ist.

    ```bash
    firewall-cmd  --reload
    ```

    Dieser Befehl ladet einfach die firewall regeln neu. 

4. Gehe in die Directory in dem der config file ist.
    ```bash
    cd /etc/dhcp/
    ```

5. Entferne die alte config Datei
    ```bash
    rm dhcpd.conf
    ```

6. Jetzt hasst du 2 Möglichkeiten um die neue config Datei zu erstellen:
    1. Lade meinen Config mit dem folgenden commando herunter:
        ```bash
        wget https://raw.githubusercontent.com/Sebi364/Anleitungen/main/clone/dhcpd.conf
        ```
    2. Erstelle die config Datei manuell von Hand. <br>
        Installiere den nano text Editor
        ```bash
        dnf install nano -y
        ```

        öffne die config Datei und bearbeite sie    
        ```bash
        nano dhcpd.conf
        ```

        Als Vorlage kannst du [meinen config](https://github.com/Sebi364/Anleitungen/blob/main/clone/dhcpd.conf) benutzen, oder selber recherchieren.

7. Nachdem der dhcp Server konfiguriert ist, musst du ihn nur noch starten:
    ```bash
    systemctl enable --now dhcpd
    ```

8. überprüfe das alles funktioniert:
    ```bash
    systemctl status dhcpd
    ```

    Wenn du keine roten Nachrichten siehst funktioniert dein DHCP Server.

--- 
## DHCP Server testen
1. Erstelle eine neue VM mit dem zweiten iso dass du heruntergeladen hast, vergss nicht sie ins gleiche "NAT Network" wo der DHCP Server ist rein zu tun. 
2. Starte die VM
3. Führe **AUF DEM DHCP SERVER** diesen Befehl aus:
    ```bash
    cat /var/lib/dhcp/dhcpd.leases
    ```

    dieser zeigt dir die aktiven leases.

---



