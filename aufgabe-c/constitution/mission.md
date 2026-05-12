# mission.md

# Assassin Party Game – Mission

## 1. Projektname

**Assassin Party Game**

## 2. Kurzbeschreibung

Assassin Party Game ist eine webbasierte Party-Spiel-Anwendung, bei der jede teilnehmende Person ein geheimes Ziel und ein geheimes Zielwort erhält. Ziel ist es, die eigene Zielperson im natürlichen Gespräch dazu zu bringen, dieses Wort auszusprechen.

Wird eine Person eliminiert, übernimmt der erfolgreiche Assassin deren Ziel und Zielwort. Das Spiel läuft weiter, bis nur noch eine aktive Person übrig ist.

## 3. Mission

Assassin Party Game soll echte soziale Interaktion auf Hauspartys wiederbeleben. Das Spiel verwandelt Smalltalk, Unsicherheit und zufällige Gespräche in ein leicht verständliches, kompetitives und humorvolles Social Game.

Es richtet sich an Studierende und junge Erwachsene, die auf Partys niedrigschwellig miteinander ins Gespräch kommen wollen.

## 4. Zielgruppe

Primäre Zielgruppe sind Studierende auf Hauspartys.

Insbesondere adressiert das Spiel Personen, die sich im sozialen Kontext unsicher fühlen, schwer Anschluss finden oder einen spielerischen Anlass für Gespräche benötigen.

## 5. Zentrales Problem

Auf vielen Partys entstehen soziale Barrieren: Gruppen bleiben unter sich, neue Personen finden schwer Einstieg in Gespräche, und Interaktion wirkt oft zufällig oder gehemmt.

Assassin Party Game gibt allen Teilnehmenden ein geheimes soziales Ziel und erzeugt dadurch Gesprächsanlässe, Dynamik und Spannung.

## 6. Produktprinzipien

- Keine Registrierung
- Schneller Einstieg über Lobby-Code
- Mobile Nutzung für Spieler
- GameMaster-Steuerung über geschützten geheimen Pfad
- Echtzeit-Synchronisation über WebSockets
- Genau eine aktive Party-Session pro Server
- Persistenter Game-State über JSON-Dateien
- Öffentlich erreichbar über Internet
- Betrieb hinter Nginx Reverse Proxy mit SSL

## 7. MVP-Ziel

Die erste Version muss es ermöglichen, eine reale Party-Session vollständig durchzuführen:

1. GameMaster erstellt eine Party-Session.
2. Spieler treten über ihre Geräte bei.
3. Server erzeugt Lobby-Codes.
4. GameMaster verbindet Lobby-Codes mit vorbereiteten Spieleridentitäten.
5. System generiert einen zirkulären Assignment-Graph.
6. Jeder Spieler sieht nur eigenes Ziel und eigenes Zielwort.
7. Kills werden über Badge-/Code-Eingabe registriert.
8. Eliminierte Spieler werden aus dem Graph entfernt.
9. Killer übernimmt Ziel und Wort der eliminierten Person.
10. Spiel endet automatisch, wenn nur noch eine Person übrig ist.

## 8. Nicht-Ziele der ersten Version

- Keine Benutzerkonten
- Keine langfristigen Nutzerprofile
- Keine native Mobile-App
- Keine Datenbank
- Keine parallelen Partys
- Kein komplexes Matchmaking
- Kein öffentlicher GameMaster-Login

## 9. Erfolgskriterium

Das MVP ist erfolgreich, wenn eine Gruppe Studierender das Spiel auf einer Hausparty ohne technische Erklärung starten, spielen und beenden kann — mit stabiler Echtzeit-Synchronisation und ohne dass geheime Missionsinformationen versehentlich sichtbar werden.
