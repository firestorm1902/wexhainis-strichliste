# Wexhainis Getränke-Strichliste

Web-App für den Verein:
- Getränke werden pro Ausfahrt-Tag eingeladen.
- Mitglieder buchen sie per Barcode mit ihrem eigenen Konto.
- Am Ende gleicht der Admin mit dem gezählten Rest ab.

Alle Handys teilen sich eine Firebase-Datenbank. Scannen funktioniert auch ohne Netz; die Buchungen werden gesendet, sobald wieder Verbindung besteht.

## Dateien im GitHub-Repository

| Datei | Zweck |
|---|---|
| `index.html` | die App |
| `sw.js` | hält die App offline startbar |
| `index_min.js` | Barcode-Erkennung (ZXing) |
| `manifest.webmanifest` | macht die Seite als App installierbar (Name, Farben, Symbole) |
| `icons/` | App-Symbole und Schnellzugriff-Symbole (Ordner mit allen Dateien hochladen) |
| `firestore.rules` | Sicherheitsregeln – werden in Firebase eingefügt, nicht auf GitHub gebraucht |

## Als App installieren (Android und iPhone)

Die Strichliste ist eine installierbare Web-App (PWA): Nach dem Installieren hat sie ein Wespen-Symbol auf dem Startbildschirm, startet im Vollbild ohne Adressleiste und funktioniert offline. Ein App-Store ist nicht nötig.

- **Android (Chrome):** In der App erscheint „Als App installieren“ → **Installieren**. Oder Menü **⋮** → „App installieren“. Samsung Internet: Menü ☰ → „Seite hinzufügen zu“ → „Startbildschirm“.
- **iPhone/iPad:** In **Safari** öffnen → **Teilen** → **„Zum Home-Bildschirm“** → „Hinzufügen“. Die App zeigt dafür eine Anleitung („So geht’s“, auch in der Hilfe unter **?**).
- Wurde der Link aus WhatsApp geöffnet, zuerst „Im Browser öffnen“ wählen – aus dem WhatsApp-Browser heraus lässt sich nicht installieren.
- **iPhone:** Die installierte App hat einen eigenen Speicher. Man muss sich dort **einmal neu anmelden**. Am besten installieren, bevor man offline bucht.
- **Updates:** Die installierte App prüft beim Öffnen und alle 30 Minuten auf eine neue Version. Ist eine da, erscheint unten „Neue Version da – Neu laden“.
- **Schnellzugriffe:** Lange auf das Wespen-Symbol drücken (Android) zeigt **Getränk buchen** (startet die Kamera), **Mein Konto**, **Korrektur beantragen**, **Kontakt & Feedback** und **Admin-Bereich** (nur für Admins/Vorstand; andere bleiben auf der Startseite bzw. werden weitergeleitet). Jeden Schnellzugriff kann man auf den Startbildschirm ziehen – wie ein kleiner Widget-Knopf. Echte Startbildschirm-Widgets können Web-Apps auf Android und iPhone nicht anbieten.
- **Zahl am App-Symbol:** Mitglieder sehen die Zahl neuer Antworten auf ihre Nachrichten und neu entschiedener Anträge; Getränke-Admins zusätzlich offene Korrekturanträge, der Super-Admin ungelesene Nachrichten im Posteingang. Auf dem iPhone (ab iOS 16.4, als App installiert) muss man dafür einmal „Erlauben“ tippen – die App fragt danach. Auf Android hängt die Anzeige vom Handy ab (manche zeigen nur einen Punkt).
- **Benachrichtigungen und Punkt am Symbol (Android):** Läuft die App im Hintergrund, kommt eine Handy-Benachrichtigung – für Admins bei neuen Korrekturanträgen, für den Super-Admin bei neuen Nachrichten (auch Fehlermeldungen und Feedback), für Mitglieder, wenn über ihren Antrag entschieden oder ihre Nachricht beantwortet wurde (dazu „Getränke gehen zu Ende“ und „Zu viel gescannt“ für Admins). Dadurch erscheint auf Android der Punkt am App-Symbol; Antippen öffnet die passende Stelle, beim Öffnen der App verschwinden sie. Mitglieder werden nach dem ersten Antrag bzw. der ersten Nachricht einmal gefragt, ob sie das möchten; Admins erlauben es unter Admin → Daten. Ist die App ganz geschlossen, kommt nichts (dafür bräuchte es einen eigenen Server).

## Einrichtung (einmalig, ca. 15 Minuten)

Die Firebase-Konsole hat links zwei Bereiche: **Projektverknüpfungen** (zuletzt benutzte Dienste) und **Produktkategorien** (alle Dienste).

### 1. Firebase-Projekt anlegen
1. <https://console.firebase.google.com> öffnen, mit dem Google-Konto des Vereins anmelden.
2. **Projekt erstellen**, Namen vergeben. Google Analytics kann aus bleiben.
3. Der kostenlose **Spark**-Tarif reicht. Keine Kreditkarte nötig.

### 2. Anmeldung einschalten
1. Links **Authentication** öffnen. Steht es nicht unter „Projektverknüpfungen“, oben über **Nach Produkten suchen** „Authentication“ eingeben. Falls ein Knopf **Jetzt starten** erscheint, ihn anklicken.
2. Reiter **Anmeldemethode** (bei schmalem Fenster ggf. die Reiterleiste nach rechts scrollen):
   - **E-Mail/Passwort** aktivieren. Die Option „E-Mail-Link (Anmeldung ohne Passwort)“ bleibt aus.
   - Falls **Anonym** aktiv ist: deaktivieren. Wird nicht mehr gebraucht.
3. Reiter **Einstellungen → Autorisierte Domains → Domain hinzufügen**: `DEIN-NAME.github.io` eintragen.

### 3. Datenbank anlegen
1. Links **Firestore** öffnen. Steht es nicht unter „Projektverknüpfungen“, über **Produktkategorien → Datenbanken und Speicher** oder die Suche oben.
2. **Datenbank erstellen**, Standort in Europa wählen (z. B. `europe-west3` Frankfurt). Der Standort lässt sich später nicht ändern.
3. **Im Produktionsmodus starten**.
4. Reiter **Regeln**: den kompletten Inhalt von `firestore.rules` einfügen und **Veröffentlichen**. Die Admin-ID trägst du in Schritt 6 nach.

### 4. Zugangsdaten in die App eintragen

Für das Projekt `wexhainis-getraenkestrichliste` ist das bereits erledigt; die Zugangsdaten stehen in `index.html`. Für ein anderes Projekt:

1. Links **Einstellungen** (Zahnrad) → **Allgemein**.
2. Ganz nach unten zu **Meine Apps** scrollen, auf das Web-Symbol **`</>`** klicken.
3. App-Namen vergeben (z. B. „Strichliste“). *Firebase Hosting* **nicht** anhaken, dann **App registrieren**.
4. Den angezeigten Block `const firebaseConfig = { … }` kopieren. Später findest du ihn wieder unter Einstellungen → Allgemein → Meine Apps → Option „Config“.
5. In `index.html` nach `KONFIGURATION` suchen (ca. Zeile 200). Den Platzhalter-Block `const firebaseConfig = { … };` komplett durch den kopierten ersetzen.

Die Werte in `firebaseConfig` sind keine Geheimnisse und dürfen im öffentlichen Repository stehen. Geschützt werden die Daten durch die Sicherheitsregeln.

### 5. Auf GitHub Pages hochladen
`index.html`, `sw.js` und `index_min.js` ins Repository legen (alle im selben Ordner). Nach ein bis zwei Minuten ist die Seite unter `https://DEIN-NAME.github.io/REPO/` erreichbar.

### 6. Super-Admin festlegen
1. Die Seite öffnen, **Registrieren** wählen und dein eigenes Konto anlegen (Name, E-Mail, Passwort).
2. **Fünfmal schnell auf den Titel** „Wexhainis Getränke-Strichliste“ oben tippen, das öffnet den Admin-Bereich. Es erscheint „Keine Admin-Rechte“. Darunter „Für die Einrichtung: Nutzer-ID“ aufklappen und die ID kopieren.
   - Alternativ steht die ID in der Firebase-Konsole unter Authentication → Nutzer → Spalte „Nutzer-UID“.
3. In der Firebase-Konsole unter **Firestore → Regeln** bei `superUids()` eintragen und **Veröffentlichen**:
   ```
   return ['aB3dE5fG7hJ9kL1mN3pQ5rS7tU9v'];
   ```
   Mehrere Super-Admins werden mit Komma getrennt: `['ID1', 'ID2']`. Ein zweiter Super-Admin als Vertretung ist sinnvoll.
4. Seite neu laden. Der Admin-Bereich ist jetzt offen.
5. Weitere **Admins** ernennst du direkt in der App unter **Admin → Personen**: Person antippen und bei „Rolle“ „Admin“ wählen. Die Regeln musst du dafür nicht mehr anfassen.

### 7. Testen
1. Im Admin einen **neuen Tag** anlegen.
2. **Einladen per Scan** wählen, eine Flasche scannen, das Getränk anlegen und Kästen antippen.
3. Auf einem zweiten Handy ein Konto registrieren. Es wartet zuerst auf Freischaltung: im Admin oben unter **Neue Anmeldungen** auf „Freischalten“ tippen. Danach scannen. Die Buchung erscheint beim Admin unter „Abgleich“ und „Nach Person“.

## Für die Mitglieder

- Seite öffnen, **Registrieren** wählen und Vor- und Nachname, E-Mail und Passwort eingeben. Dafür braucht man einmal Internet.
- Danach steht „Fast geschafft – dein Konto wartet auf Freischaltung“. Sobald ein Admin freischaltet, geht die App von selbst weiter.
- Am besten zum Home-Bildschirm hinzufügen:
  - iPhone: Safari → Teilen → „Zum Home-Bildschirm“
  - Android: Chrome → Menü → „Zum Startbildschirm hinzufügen“
- Man bleibt angemeldet, auch offline. Neues Handy oder Browserdaten gelöscht? Einfach mit derselben E-Mail **anmelden**, Name und Buchungen sind wieder da.
- Nach jeder Buchung steht die Meldung mit „Rückgängig“ eine Weile da (Countdown, Standard **10 Sekunden**), dann verschwindet sie. Ob es „Rückgängig“ gibt und wie lange, stellt der Super-Admin ein (siehe unten). Ist es ausgeschaltet, steht dort „Vertippt? Korrektur beantragen“.

### Rückgängig einstellen und nachverfolgen (Super-Admin)
Admin → **Einstellungen** → „Rückgängig nach dem Buchen“: an/aus und die Zeit (3–120 Sekunden). Der Server prüft das mit: Mitglieder können eine eigene Buchung nur löschen, solange Rückgängig an ist und höchstens die eingestellte Zeit plus 2 Minuten (für langsames Netz) vergangen ist. Dafür müssen die neuen `firestore.rules` veröffentlicht sein.

Jedes Rückgängig wird gemerkt: wer, wann, welches Getränk, und wie viele Sekunden nach dem Buchen. Beim **Abgleich** eines Tages steht die Liste „Rückgängig gemacht“. Fehlen bei einem Getränk Flaschen und wurde es an dem Tag zurückgenommen, ist die Zeile rot markiert und oben steht „Prüfen: Pils: fehlt 1, aber 1× rückgängig gemacht (Anna 15:20)“. Beim **Tag abschließen** kommt dann eine Rückfrage mit diesem Hinweis. Auch „Zu wenig gebucht“ zeigt die Rückgängig-Fälle der betroffenen Tage. Das Protokoll kann nur der Super-Admin löschen; es steckt auch im Backup.
- Unten rechts erscheint beim Herunterscrollen ein gelber Knopf **nach oben**.
- **Passwort ändern:** angemeldet auf „Passwort ändern“ neben „Abmelden“ tippen.
- **Passwort vergessen?** E-Mail eintragen und auf „Passwort vergessen?“ tippen. Es kommt eine Mail von Firebase zum Zurücksetzen.
- Oben neben „Strichliste“ steht der Netzstatus. „offline · 3 warten“ heißt: 3 Buchungen sind gespeichert und werden gesendet, sobald Netz da ist. In dieser Zeit nicht abmelden; die App warnt davor.

- **Ich fahre heute:** In der Scan-Ansicht auf das Auto tippen. Dann fragt die App bei jedem alkoholischen Getränk nach („Du fährst heute – trotzdem buchen?“). Admins sehen in der Tagesübersicht, wer heute fährt. Gilt nur für den einen Tag.
- **Ich fahre heute** zeigt beim Einschalten kurz ein Lenkrad mit „Gute Fahrt!“.
- **Easter Eggs für sich einstellen:** Wer Easter Eggs sehen darf, hat neben „Ich fahre heute“ den Knopf „Easter Eggs: an“. Dort wählt man *An*, *An, ohne Ton* (nur dieses Handy) oder *Aus* (für das Konto auf allen Handys: keine Popups, kein Verlauf, keine Sammelalbum-Meldungen; gezählt wird trotzdem).
- **Nachtmodus:** Oben rechts zwischen *Auto*, *Hell* und *Nacht* umschalten. *Auto* wird dunkel, wenn das Handy dunkel eingestellt ist oder in der Nachtzeit (Standard 19 bis 7 Uhr).
- **Nachtzeit einstellen (Super-Admin):** Admin → Einstellungen → **Nachtmodus**: Beginn und Ende wählen. Mit **„Nachtmodus in dieser Zeit für alle erzwingen“** ist die App in dieser Zeit für alle dunkel, auch bei „Hell“; der Knopf oben zeigt dann „Nacht 🔒“. Außerhalb der Zeit gilt wieder die eigene Wahl. Die Einstellung gilt nach kurzer Zeit auf allen Handys (auch auf dem Anmeldebildschirm, sobald das Handy sie einmal geladen hat).

## Tage zum Scannen freigeben

- Gebucht werden kann nur, wenn ein Admin einen Tag **zum Scannen freigegeben** hat (Admin → Tag). Sonst sehen Mitglieder „Gerade kein Tag zum Scannen freigegeben“.
- **Zum Scannen freigeben** / **Scannen beenden**: Freigeben gilt für einen Tag; wird ein anderer freigegeben, endet der vorige. „Tag anlegen und freigeben“ macht beides in einem Schritt.
- **Tag abschließen** beendet auch das Scannen. Ein abgeschlossener Tag ist nie freigegeben; wer ihn freigibt, öffnet ihn dabei wieder (mit Rückfrage).
- Buchungen, die Handys offline gespeichert haben, werden auch nach dem Beenden noch übertragen und zählen zu ihrem Tag.

## Nachtragen auf einen bestimmten Tag und „Zu wenig gebucht“

- **Buchung nachtragen** hat oben die Auswahl **„Nachtragen auf Tag“** – auch frühere oder abgeschlossene Tage (mit Hinweis). Die Liste darunter heißt „Am 14.11. gebucht von …“.
- **Zu wenig gebucht** (Admin, auch Vorstand): **„Tage prüfen“** rechnet für jeden Tag mit eingetragenem „Übrig“ pro Getränk *mitgenommen − gebucht − übrig*. Fehlende Flaschen erscheinen rot mit Betrag, zu viel Gebuchtes gelb. „Nachtragen auf diesen Tag“ springt direkt zum Nachtragen mit dem richtigen Tag. Gezählt wird per Zählabfrage – das kostet kaum Lesevorgänge.

## Backup

- **Backup speichern** (Admin → Daten) lädt eine JSON-Datei mit allem, was sich nicht neu berechnen lässt: Getränke (mit Sorte, Limits, Lager), Tage, Buchungen, Personen (mit Rolle, Gruppe, „unter 18“), alle Einstellungen (auch Gruppen, Fasnets-Titel, Easter-Egg-Regeln, Datenschutz, Nachtmodus), Korrekturanträge, Posteingang, Historie, Sammelalben, Easter-Egg-Verläufe, Fahrer-Markierungen und – abwählbar – Profilbilder. Nicht enthalten: Zähler und Bestenliste (werden nach dem Import neu berechnet) und die Logins (liegen bei Firebase Authentication). Vollständig wird es nur beim Super-Admin; Getränke-Admins fehlen Posteingang und Historie.
- **Backup importieren** (Super-Admin) zeigt vorher, was in der Datei steckt, und schreibt alle Felder vollständig zurück. Gleiche Einträge werden überschrieben, andere bleiben. Die Historie wird nur ergänzt, nie geändert. Ältere Backups und Daten der Offline-Version lassen sich weiter importieren.
- **Erinnerung:** Der Super-Admin stellt unter Daten ein, nach wie vielen Tagen ohne Backup erinnert wird (Standard 30, 0 = nie). Dann steht oben im Admin-Bereich „Zeit für ein Backup“ mit „Jetzt sichern“, und Admins bekommen einmal am Tag einen Hinweis.
- Die Datei enthält persönliche Daten – sicher aufbewahren (nicht in die WhatsApp-Gruppe).

- **Automatisches Backup vor dem Löschen (Super-Admin):** Bevor der Super-Admin etwas Größeres löscht – einen Tag mit Buchungen, eine Person (auf Wunsch mit Buchungen), einen Personeneintrag endgültig, alle erledigten Anträge oder Nachrichten, oder „Statistiken zurücksetzen“ –, kommen nacheinander: **Passwort erneut eingeben**, ein **automatisches Backup** (JSON-Datei im Download-Ordner, Name mit Datum und Uhrzeit) und eine **letzte Bestätigung**, die den Dateinamen nennt. Schlägt das Backup fehl, fragt die App, ob trotzdem ohne Backup weitergemacht werden soll. Getränke-Admins sind davon nicht betroffen.

## Bedienung

- **Abfragen** („Wirklich löschen?“, „Du fährst heute – trotzdem buchen?“ usw.) erscheinen als eigene Fenster im Stil der App statt als Browser-Fenster. Löschen/Entfernen/Sperren hat einen roten Knopf.
- **Tippen:** Die Enter-Taste der Handy-Tastatur springt ins nächste Feld („Weiter“); im letzten Feld schickt sie ab (Anmelden, Antrag senden, Passwort speichern …) bzw. übernimmt die Eingabe. Zahlenfelder markieren beim Antippen ihren Inhalt, sodass man direkt überschreiben kann.
- **Anzahl** (Korrekturantrag, Nachtragen, Einladen per Scan) hat **− / +** Knöpfe – meist braucht man die Tastatur gar nicht.
- Auf Android verkleinert die Tastatur die Seite, sodass Fenster und Eingabefelder sichtbar bleiben.
- **Taschenlampe 🔦:** Beim Scannen (Hauptseite und Admin-Scan) erscheint oben rechts im Kamerabild ein Lampen-Knopf, wenn Handy und Browser das Kameralicht freigeben (Android/Chrome meist ja, iPhone je nach iOS-Version). Die Wahl wird pro Gerät gemerkt – wer sie anlässt, hat beim nächsten Scannen gleich Licht. Beim Stoppen geht die Lampe aus.

## Partymodus 🪩

- **Erlauben (Super-Admin):** Admin → Einstellungen → Partymodus → „Partymodus erlauben“. Standard: aus. Ist er nicht erlaubt, kann niemand ihn starten; wird er verboten, während er läuft, endet er sofort für alle.
- **Starten:** Ist er erlaubt, haben Getränke-Admins und der Super-Admin auf der **Startseite** neben „Ich fahre heute“ den Knopf **„🪩 Party starten“**. Es erscheint ein Fenster mit der Dauer (1, 3, 6 Stunden oder offen) – „Starten“. Derselbe Knopf heißt dann „🪩 Party beenden“. Im Admin-Bereich geht beides ebenfalls.
- **Alle anderen** bekommen beim Start ein Fenster „🎉 Partymodus! … Willst du mitmachen?“ mit einer roten **Triggerwarnung** (wechselnde Farben und Bewegung; bei Lichtempfindlichkeit, Epilepsie oder Migräne nicht mitmachen) und **Mitmachen** / **Nein danke**. Die Warnung erscheint auch beim späteren Einsteigen. Wer mitmacht, bekommt einen bunten, sich drehenden Farbschleier mit Lichtflecken, Titel und Knöpfe wechseln die Farbe. Die App bleibt normal bedienbar.
- Unten links erscheint ein Knopf **„🪩 Party: dabei / aus“** zum Aus- und wieder Einsteigen.
- Die Farbwechsel sind bewusst sanft (kein schnelles Blitzen). Wer am Handy „Bewegung reduzieren“ eingeschaltet hat, bekommt einen ruhigen Farbschleier ohne Animation.

- **Effekte (Admins):** Welche Animationen laufen, wählen Admins im Admin-Bereich unter „Partymodus“ oder während der Party auf der Startseite über **🎛 Effekte**: *Farbwirbel, Lichtspots, Discokugel, Laser, Konfetti, Emoji-Regen, Seifenblasen, Funkeln, Leuchtende Knöpfe*. Schnellwahl: *Alle an*, *Ruhig* (Farbwirbel, Lichtspots, Funkeln) und *Standard* (Farbwirbel, Lichtspots, Discokugel, Konfetti). Änderungen gelten sofort für alle, die mitmachen. Nichts davon blitzt; wer am Handy „Bewegung reduzieren“ eingestellt hat, sieht keine Animationen.

## Mehr: Bestenliste, Album, Mein Konto, Rückblick

Unter der Liste „Deine Getränke heute“ gibt es bis zu vier Reiter. Welche jemand sieht, legt der Super-Admin unter Einstellungen fest (Standard: nur „Mein Konto“).

| Reiter | Inhalt |
|---|---|
| **Bestenliste** | heute oder gesamt, Personen und Gruppen. Wonach gewertet wird, legt der Super-Admin fest (Standard: alkoholfreie Getränke). |
| **Sammelalbum** | alle Easter Eggs als Abzeichen. Antippen zeigt, wie man es bekommt, und bei gesammelten wie oft und wann zuletzt. Minderjährige sehen nur die alkoholfreien. |
| **Mein Konto** | eigene Getränke und Kosten der Saison (11.11. bis Aschermittwoch) pro Tag und Getränk, als Excel herunterladbar. |
| **Rückblick** | die eigene Saison wie ein Jahresrückblick: Getränke, Tage, Lieblingsgetränk, stärkster Tag, Easter Eggs, Platz der Gruppe und ein Fasnets-Titel. **Als Bild teilen** erzeugt ein Bild für WhatsApp oder Instagram. Ab Aschermittwoch erscheint oben ein Hinweis. |

## Fasnets-Titel im Rückblick

- Im Rückblick bekommt jede Person einen **Fasnets-Titel**. Von oben nach unten gilt der erste Titel, dessen Bedingung passt; gezählt wird die Saison (11.11. bis Aschermittwoch). Unter dem Titel steht, warum man ihn hat, und **„Alle Titel ansehen“** zeigt die ganze Liste.
- **Super-Admin:** Admin → **Fasnets-Titel (Rückblick)**: Titel umbenennen, an/aus, mit ↑/↓ sortieren, löschen, **Neuer Titel**. Bedingungen: keine Buchung, Getränke mindestens/höchstens, Tage dabei, Anteil alkoholfrei (%), Bier, Getränke einer Sorte, Lieblingssorte, am stärksten Tag, Easter Eggs gesammelt, Platz der Gruppe, Strichliste in €, immer (Auffang-Titel ganz unten).
- Standard wie bisher: Noch ohne Strich → Vernunft-Wefzg (≥ 60 % alkoholfrei) → Treue Fasnetsseele (≥ 8 Tage) → Stammgast der Strichliste (≥ 40 Getränke) → Gmiatliche Wefzg. Zusätzlich vorbereitet, aber aus: Wasserratte, Durchhalte-Wefzg, Gruppen-Champion, Easter-Egg-Jäger, Seltener Gast. **„Standard-Titel zurückholen“** ergänzt gelöschte Standard-Titel, eigene bleiben.

## Einstellungen (Super-Admin)

Unter Admin → **Einstellungen**:
- **Kontakt:** Name, WhatsApp-Nummer (oder Link) und Telefon. Erscheint überall, wo man sich melden soll: „Konto wartet auf Freischaltung durch …“, „gesperrt“, „keine Rechte“ und in der Hilfe – mit Knöpfen „WhatsApp an …“ und „Anrufen“.
- **Wer sieht was?** Für Easter Eggs, Bestenliste, Sammelalbum und Saison-Rückblick jeweils: *niemand (nur Super-Admin)*, *Admins und Vorstand*, *alle Mitglieder* oder *ausgewählte Personen*. **Standard: ausgeblendet.** Gezählt wird trotzdem, beim Einschalten sind die Daten also da. „Mein Konto“ sehen immer alle.
- **Bestenliste – Regeln:** wonach Personen gewertet werden (alkoholfreie Getränke, alle Getränke oder Bier), Überschrift, Anzahl der Plätze, Gruppen-Wertung an/aus und wonach die Gruppen sortiert werden (Getränke gesamt, pro Kopf, alkoholfreie, Anteil alkoholfrei), Startansicht heute/gesamt.

Außerdem zeigt **Neu registriert** die letzten 25 Registrierungen mit Status (wartet, aktiv, abgelehnt, gesperrt); „Als gesehen markieren“ setzt die „neu“-Markierung zurück.

## Datenschutz

- Bei der **Registrierung** muss man den Datenschutzhinweis bestätigen und kann gleich seine **Gruppe** wählen.
- Der Hinweis steht unten auf jeder Seite („Datenschutz“) und in der Hilfe: wer verantwortlich ist, was gespeichert wird, wofür, wer was sieht (mit den aktuellen Einstellungen), wo die Daten liegen, wie lange und welche Rechte man hat.
- Bestehende Konten sehen einmal den Hinweis „Bitte lies kurz …“ mit „Gelesen und einverstanden“.
- **Konto löschen:** Mitglieder beantragen es im Datenschutzhinweis. Der Super-Admin sieht oben im Admin „Löschanträge“ und entfernt das Konto (auf Wunsch mit Buchungen); das Login löscht er in der Firebase-Konsole.
- **Wer hat bestätigt? (Super-Admin):** Admin → **Datenschutz-Bestätigungen** zeigt „x von y“, Fortschrittsbalken und eine Liste (Filter *Offen* / *Alle*) mit Datum der Bestätigung bzw. der letzten Erinnerung.
  - **Erinnern** (einzeln oder „Alle Offenen in der App erinnern“): Beim nächsten Öffnen der App erscheint der Datenschutzhinweis als Fenster mit „Erinnerung von …“ und dem Knopf **Gelesen und einverstanden**.
  - **E-Mail an alle Offenen:** öffnet dein Mailprogramm mit allen offenen Adressen in BCC und fertigem Text.
  - **Text für WhatsApp-Gruppe:** fertiger Erinnerungstext mit Link zum Teilen.
  - **Neue Fassung: alle erneut bestätigen lassen:** nach einer wichtigen Änderung des Hinweises müssen alle (auch du) neu bestätigen.
- Verein, Aufbewahrungsdauer und einen Zusatztext stellt der Super-Admin unter Einstellungen → Datenschutzhinweis ein. **Bitte den Text vom Vorstand bzw. Datenschutz-Verantwortlichen prüfen lassen** – er ist eine Vorlage, keine Rechtsberatung.
- Gruppen, Kontakt und Datenschutz-Angaben sind schon vor der Anmeldung lesbar (für die Registrierung).

## Schutz der Zähler und der Bestenliste

- Mitglieder dürfen einen Zähler pro Schreibvorgang nur um genau 1 ändern und in der Bestenliste nur ihren eigenen Eintrag und ihre eigene Gruppe (Firebase-Regeln).
- Admin-Handys gleichen alle 5 Minuten die Zähler des aktiven Tages mit den echten Buchungen ab und korrigieren sie. Größere Abweichungen (mehr als 2) landen in der Historie.
- Das Handy des Super-Admins prüft einmal am Tag alle Zähler „seit Beginn“. Dafür liest es nur die Buchungen der Tage neu, an denen sich etwas geändert hat (neue oder spät synchronisierte Buchungen, Stornos und Nachträge durch Admins, die letzten 14 Tage). Pro Tag merkt es sich eine kleine Zusammenfassung (Sammlung `dsum`, nur für den Super-Admin lesbar); daraus entstehen alle Zähler und die Bestenliste. So bleibt der Verbrauch klein, auch nach vielen Jahren.
- **Zähler neu berechnen** (Admin, nur Super-Admin) liest dagegen alle Buchungen einmal komplett – nur nötig, wenn etwas nicht stimmt. Nach einem Backup-Import passiert das automatisch bei der nächsten Prüfung.
- Seit Version 46 braucht das die neue Regel für `dsum`: `firestore.rules` neu in die Firebase-Konsole einfügen und veröffentlichen. Solange das fehlt, zeigt der Admin-Bereich einen roten Hinweis und die tägliche Prüfung ist ausgesetzt.

## Einkaufsliste

Admin → **Einkaufsliste**: Einkauf für wie viele Ausflüge, Basis (letzte wie viele Ausflüge) und Puffer eintragen, unter „Lagerbestand“ die Flaschen im Lager (oder „Lager = Übrig vom …“ übernehmen), dann **Einkaufsliste berechnen**. Verbrauch pro Ausflug = gebucht, oder mitgenommen − übrig, falls das mehr ist. Ergebnis in Kästen (größtes Gebinde), teilbar per WhatsApp oder kopierbar.

## Hilfe

Der **?**-Knopf oben erklärt Mitgliedern, wofür die App ist und wie Buchen, Offline, Mein Konto, Fahrer-Modus und Profilbild funktionieren – ohne Easter Eggs und Extras.

## Freischaltung neuer Konten

- Neue Konten können erst buchen, wenn ein **Admin** sie freischaltet. Admins sehen im Admin-Bereich ganz oben **Neue Anmeldungen** mit „Freischalten“ und „Ablehnen“ und bekommen eine kurze Meldung, wenn sich jemand registriert.
- Beim Freischalten lassen sich gleich **Gruppe** und **unter 18** setzen.
- Konten, die es vor Version 15 schon gab, bleiben aktiv. Der Super-Admin schaltet sich selbst automatisch frei.

## Jugendschutz

- Unter Admin → Personen (oder beim Freischalten) **unter 18** ankreuzen.
- Minderjährige sehen nur alkoholfreie Getränke. Bier, Radler/Mischbier, Wein/Sekt und Schnaps lassen sich für sie nicht buchen – das prüfen auch die Firebase-Regeln, also nicht nur die App. Admins können für sie ebenfalls nichts Alkoholisches nachtragen.
- Sie bekommen keine Easter Eggs mit Alkoholbezug, auch nicht die gemeinsamen der Gruppe.
- Wichtig dafür ist die richtige **Sorte** jedes Getränks (Admin → Getränke): *Bier, Radler/Mischbier, Alkoholfrei, Wein/Sekt, Wasser, Softdrink, Schnaps, Sonstiges*. Fehlt sie, setzt die App sie beim nächsten Öffnen durch einen Admin automatisch anhand des Namens; bitte einmal kontrollieren.

## Gruppen (Narrenfiguren)

- Der **Super-Admin** legt unter Admin → **Gruppen** die Gruppen an (z. B. Wilde Wefzg, Hofgoischd), Admins ordnen Personen unter Admin → Personen zu.
- Die Gruppe steht neben dem Namen, in der Bestenliste und im Rückblick.
- Gruppen-Easter-Eggs: „30 Getränke für die Gruppe“ und „Gruppe bleibt vernünftig“ (10 alkoholfreie). Sie erscheinen auf allen Handys.

## Kontakt, Fehler melden, Feedback (Posteingang)

- **Mitglieder** schreiben über **„Kontakt & Feedback“** (unten auf jeder Seite), über die Hilfe (**?**) oder über „Nachricht an …“ (z. B. beim Warten auf Freischaltung). Drei Arten: *Nachricht*, *Fehler melden* (auf Wunsch mit Geräte-Infos: App-Version, Handy, Browser, Bildschirm, online/offline) und *Feedback / Idee*.
- **Name und E-Mail** des Absenders kommen automatisch aus dem Konto – die Firebase-Regeln lassen keinen anderen Namen zu. Auch wartende oder gesperrte Konten können schreiben.
- **Super-Admin:** ganz oben im Admin **Posteingang (ungelesen)** mit Filtern *Offen / Fehler / Feedback / Alle*. Aufklappen markiert als gelesen; **Antworten** (erscheint beim Mitglied in der App, mit Hinweis), **Erledigt**, **Ungelesen**, **Löschen**, „Alle erledigten löschen“. Neue Nachrichten melden sich mit einem Hinweis.
- Mitglieder sehen unter „Meine Nachrichten“ ihre Nachrichten und Antworten; im Fußbereich steht „1 neu“, wenn eine Antwort da ist.
- WhatsApp als Kontaktweg ist entfallen; die Telefonnummer (falls eingetragen) bleibt als „Anrufen“.

## Zu viel gescannt

- Pro Getränk unter Admin → Getränke: **„Darf mehr gebucht werden (Flaschen)“**.
  - **Zahl eingetragen (auch 0): harte Grenze.** Sind *mitgenommen + diese Zahl* gebucht, lässt sich das Getränk nicht mehr buchen – weder per Scan noch mit „+1“. Mitglieder sehen „ausgebucht“ und „Bitte beim Getränke-Admin melden“. 0 heißt: Schluss, sobald alles Mitgenommene gebucht ist. Wurde das Getränk heute gar nicht mitgenommen, ist es gesperrt.
  - **Ausgebucht-Meldung:** Sobald ein Getränk mit Grenze voll ist, bekommen alle Admin-Handys oben die rot-schwarze Leiste „Getränk ausgebucht“ mit Ton und Vibration – und eine Handy-Benachrichtigung, wenn die App im Hintergrund läuft. Wird wieder Platz frei (Storno, mehr eingeladen), meldet sich das Getränk beim nächsten Vollwerden erneut.
  - **Leer: keine Grenze.** Es wird nur gemeldet (siehe unten).
  - Die Grenze greift erst, wenn für den Tag etwas als mitgenommen eingetragen ist. Admins können weiterhin nachtragen. Gezählt wird in `dused` (pro Tag und Getränk); Admin-Handys gleichen den Zähler laufend mit den echten Buchungen ab. Handys ohne Netz kennen den aktuellen Stand nicht – dort kann es einzelne Buchungen über der Grenze geben, die dann wie bisher gemeldet werden.
- Werden an einem Tag mehr Flaschen gebucht als *mitgenommen + dieser Wert*, bekommen **alle Admin-Handys** oben eine rot-schwarze Meldung mit Ton: welches Getränk, wie viele zu viel und **wer** die Buchungen über der Grenze gemacht hat. „Ansehen“ öffnet im Admin den Bereich **„Zu viel gescannt“** mit Uhrzeit und Person jeder überzähligen Buchung.
- Getränke, die an dem Tag gar nicht mitgenommen wurden und keinen Wert haben, lösen keine Meldung aus (dafür gibt es den Hinweis „nicht mitgenommen“).
- Wie bei „Getränke gehen zu Ende“: Die Meldung kommt nur, solange die App auf dem Admin-Handy offen ist (mit erlaubten Benachrichtigungen auch im Hintergrund).

## Korrektur- und Nachbuchungsanträge

- **Mitglieder:** unter „Deine Getränke heute“ und in „Mein Konto“ auf **Korrektur beantragen**: *Nachbuchen* oder *Stornieren*, Tag (die letzten Ausflüge), Getränk, Anzahl (bis 20 pro Antrag – für mehr einen zweiten Antrag stellen) und eine Bemerkung. Beim **Stornieren** stehen nur Getränke zur Auswahl, die man an dem Tag selbst gebucht hat, und höchstens so viele, wie gebucht wurden (abzüglich schon beantragter Stornos). Unter **Meine Anträge** steht der Status; offene Anträge lassen sich zurückziehen. Wird entschieden, kommt eine Meldung.
- **Getränke-Admins und Super-Admin:** oben im Admin **Korrekturanträge** mit **Genehmigen** / **Ablehnen** (mit Begründung). Genehmigen bucht nach bzw. storniert die jüngsten passenden Buchungen und zieht die Zähler mit. Neue Anträge melden sich mit einem Hinweis. Erledigte stehen aufklappbar darunter, alles steht auch in der Historie.
- **Super-Admin:** kann jeden Antrag löschen (Knopf „Löschen“ an jedem Antrag) und unter „Erledigt“ alle erledigten auf einmal. Gelöscht wird nur der Antrag – Buchungen, die durch eine Genehmigung entstanden oder storniert wurden, bleiben, wie sie sind. Das Löschen steht in der Historie.
- Jugendschutz gilt auch hier: Minderjährige können keinen Alkohol nachbuchen lassen. Der Vorstand sieht die Anträge nicht.

## Admin-Bereich öffnen

Es gibt keinen sichtbaren Admin-Knopf. **Fünfmal schnell hintereinander auf den Titel** oben tippen (innerhalb von zwei Sekunden), dann öffnet sich der Admin-Bereich. Zurück geht es über „Zurück zum Scannen“ oben rechts. Oben steht eine **Navigation** mit allen Bereichen (antippen springt hin). Jeder Bereich lässt sich über seine Überschrift **ein- und ausklappen**, „Alle auf“ / „Alle zu“ schaltet alle; das Handy merkt sich die Auswahl. Das ist nur ein Sichtschutz – die eigentlichen Rechte prüfen die Firebase-Regeln. Wer kein Admin und nicht Vorstand ist, bleibt einfach in der normalen Ansicht – oder wird auf die Seite weitergeleitet, die der Super-Admin unter Einstellungen → Kontakt bei „Weiterleitung“ einträgt (z. B. https://wexhainer.de/). Die Einrichtungs-Ansicht mit der eigenen Nutzer-ID (für `superUids` in `firestore.rules`) erreicht man nur noch über die Adresse mit **`?setup=1`** am Ende, z. B. `…/index.html?setup=1`, dann 5× auf den Titel tippen.

## Excel-Auswertung (Admins)

Unter Admin → **Excel-Auswertung** einen Zeitraum wählen (oder „Gesamter Zeitraum“, „Dieser Monat“, „Gewählter Tag“) und **Excel erstellen** tippen. Die Datei enthält:

| Blatt | Inhalt |
|---|---|
| Übersicht | Zeitraum, Anzahl Tage und Personen, Getränke, Wert, Fehlmengen, Liste der Tage |
| Pro Person | jedes Mitglied mit Anzahl je Getränk, Getränke gesamt und **Kosten**, darunter die Summen |
| Person pro Tag | wer an welchem Tag was getrunken hat, mit Preis und Summe |
| Abgleich pro Tag | mitgenommen, gebucht, Soll-Rest, gezählt, Differenz und Werte |
| Buchungen | jede einzelne Buchung mit Datum, Uhrzeit, Person, Getränk und Art |
| Getränke | Stammdaten mit Sorte, Preis, Gebinde und Barcodes |

Summen und Kosten stehen als Formeln drin, Kopfzeilen sind fixiert und mit Filter versehen. Die Kosten werden mit den **aktuellen** Preisen berechnet. Die Datei entsteht direkt im Browser (ohne zusätzliche Bibliothek); dafür wird nur Internet für das Laden der Buchungen gebraucht.

## Historie (Super-Admin)

Unter Admin → **Historie** steht jede Änderung: Mitglieder (Registrierung, Name, Rolle, Sperre, Entfernen, Profilbild, Passwort), Buchungen (Rückgängig, Storno, Nachtrag), Einladen und Zählen, Getränke, Tage, Easter Eggs und System (Zähler, Import, Zurücksetzen). Datum wählen, die Bereiche lassen sich aufklappen. Mit „auch normale Scans“ erscheinen zusätzlich alle Buchungen des Datums. Die Historie kann niemand ändern oder löschen – auch der Super-Admin nicht, und auch „Statistiken zurücksetzen“ lässt sie stehen.

## Buchungen einer Person korrigieren

Unter Admin → **Buchung nachtragen** die Person wählen: Darunter stehen ihre Buchungen des Tages, pro Getränk mit „−1 stornieren“ und aufklappbar einzeln. Nachtragen und Stornieren passen die Easter-Egg-Zähler automatisch an.

## Statistiken zurücksetzen (Super-Admin)

Admin → Daten → **Statistiken zurücksetzen**, dann das eigene Passwort eingeben. Gelöscht werden alle Easter-Egg-Zähler (Gruppe und Personen, heute und seit Beginn) und alle Easter-Egg-Verläufe; danach können alle Easter Eggs neu auslösen. Mit gelöscht werden Bestenliste, Sammelalben und Fahrer-Einträge. Optional lassen sich zusätzlich **alle Buchungen und Tage** löschen. Getränke, Personen, Easter-Egg-Regeln und die Historie bleiben erhalten.

## Rollen

Rollen vergibt der Super-Admin unter Admin → Personen → Rolle.

| | Mitglied | Vorstand | Getränke-Admin | Super-Admin |
|---|:-:|:-:|:-:|:-:|
| Getränke für sich buchen, eigene Buchung zurücknehmen, Passwort ändern | ✓ | ✓ | ✓ | ✓ |
| Tag, Abgleich, Nach Person, Buchungen, Getränke, Personen **ansehen** | | ✓ | ✓ | ✓ |
| Excel-Auswertung erstellen | | ✓ | ✓ | ✓ |
| Tage, Getränke, Einladen, Zählen, Abgleich **ändern**, CSV, Backup speichern | | | ✓ | ✓ |
| Buchungen stornieren und nachtragen, Namen korrigieren | | | ✓ | ✓ |
| Neue Konten freischalten, unter 18 markieren, Gruppe zuordnen | | | ✓ | ✓ |
| Partymodus starten und beenden | | | ✓ | ✓ |
| Einstellungen: Kontakt, wer sieht was, Bestenliste-Regeln | | | | ✓ |
| Easter Eggs verwalten und an/aus, Zähler neu berechnen, Gruppen anlegen | | | | ✓ |
| Neu registrierte Mitglieder, Historie, Statistiken zurücksetzen | | | | ✓ |
| Tag löschen, Backup importieren | | | | ✓ |
| Rollen vergeben, Konten sperren, entfernen, Passwort-Mail | | | | ✓ |

- **Vorstand** sieht im Admin-Bereich (5× auf den Titel tippen) alles nur lesend, ohne Rollen der Personen. Einziger Knopf ist „Excel erstellen“.
- **Getränke-Admin** (früher „Admin“) kümmert sich um Tage, Getränke und Abgleich. Easter Eggs an/aus und „Zähler neu berechnen“ darf er nicht mehr.

Die Rechte prüfen die Firebase-Regeln, nicht nur die App.
- **Sperren:** wirkt sofort. Die Person sieht „Dein Konto ist gesperrt“ und kann nichts mehr buchen.
- **Entfernen:** sperrt das Konto und nimmt es aus der Liste. Auf Wunsch werden dabei alle Buchungen der Person gelöscht. Unter „Entfernte Konten“ lässt sich das rückgängig machen.
- **Login endgültig löschen:** In der Firebase-Konsole unter Authentication → Nutzer, über das Menü „⋮“ der Person. Die App verlinkt dorthin. Direkt aus der App geht das im kostenlosen Tarif nicht. Fremde Passwörter setzen geht ebenfalls nicht; dafür gibt es die Passwort-Mail.

## Profilbilder

- Jedes Mitglied tippt in der Scan-Ansicht auf den runden Kreis neben „Gebucht als“ und wählt ein Foto (Kamera oder Galerie). Das Bild wird auf dem Handy quadratisch zugeschnitten und auf 160×160 Pixel verkleinert (etwa 10 KB).
- Das Bild erscheint groß im Easter-Egg-Popup und klein im Verlauf „Easter Eggs heute“, bei Gruppen-Easter-Eggs immer das Bild der Person, die es ausgelöst hat. Ohne Bild erscheinen die Initialen.
- „Bild entfernen“ steht direkt darunter. **Admins** sehen die Bilder unter Admin → Personen und können unpassende entfernen.
- Die Bilder liegen in der Datenbank (nicht in Firebase Storage, das für neue Projekte den kostenpflichtigen Tarif bräuchte). Andere Handys laden ein Bild einmal pro Sitzung und merken es sich.

## Namen

- Der Name wird bei der Registrierung festgelegt und hängt am Konto. Mitglieder können ihn nicht ändern, das verhindern die Sicherheitsregeln.
- Admins und Super-Admins können einen Namen unter **Admin → Personen** korrigieren, z. B. bei Tippfehlern. Dort stehen auch die E-Mail-Adressen. Gleiche Namen werden als „doppelt“ markiert.
- Bei älteren Konten fehlt die E-Mail in der Liste noch. Sie wird automatisch ergänzt, sobald die Person die App das nächste Mal öffnet.
- Wer mit einer zweiten E-Mail-Adresse ein weiteres Konto anlegt, taucht in der Personenliste als eigener Eintrag auf.

## Ablauf an einem Tag

1. **Vorher (Admin):**
   - **Neuer Tag** mit Datum und Ziel anlegen. Er wird automatisch der aktive Tag für alle Handys.
   - Optional „Rest vom Vortag übernehmen“ ankreuzen.
   - **Einladen per Scan:** Flasche oder Kasten scannen und „+1 Kasten“ bzw. „+1 Flasche“ antippen.
2. **Unterwegs (alle):** Getränk nehmen, Barcode scannen.
3. **Danach (Admin):**
   - **Rest zählen per Scan** oder in der Spalte **Übrig** direkt eintippen, wie viele Flaschen übrig sind.
   - Die Spalte **Diff.** zeigt, was nicht gescannt wurde.
   - Mit **Nach Person als CSV** gibt es die Abrechnung pro Mitglied.
   - **Tag abschließen**.

## Warnung, wenn Getränke zu Ende gehen

- Unter **Admin → Getränke** bei jedem Getränk **„Warnen ab Rest (Flaschen)“** eintragen, z. B. 6. Ohne Wert gibt es für dieses Getränk keine Warnung.
- Sinkt am aktiven Tag der Soll-Rest (mitgenommen minus gebucht) auf diesen Wert, bekommen **alle Admins** unten eine rot-schwarze Hinweisleiste mit Ton und Vibration – egal, ob sie gerade scannen oder im Admin-Bereich sind. Bei „leer“ kommt ein zweiter Hinweis.
- **OK** schließt die Leiste. Wird später doch mehr eingetragen, ist die Warnung wieder scharf.
- In der **Tagesübersicht** stehen „Leer“ und „Geht zu Ende“ ganz oben, im **Abgleich** sind die Zeilen markiert.
- **Hinweis:** Die Warnung kommt nur, solange die App auf dem Admin-Handy geöffnet ist. Mit „Warnungen aufs Handy erlauben“ (Admin → Daten) erscheint sie zusätzlich als Handy-Benachrichtigung, wenn die App im Hintergrund offen ist – auf dem iPhone nur, wenn die App über „Zum Home-Bildschirm“ installiert ist. Benachrichtigungen bei ganz geschlossener App bräuchten einen eigenen Server (Firebase Blaze-Tarif).

## Easter Eggs

Bei bestimmten Buchungen erscheint ein Vollbild mit Wespe, Animation, Ton und Vibration. Es bleibt stehen, bis man **Schließen** tippt. Mit **Teilen** lässt es sich verschicken (Bild, z. B. per WhatsApp). Wer zum ersten Mal ein Abzeichen bekommt, sieht danach ein eigenes **Sammelalbum-Popup** (helle Karte mit Medaille, „x von y Abzeichen“) – nur wenn das Sammelalbum für ihn sichtbar ist. Solange es offen ist, pausiert der 10-Sekunden-Countdown der Buchungsmeldung. Easter Eggs sieht nur, wer sie laut „Wer sieht was?“ sehen darf. Jedes Easter Egg kommt pro Person und Tag nur einmal, auch nach „Rückgängig“.

**Nur auf dem eigenen Handy**
- 1., 3., 5., 7., 8., 9. und 10. Bier am Tag, danach bei jedem weiteren Bier (mit zunehmendem Augenzwinkern Richtung Heimfahrt)
- erstes Wasser nach mindestens 3 Bier
- Schnapszahl bei den eigenen Getränken (11, 22, 33 …): heute und seit Beginn
- Scan um 11:11 Uhr, um Mitternacht und am 11.11.
- für jede Sorte etwas: erstes und fünftes/zehntes **Wasser**, erster und fünfter **Softdrink**, erstes und drittes **Alkoholfreies**, **Radler**, **Wein/Sekt** und **Schnaps**, das erste **sonstige** Getränk, „Schlau!“ (Wasser nach mindestens 2 Schnäpsen)
- 50 und 100 Getränke seit Beginn

**Auf allen Handys** („Max Meier hat … ausgelöst“)
- Anstich: erster Scan des Tages
- 10., 20. und 30. Bier der ganzen Gruppe am Tag
- 10 Wasser, 10 Softdrinks und 11 Schnäpse der ganzen Gruppe am Tag
- alle 100 Getränke seit Beginn
- Schnapszahl bei allen Getränken der Gruppe: heute und seit Beginn

**Für jede Getränkesorte derselbe Satz:** 1., 3., 5. und 10. am Tag sowie 25. und 50. seit Saisonbeginn – für Bier, Radler, Alkoholfrei, Wein/Sekt, Wasser, Softdrinks, Schnaps und Sonstiges. Bestehende Easter Eggs bleiben; wo es schon eins mit derselben Zahl gibt (z. B. 1. Bier), wird keins doppelt angelegt.

**Nach Sorte ein-/ausschalten (Super-Admin):** Admin → Easter Eggs verwalten → „Nach Getränkesorte ein-/ausschalten“. Pro Sorte zwei Schalter: *Easter Eggs* (aus = für diese Sorte kommt gar nichts) und *Sammelalbum* (aus = Popups ja, aber nicht im Sammelalbum). Darunter lässt sich die Liste nach Sorte filtern.

**Animationen:** Konfetti, Sprudelblasen, Getränke-Regen (passendes Emoji zur Sorte), Feuerwerk, Wespenschwarm, „Dreh durch“ (die Karte wirbelt herein), Luftballons und Sternenregen – jeweils mit eigener Melodie und eigenem Vibrationsmuster. Standard ist „automatisch“: Die App wählt abwechselnd eine Animation, die zum Getränk passt. Im Editor lässt sich pro Easter Egg eine feste Animation oder „Zufall“ einstellen.

Hinweise:
- **Was als Bier zählt**, legt die **Sorte** des Getränks fest (Admin → Getränke). Ohne Angabe rät die App aus dem Namen. Radler und Alkoholfreies zählen nicht als Bier.
- Die **Gruppen-Easter-Eggs** brauchen Netz. Offline wird weiter mitgezählt, aber ohne Meldung.
- Nach Stornos, Nachträgen oder einem Import unter **Admin → Daten → Zähler neu berechnen** tippen, damit die Zahlen wieder stimmen.
- **Easter Eggs: an/aus** schaltet alles für alle Handys ab.
- **Verlauf für alle:** In der Scan-Ansicht steht unter „Deine Getränke heute“ der Abschnitt „Easter Eggs heute“ mit zwei Reitern: *Für alle* (alle gemeinsamen Easter Eggs des Tages mit Uhrzeit und Auslöser) und *Deine* (die eigenen). Die eigenen sind in der Datenbank gespeichert und bleiben auch bei einem Handywechsel erhalten.
- **Vorschau für Admins:** Im Admin-Bereich zeigt „Anstehende Easter Eggs“ für den aktiven Tag, was als Nächstes kommt – für die Gruppe (Anstich, nächstes Gruppen-Bier, nächste Schnapszahl, 11:11 und 11.11.) und pro Person, sortiert danach, wer am nächsten dran ist.
- **Verwalten (Super-Admin):** Unter Admin → **Easter Eggs verwalten** lässt sich jedes Easter Egg an- und ausschalten, ändern, kopieren, löschen und als Vorschau anzeigen; „Neues Easter Egg“ legt eins an, „Standard-Easter-Eggs zurückholen“ setzt nur die mitgelieferten zurück und holt gelöschte wieder – **eigene Easter Eggs bleiben erhalten**. Einstellbar sind:
  - **Für:** jede Person für sich oder alle gemeinsam
  - **Wann:** genau bei einer Anzahl, bei jeder n-ten, ab einer Anzahl jedes Mal, bei Schnapszahl, „erstes … nach mindestens n …“, zu einer Uhrzeit, an einem Datum
  - **Getränk** (Sorte oder alle) und **Zeitraum** (heute oder seit Beginn)
  - **Animation:** automatisch, fest oder Zufall
  - **Texte:** eine Zeile pro Variante im Format `Titel | Text`; mehrere Zeilen wechseln sich ab. `{n}` wird durch die Zahl ersetzt, `{name}` durch die Person.
- Die mitgelieferten Easter Eggs stehen als `DEFAULT_RULES` in `index.html`. Sobald der Super-Admin etwas ändert, wird die Liste in der Datenbank gespeichert und gilt für alle Handys. Neue mitgelieferte Easter Eggs aus späteren Updates kommen automatisch dazu – außer solche, die der Super-Admin gelöscht hat.
  - **Animation:** pro Easter Egg wählbar (siehe oben).

## Kosten und Grenzen (kostenloser Spark-Tarif)

- **Datenbank:** 50.000 Lesevorgänge und 20.000 Schreibvorgänge pro Tag (Zurücksetzen gegen 9 Uhr deutscher Zeit). Jede Buchung kostet etwa vier Schreibvorgänge (also bis ca. 5.000 Buchungen am Tag) und auf jedem Admin-Handy, das gerade offen ist, einen Lesevorgang. Jedes Öffnen der App kostet ein Mitglied etwa 30–50 Lesevorgänge; ein Admin-Handy, das nach längerer Pause den Admin-Bereich öffnet, liest die Buchungen des Tages und die Mitgliederliste neu.
- **Faustregel:** bis ca. 60 Handys mit 3–4 Admin-Handys und 1.500 Getränken am Tag passt es gut. Darüber: nur so viele Admins wie nötig, oder den Blaze-Tarif (Abrechnung nach Verbrauch, mit Budget-Warnung) nehmen.
- Ist das Tageskontingent aufgebraucht, sperrt Firebase bis zum Zurücksetzen; Buchungen bleiben auf den Handys gespeichert und werden danach gesendet.
- **Anmeldung:** bis zu 100 neue Konten pro Stunde und Internetanschluss. Das genügt auch, wenn sich alle gleichzeitig im selben WLAN registrieren.
- **E-Mails:** 150 „Passwort vergessen“-Mails pro Tag.

## Update der App

Aktuell ist **Version 59**. Nach Änderungen zwei Zahlen gleich hochzählen (z. B. von 59 auf 60):
- in `index.html`: `const APP_VERSION = '59';`
- in `sw.js`: `const VERSION = 'strichliste-v59';`

Seit Version 56 gibt es neue Regeln (`dused` für die Buchungsgrenze; vorher Backup, `dsum` und `theme`). Falls noch nicht geschehen: `firestore.rules` neu in die Firebase-Konsole einfügen und veröffentlichen.

Dann holen sich alle Handys beim nächsten Öffnen mit Netz die neue Version. Ganz unten auf der Seite steht „Version 59“. Daran siehst du, ob das Update auf einem Handy angekommen ist. Falls nicht: die Seite schließen und neu öffnen, beim zweiten Öffnen ist die neue Version aktiv.

## Daten aus der Offline-Version übernehmen

1. In der alten Version unter **Admin → Daten → Backup speichern** eine JSON-Datei erzeugen.
2. In der neuen Version unter **Admin → Daten → Backup importieren** diese Datei auswählen (nur der Super-Admin).

Die alten Buchungen haben keinen Personennamen.
