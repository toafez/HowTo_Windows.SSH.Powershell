[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Ftoafez%2FHowTo_Windows.SSH.Powershell&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

# HowTo: SSH-Key mit der Windows PowerShell
Secure Shell, abgekürzt SSH, ist ein Netzwerkprotokoll zum Aufbau verschlüsselter Verbindungen zwischen Geräten im lokalen Netzwerk oder über das Internet. Die Anmeldung erfolgt in der Regel über die Eingabe eines Benutzernamens und des zugehörigen Passworts. Neben dem Verbindungsaufbau direkt über das Terminal werden auch grafische Benutzeroberflächen wie PuTTY verwendet.
Durch die Verwendung einer RSA-Schlüssel-Authentifizierung entfällt beim SSH-Verbindungsaufbau zu einem entfernten Server die Eingabe von Benutzername und Passwort. Dies ermöglicht einen automatisierten Verbindungsaufbau ohne die Anwesenheit eines Benutzers. Die Authentifizierung erfolgt hierbei über einen zuvor generierten privaten Schlüssel ohne Passwort und einen daraus resultierenden öffentlichen Schlüssel.

## PowerShell öffnen
1. Starte die **PowerShell** aus der Liste der Windows-Programme.
2. Du solltest dich direkt im Home-Verzeichnis deines aktuell angemeldeten Windows-Benutzers befinden.

	`PS C:\Users\[BENUTZERNAME]>`

3. Überprüfe sicherheitshalber noch einmal die Version der PowerShell. Diese sollte mindestens Version 5.1 oder höher sein. Dazu gibst du in der Eingabeaufforderung folgenden Befehl ein

	`$PSVersionTable.PSVersion`

    **Anmerkung:** Seit Windows 10 1809 ist der OpenSSH-Client bereits standardmäßig installiert. In Verbindung mit der Windows PowerShell 5.1 oder höher lassen sich SSH Schlüssel einfach erstellen.

## Schlüsselpaar erstellen
1. Du kannst nun beginnen, ein neues Schlüsselpaar zu erzeugen, das aus einem öffentlichen und einem privaten Schlüssel besteht. Dies geschieht mit dem Befehl **ssh-keygen**. Durch die einfache Eingabe des Befehls **ssh-keygen** können je nach Konfiguration verschiedene Verschlüsselungsalgorithmen zur Anwendung kommen. Daher sollte immer angegeben werden, welcher Algorithmus verwendet werden soll. Im Folgenden wird der **RSA**-Algorithmus mit einer Verschlüsselung von **4096** Bit verwendet.

	`ssh-keygen -t rsa -b 4096`

	(-t steht für type, also den Verschlüsselungstyp, hier rsa für RSA Protokoll 2)

	(-b steht für Bit, also die Bitlänge des Verschlüsselungsschlüssels, hier 4096 Bit)

2. Unmittelbar nach dem Ausführen des Befehls wirst du gefragt, unter welchem Dateinamen der Schlüssel gespeichert werden soll. Wenn du den vorgeschlagenen Pfad und Dateinamen beibehalten möchtest, drücke einfach die Eingabetaste. Andernfalls kannst du unter Verwendung des vollständigen Pfads einen alternativen Dateinamen ohne Dateiendung eingeben. In diesem Beispiel wird der vorgeschlagene Dateiname verwendet.

	```
	Generating public/private rsa key pair.
	Enter file in which to save the key (C:\Users\[BENUTZERNAME]/.ssh/id_rsa):
	Created directory 'C:\\Users\\[BENUTZERNAME]/.ssh'.
	```
 
3. Anschließend wirst du aufgefordert, eine Passphrase, also ein Passwort, einzugeben. Die Verwendung einer Passphrase wird einerseits dringend empfohlen, kann aber auch kontraproduktiv sein, wenn man später unbeaufsichtigte und damit automatisierte SSH-Verbindungen aufbauen möchte, um z.B. regelmäßige Backups durchzuführen. In diesem Fall ist von der Eingabe einer Passphrase dringend abzuraten. Es ist also jedem selbst überlassen, ob er eine Passphrase verwenden möchte oder nicht. In diesem Beispiel wird keine Passphrase verwendet, sondern die beiden nachfolgenden Abfragen einfach durch Drücken der Eingabetaste übersprungen.

	```
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	```

	In der Folge erscheint eine ähnliche Ausgabe wie diese...

	```
	Your identification has been saved in C:\Users\[BENUTZERNAME]/.ssh/id_rsa
	Your public key has been saved in C:\Users\[BENUTZERNAME]/.ssh/id_rsa.pub
	The key fingerprint is:
	SHA256:ScteBU5B3/m7/cod5OqlH8JSDpiNC8oGGYx0sEhoHFM thomas@Windows-V17-IIL
	The key's randomart image is:
	+---[RSA 4096]----+
	|o*+E     .=.     |
	|=o*      o o . . |
	|oo o    . . o o  |
	|    o  o o=.   . |
	|   o   .S+.o . ..|
	|    o ..... = o .|
	|     +  .. . + * |
	|    .       ..=.=|
	|            .+++=|
	+----[SHA256]-----+
	PS C:\Users\[BENUTZERNAME]>
	```

4. Nach der Ausführung wird ein neues Verzeichnis mit dem Namen .ssh in deinem Homeverzeichnis angelegt, falls es noch nicht existiert. In diesem Verzeichnis befindet sich eine Datei mit dem Namen id_rsa, die den privaten Schlüssel enthält, und eine Datei mit dem Namen id_rsa.pub, die den öffentlichen Schlüssel enthält. Wechsle in das .ssh Verzeichnis durch Eingabe von...

	`cd .ssh`

	...um dir den Inhalt durch die Eingabe von...

	`dir`

	...anzeigen zu lassen.

## Den öffentlichen Schlüssel auf den Remote-Server kopieren
1. Um in Zukunft eine passwortlose SSH-Verbindung zu deinem Remoter-Server aufbauen zu können, ist es notwendig, den öffentlichen Schlüssel in der Datei authorized_keys deines Remote-Servers zu hinterlegen. Um dich mit deinem Remote Server verbinden zu können, benötigst du neben der IP-Adresse [IP-ADRESSE] und dem [PORT] auch den Benutzernamen [BENUTZERNAME] sowie das zugehörige **Passwort**. Ersetze daher im folgenden Befehl die Platzhalter für [PORT], [BENUTZERNAME] und [IP-ADRESSE] durch deine eigenen Daten, ohne die eckigen Klammern zu verwenden. Führe anschließend folgenden Befehl aus

    `type $env:USERPROFILE\.ssh\id_rsa.pub | ssh -p [PORT] [BENUTZERNAME]@[IP-ADRESSE] "cat >> .ssh/authorized_keys"`

2. Nachdem du den folgenden Befehl mit der Eingabetaste bestätigt hast, solltest du eine Meldung sehen, die dich am Ende des Textes auffordert, den Verbindungsaufbau mit **yes** zu bestätigen. `Are you sure you want to continue connecting (yes/no/[fingerprint])?` Diese Meldung erscheint aber nur, wenn du dich zum ersten Mal per SSH mit dem Remote-Server verbinden willst.

    ```
    The authenticity of host '172.16.1.12 (172.16.1.12)' can't be established.
    ED25519 key fingerprint is SHA256:DosguIv2tIYP+9n3BIrSM2df1841CdXbIc4pAZ01ehA.
    This key is not known by any other names.
    Are you sure you want to continue connecting (yes/no/[fingerprint])?
    ```

    Nun wirst du aufgefordert dein Passwort für die Anmeldung an deinem Remote-Server einzugeben.

    `[BENUTZERNAME]@[IP-ADRESSE]'s password:`

3. Nach erfolgreicher Anmeldung wird der öffentliche Schlüssel in der Datei authorized_keys deines Remote-Servers gespeichert und die Verbindung wieder getrennt. Du befindest dich wieder in der lokalen Eingabeaufforderung der PowerShell.

    `PS C:\Users\[BENUTZERNAME]\.ssh>`

## Prüfen, ob die SSH-Schlüssel-Authentifizierung funktioniert

Baue eine SSH-Verbindung zu deinem Remote-Server auf indem du folgendes eingibst

  `ssh -p [PORT] [BENUTZERNAME]@[IP-ADRESSE]`

wobei du auch hier die Platzhalter für [BENUTZERNAME] und [IP-ADRESSE] durch deine eigenen Daten ersetzt, ohne die eckigen Klammern zu verwenden. Dieses mal sollte die Verbindung ohne Handshake und ohne die Eingabe eines Passwortes erfolgen.
