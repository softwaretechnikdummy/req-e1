# Aufgabe B

Anstelle einer kommerziellen Lösung wollte ich gerne eine eigene Lösung bauen und habe dafür mittels ChatGPT eine simple HTML-Anwendung kreiren lassen, dass es erlauben sollte, Requirements als CSV zu importieren.

## chatgpt prompt
```
Can you help me create a single page HTML app that can do requirement modeling for software projects?
Per requirement I need the following fields please:
<list>  ID  Date  Author  Short description  Long description  Status  Reference to the use case  Reference to other documents  Who approves the requirement?  What is the acceptance criterion?  Priority  User Scope  Due date  Conflicts  Dependencies Comments Category</list>

It should be possible to create a new requirement entry using a modal popup. The requirement ID should be generated automatically from Category and incrementing number. Categories should be reusable, so existing ones should automatically appear once you start typing.

its very important that you are able to create a new revision for an existing requirement and should be able to view the revision history as well.

All requirements should be exportable and importable in csv format.

A canban-style view should exist to get an overview on tasks
```

Die daraus resultierende Anwendung sieht vielversprechend aus:
<img width="996" height="560" alt="grafik" src="https://github.com/user-attachments/assets/1312c8e6-3434-4759-99dc-67f4a1f4ba38" />

Das Erstellen von Requirements funktioniert wie gewünscht über ein Modal:
<img width="983" height="852" alt="grafik" src="https://github.com/user-attachments/assets/5e7d24b8-ddb6-4c6c-879b-c1ca044f1eaa" />

Revisionen lassen sich über die Listenansicht erstellen. Hier eröffnet sich das Erstellungsmodal mit vorausgefüllten Daten.
Ein klarer Vorteil besteht gegenüber meiner Airtable-Implementation, da hier die Übersicht nicht durch unzählige Rows zugemüllt wird.
<img width="1202" height="112" alt="grafik" src="https://github.com/user-attachments/assets/d82580ba-bf2a-4e4d-a8f3-930987b78efe" />

Natürlich wollte ich die Daten nicht einfach manuell anlegen, also habe ich meinen Airtable als CSV heruntergeladen, um ihn in die Anwendung hochzuladen.
Zwar wurde mir angezeigt, dass die 30 Datensätze erfolgreich geladen wurden, doch tauchten sie nicht auf.
Also fragte ich chatgpt, ob es mir die CSV-Datei nicht freundlicherweise in das richtige Format für die soeben erstellte Anwendung konvertieren könne:

## chatgpt prompt
```
Can you modify this csv so that it is compatible with the import csv functionality of this newly created app?

<...csv content from airtable...>
```

Das klappte hervorragend und der anschließende Import auch:
<img width="1734" height="882" alt="grafik" src="https://github.com/user-attachments/assets/7eeb8aac-4643-4d49-bd44-c9e1e6315e4e" />

## referenzen

Die [csv-datei von chatgpt](requirements-converted.csv) und die generierte [Anwendung](./requirements-manager.html) habe ich mit hochgeladen
