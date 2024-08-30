**Border + Quellennachweis Simone Arnold: Vorlesung Datenanalyse mit Python. 03 Funktionen. Fachhochschule Dortmund**

Um mit Python auf eine Datei zuzugreifen, muss diese fürs Lesen oder Schreiben geöffnet werden. Dazu wird in Python die Funktion [open](https://docs.python.org/3/library/functions.html#open) verwendet. Diese nimmt zwei Argumente, den Pfad der Datei und den Zugriffsmodus, an und liefert ein [Dateiobjekt](https://docs.python.org/3/glossary.html#term-file-object) zurück. Aus dem Dateiobjekt werden dann die Inhalte der Datei ausgelesen.

#### Dateipfad
Der lokale Dateipfad wird ausgehend vom aktuellen Arbeitsverzeichnis angegeben.

``` {python}

pfad_maya = "skript/01-daten/dice-maya.txt"
pfad_hans = "skript/01-daten/dice-hans.txt"

```

:::: {#tip-wd-Python .callout-tip collapse="false"}
## Arbeitsverzeichnis in Python ermitteln und wechseln
Der Pfad des aktuellen Arbeitsverzeichnisses kann mit dem Modul os mittels `os.getcwd()` ermittelt werden (hier ohne Ausgabe). Mit `os.chdir('neuer_pfad')` kann das Arbeitsverzeichnis ggf. gewechselt werden. Die korrekte Formatierung des Pfads erkennen Sie an der Ausgabe von `os.getcwd()`.

``` {python}
#| output: false

import os
print(os.getcwd())
```

::::

#### Zugriffsmodus
Als Zugriffsmodus stehen unter anderem folgende Optionen zur Verfügung:

| Modus | Beschreibung |
| --- | ----------- |
| `r` | lesender Zugriff |
| `w` | Schreibzugriff, Datei wird überschrieben |
| `x` | Erzeugt die Datei, Fehlermeldung, wenn die Datei bereits existiert |
| `a` | Schreibzugriff, Inhalte werden angehängt |
| `b` | Binärmodus (z. B. für Grafiken) |
| `t` | Textmodus, default|

&nbsp;

Die Zugriffsmodi können auch kombiniert werden. Weitere Informationen dazu finden Sie in der [Dokumentation](https://docs.python.org/3/library/functions.html#open). Sofern nicht im Binärmodus auf Dateien zugegriffen wird, liefert die Funktion `open()` den Dateiinhalt als string zurück. 

Im Lesemodus wird ein Datenobjekt erzeugt. 

``` {python}

daten_maya = open(pfad_maya, mode = 'r')
print(daten_maya)
```

Wenn das Datenobjekt `daten_maya` der Funktion `print()` übergeben wird, gibt Python die Klasse des Objekts zurück, in diesem Fall also _io.TextIOWrapper. Diese Klasse stammt aus dem Modul io und ist für das Lesen und Schreiben von Textdateien zuständig. Ebenfalls werden der Dateipfad, der Zugriffsmodus und die Enkodierung der Datei ausgegeben. cp65001 **möp: da steht jetzt cp1252...** ist in Windows (dem Betriebssystem, auf dem dieser Baustein erstellt wurde) eine Kodierung für UTF-8. Sollte dies nicht automatisch erfolgen, kann eine Kodierung in UTF-8 auch mit dem Argument `encoding='UTF-8'` übergeben werden. Die Attribute der Datei können mit entsprechenden Befehlen abgerufen werden.

``` {python}

print(f"Dateipfad: {daten_maya.name}\n"
      f"Dateiname: {os.path.basename(daten_maya.name)}\n"
      f"Datei ist geschlossen: {daten_maya.closed}\n"
      f"Zugriffsmodus: {daten_maya.mode}")


```

#### Daten ausgeben
Um den Dateiinhalt auszugeben, kann das Datenobjekt mit einer Schleife zeilenweise durchlaufen und ausgegeben werden. (Die Datei dice-maya hat nur eine Zeile.) 

``` {python}

i = 0
for zeile in daten_maya:
    print(f"Inhalt Zeile {i}, mit {len(zeile)} Zeichen:")
    print(zeile)
    i += 1

```

Dies ist jedoch für größere Dateien nicht sonderlich praktikabel. Die Ausgabe einzelner Zeilen mit der Funktion `print()` kann aber nützlich sein, um die genaue Formatierung der Zeichenkette zu prüfen. In diesem Fall hat Maya ihre Daten in Anführungszeichen gesetzt und mit einem Komma voneinander getrennt.

:::: {#tip-zeilenweise .callout-tip collapse="false"}
## Daten kontrollieren

Beim Einlesen goßer Datensätze sollten nicht nur die erste(n) Zeile(n) des Datensatzes, sondern auch Ausschnitte aus der Mitte und dem Ende kontrolliert werden. Dies hilft, Fehler etwa bei der Umwandlung von Dezimal- und Tausendertrennzeichen, des Datumsformats oder eine unerwartete Anzahl fehlender Werte und sonstige Auffälligkeiten zu identifizieren.

::::

#### Daten einlesen
Um den gesamten Inhalt einer Datei einzulesen, kann die Methode [datenobjekt.read()](https://docs.python.org/3/tutorial/inputoutput.html) verwendet werden. Die Methode hat als optionales Argument `.read(size)`. size wird als Ganzzahl übergeben und entsprechend viele Zeichen (im Binärmodus entsprechend viele Bytes) werden ggf. bis zum Dateiende ausgelesen.

``` {python}

augen_maya = daten_maya.read()

print(f"len(augen_maya): {len(augen_maya)}\n\n"
      f"Inhalt der Datei augen_maya:\n{augen_maya}")

```

Das hat offensichtlich nicht geklappt, der ausgelesene Dateiinhalt ist leer! Der Grund dafür ist, dass beim Lesen (und beim Schreiben) einer Datei der Dateizeiger die Datei durchläuft. Nachdem die Datei daten_maya im Abschnitt "Daten ausgeben" zeilenweise ausgegeben wurde, steht der Dateizeiger am Ende der Datei.

:::: {#wrn-Dateizeiger .callout-warning appearance="simple"}
## Dateizeiger in Python

Wird eine Datei zeilenweise oder mit der Methode `.read()` ausgelesen, wird der Dateizeiger um die angegebene Zeichenzahl bzw. bis ans Ende der Datei bewegt. Wird beispielsweise ein Datensatz 'daten' geöffnet und mit der Methode `daten.read(3)` die ersten drei Zeichen ausgelesen, bewegt sich der Dateizeiger von der Indexposition 0 zur Indexposition 3 (bzw. steht jeweils davor).

::::: {#fig-Dateizeiger layout-ncol=2}

![](skript/00-bilder/indexposition-0.png){fig-alt="Dargestellt ist ein in fünf Blöcke unterteilter Streifen, der einen eindimensionalen Datensatz repräsentiert. Die Blöcke sind entlang der 0. Achse von links nach rechts mit 0 bis 4 beschriftet. Oberhalb steht links vom Block Null ein Pfeil mit der Beschriftung Indexposition Null"}

![](skript/00-bilder/indexposition-3.png){fig-alt="Dargestellt ist der gleiche Streifen. Oberhalb steht links vom Block drei ein Pfeil mit der Beschriftung Indexposition drei"}

Bewegung des Dateizeigers beim Auslesen von drei Zeichen
:::::

Die Methode `daten.tell()` gibt zurück, an welcher Position sich der Dateizeiger befindet.

Mit der Methode `daten.seek(offset, whence = 0)` wird der Zeiger an eine bestimmte Position gesetzt. Die Methode akzeptiert das Argument offset (Versatz) und das optionale Argument whence (woher), dessen Standardwert 0 (Dateianfang) ist. Für Zugriffe **im Binärmodus** (`open(pfad, mode = 'rb')`) kann das Argument whence außerdem die Werte 1 (aktuelle Position) oder 2 (Dateiende) annehmen.

  * `daten.seek(0, 0)` bezeichnet den Dateianfang

  * `daten.seek(0, 1)` bezeichnet die aktuelle Position in der Datei

  * `daten.seek(0, 2)` bezeichnet das Dateiende

  * `daten.seek(-3, 2)` bezeichnet das dritte Zeichen vor dem Dateiende

::::

Wird der Dateizeiger mit der Methode `datenobjekt.seek(0)` an den Dateianfang gestellt, gelingt das Auslesen der Datei.

``` {python}
#| results: hold

print(f"Position des Dateizeigers vor dem Zurücksetzen auf 0: {daten_maya.tell()}")

daten_maya.seek(0);
print(f"Position des Dateizeigers nach dem Zurücksetzen auf 0: {daten_maya.tell()}")

augen_maya = daten_maya.read()

print(f"Inhalt des Objekts augen_maya:\n{augen_maya}")
```

**Geben Sie aus dem Datenobjekt daten_maya mit den Methoden .seek() und .read() die Zahlen and zweiter und dritter Stelle, also 6 und 2, aus.**

:::: {#tip-Musterlösung-Zeigerposition .callout-tip collapse="true"}
## Musterlösung Dateizeiger bewegen

``` {python}

daten_maya.seek(6, 0);
print(daten_maya.read(1))

daten_maya.seek(daten_maya.tell() + 4, 0);
print(daten_maya.read(1))
```

::::

Um Mayas Würfelergebnisse zu addieren, müssen die Zahlen extrahiert und in Ganzzahlen umgewandelt werden, da im Textmodus stets strings zurückgegeben werden.

``` {python}

print(type(augen_maya))
```

Dazu werden mit der Methode `str.strip(")` das führende und abschließende Anführungszeichen entfernt sowie anschließend mit der Methode `str.split('", "')` der String in eine Liste aufgeteilt. Anschließend werden die Listenelemente in Ganzzahlen umgewandelt und summiert.

``` {python}
#| results: hold

print(f"augen_maya:\n{augen_maya}")

augen_maya = augen_maya.strip('"')
print(f"\naugen_maya.strip('\"'):\n{augen_maya}")

augen_maya = augen_maya.split('", "')
print(f"\naugen_maya.split('\", \"'):\n{augen_maya}")

augen_maya_int = []
for i in augen_maya:
  augen_maya_int.append(int(i))

print(f"\naugen_maya_int:\n{augen_maya_int}\n\nSumme Augen: {sum(augen_maya_int)}")
```

#### Datei schließen

Nach dem Zugriff auf die Datei, muss diese wieder geschlossen werden, um diese für andere Programme freizugeben.
``` {python}

daten_maya.close()
```

:::: {#wrn-Schreiboperationen .callout-warning appearance="simple"}
# Schreiboperationen mit Python

Das Schließen einer Datei ist besonders für Schreiboperationen auf Datenobjekten wichtig. Andernfalls kann es passieren, dass Inhalte mit `datenobjekt.write()` nicht vollständig auf den Datenträger geschrieben werden. Siehe dazu die [Dokumentation](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files).

::::

### Übung eindimensionale Datensätze

**Welche Augenzahl hat Hans erreicht?**

**Die Musterlösung kann Marc machen**

::: {#tip-Musterlösung-Augenzahlvergleich .callout-tip collapse="true"}
## Musterlösung Augenzahlvergleich
Musterlösung von Marc

:::