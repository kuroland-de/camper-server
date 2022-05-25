# camper-server

Dies ist eine Sammlung von Software, um aus dem Raspberry Pi, einen Server zur Überwachung von Campern und Booten zu  machen.

# Voraussetzungen:

Hardware:
- Raspberry Pi inkl. Netzteil (Empfehlung ist der Raspberry Pi 4 mit 4 Gb Ram und das Netzteil vom Hersteller)
- SD-Karte oder SSD (Empfehlung SSD)
- PC und Netzwerk mit Internet für die Installation
- Optional: Gehäuse (Empfehlung Argon One M.2)
- Optional: Victron GX Controller
- Optional: Zigbee USB Stick für Zigbee2MQTT

Software:
- ssh-Client z.B. PuTTY
- Für Kurzanleitung: RasbianOS 32bit, Docker und Docker-Compose-Plugin

# Kurzanleitung:

> git clone https://github.com/kuroland-de/camper-server.git

> cd camper-server/

> docker compose up -d



# Installtion vom Raspberry OS

Als erstes wird der Raspberry Pi Imager benötigt.

		Download und Installation:
> https://www.raspberrypi.com/software/

Nach der Inastallation den Imager öffnen und ein Betriebssystem aussuchen.
Mit Bildschirm die RaspiOS 32bit, ohne Monitor die RaspiOS 32 bit lite Version. Die richtige SD-Karte oder SSD auswahlen. In den Einstellungen SSH aktivieren und ein Passwort vergeben. Ggf. die WiFi-Konfiguration anpassen und dann mit "Schreiben" die Daten  auf die SD-Karten/SSD schreiben.

Nach der Installation den Raspberry Pi booten und via SSH verbinden. Zum Beispiel mit dem Programm PuTTY.
Mit dem nachfolgenden Befehl wird das System auf den neusten Stand gebracht.

	Upgrade auf aktuelle Version:
> sudo apt update && sudo apt upgrade -y

# Argon One Gehäuse
	Aktualisieren und curl installieren:
> sudo apt update && sudo apt install curl -y

	sript herunterladen und installieren, neustarten:
> curl https://download.argon40.com/argon1.sh | bash

> sudo reboot

	Konfiguration:
> argonone-config

	Deinstallation:
> argonone-uninstall


## Argon One Funktionen


ARGON ONE (V2) PI 4 STATE | ACTION | FUNCTION
:------------------: | :----: | :------:
OFF | Short Press | Turn ON
ON | Long Press (>= 3 s) | Soft Shutdown and Power Cut
ON | Short Press (< 3 s) | Nothing
ON | Double Tap | Reboot
ON | Long Press (>= 5 s) | Forced Shutdown


# Docker und Docker Compose installieren

Docker nach der aktuellen Anleitungen installieren.
Hier sind die Befehle:
 
	 Alte Versionen deinstallieren:
 > sudo apt-get remove docker docker-engine docker.io containerd runc 

	Pakteliste aktualisieren und nötige Pakete installieren:
> sudo apt-get update 

> sudo apt-get install ca-certificates curl gnupg lsb-release

	Docker GPG Key hinzufügen:
> curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

	Repository hinzufügen:
> echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

	aktualisieren und Docker und Docker Compose installieren:
> sudo apt-get update

> sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

	Pi zur Gruppe Docker hinzufügen und neustarten:
> sudo usermod -aG docker pi

> sudo reboot


	Glückwunsch! Docker und Docker Compose sind installiert. Achtung bei der aktuellen Version von docker compose wird der Bindestrich im Befehl weggelassen
> ~~docker-compose up -d~~

>docker compose up -d


# Server einrichten
	Ordner erstellen und in diesen wechseln:
> mkdir NAMEdesORDNERS

>cd NAMEdesORDNERS

	Herunterladen oder erstellen von docker-compose.yml
>nano docker-compose.yml

oder:
>gitclone https://meine.neue.gitaddress.com


# Pihole
	Pihole Installations Script starten
> curl -sSL https://install.pi-hole.net | bash


# Tailscale
Es wird ein Konto bei Tailscale benötigt. Dies bitte anlegen.

	notwendige Pakete installieren:
> sudo apt-get install apt-transport-https

	Paketquellen und Schlüssel hinzufügen:
> curl -fsSL https://pkgs.tailscale.com/stable/raspbian/bullseye.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg > /dev/null

> curl -fsSL https://pkgs.tailscale.com/stable/raspbian/bullseye.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list

	aktuallsieren und installieren:
> sudo apt-get update && sudo apt-get install tailscale -y

Als nächstes wird ein Schlüssel benötig um Tailscale mit dem Konto zuverknüpfen. Gehen Sie dazu auf die Homepage von Tailscale in Ihr Konto. Im Bereich Einstellungen (Settings) und dann unter Keys können Sie einen neuen Schlüssel erzeugen. Es wird der Auth Key in den Standart  Einstellungen benötig. Hier ein Beispiel:

tskey-abcdef1432341818


	tailscale starten, tskey vorher ersetzen:
>sudo tailscale up --authkey tskey-abcdef1432341818

In der Übersicht vom Tailscale Konto sollte der RaspberryPi nun auftauchen. Damit Sie nicht regelmäßig einen neuen Schlüssel eingeben müssen, klicken Sie in der Übersicht hinter dem RaspberryPi auf die Menüpunkte und dann auf "Disable key expiry"

Um aus der Ferne nicht nur auf den Raspberry Pi sondern auch auf andere Netzwerkgeräte zugreifen zukönnen muss das Netzwerk noch freigegeben werden.


	ipv4 und ipv6 Forwarding:
>echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf

>echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.conf

>sudo sysctl -p /etc/sysctl.conf

	Netzwerke freigeben:
> sudo tailscale up --advertise-routes=10.0.0.0/24,10.0.1.0/24

Anschließen wieder im Konto auf das Menü von dem Raspberry Pi klicken und unter "Edit route settings" das Netzwerk noch freigeben.

