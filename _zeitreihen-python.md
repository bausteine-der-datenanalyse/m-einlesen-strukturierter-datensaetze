## Zeitreihen {#sec-zeitreihen}
Die Verarbeitung von Datums- und Zeitinformationen wird in Python durch verschiedene Module ermöglicht. Im Folgenden wird die Arbeit mit Zeitreihen mit den Modulen NumPy und Pandas weiter ausgeführt. Dennoch sollten Sie die hier aufgezählten Module kennen, da in der Dokumentation gelegentlich auf diese verwiesen wird.

  - Das Modul time stellt Zeit- und Datumsoperationen mit Objekten vom Typ `struct_time` bereit. ([Dokumentation des Moduls time](https://docs.python.org/3/library/time.html))

       - Das Modul datetime führt die Datentypen `datetime` und `timedelta`, zusätzliche Methoden für die Bearbeitung und die Ausgabe von Datums- und Zeitinformationen ein. Das Modul kann Jahreszahlen von 1 bis 9999 nach unserer Zeitrechnung im Gregorianischen Kalender verarbeiten. ([Dokumentation des Moduls datetime](https://docs.python.org/3/library/datetime.html#))
       
       - Das Modul calendar führt verschiedene Kalenderfunktionen ein und erweitert den verarbeitbaren Zeitraum. Basierend auf dem Gregorianischen Kalender reicht dieser in beide Richtungen ins Unendliche. ([Dokumentation des Moduls calendar](https://docs.python.org/3/library/calendar.html#module-calendar))

   - NumPy führt die Datentypen `datetime64` und `timedelta64` ein. Diese basieren auf dem Gregorianischen Kalender und reichen in beide Richtungen ins Unendliche. https://de.wikipedia.org/wiki/Internationale_Atomzeit <https://numpy.org/doc/stable/reference/arrays.datetime.html>

  - Pandas nutzt die NumPy-Datentypen `datetime64` und `timedelta64` und ergänzt zahlreiche Funktionen zur Verarbeitung von Datums- und Zeitinformationen aus anderen Paketen. <https://pandas.pydata.org/docs/user_guide/timeseries.html>

### Alles ist relativ: die Epoche
Python speichert Zeit relativ zu einem zeitlichen Bezugspunkt, der sogenannten Epoche. **Was fällt Ihnen auf?**  
(*Nicht gemeint ist `tm_isdst=0`, das angibt, ob daylight saving time, also Sommerzeit, ist.*)

``` {python}
# time
import time as time
print(time.gmtime(0))

# datetime
import datetime as datetime
print("datetime:", datetime.datetime.fromtimestamp(0))

# NumPy
import numpy as np
print("NumPy:", np.datetime64(0, 's'))

# pandas
import pandas as pd
print("Pandas:", pd.to_datetime(0))
```

::: {#tip-aware .callout-tip collapse="true"}
## Der kleine Unterschied
Das Modul datetime verwendet die Zeitzone Ihres Computers und gibt deshalb den Zeitpunkt der Epoche zur ersten Stunde des Tages zurück (zumindest, wenn auf Ihrem System die zentraleuropäische Zeit UTC+1 eingestellt ist.). Um den gleichen Rückgabewert wie für die anderen Module zu erhalten, muss die Zeitzone angegeben werden.
``` {python}

print(datetime.datetime.fromtimestamp(0))
print(datetime.datetime.fromtimestamp(0, tz = datetime.timezone.utc))
```

:::

### Naive und bewusste datetime-Objekte
Datetime-Objekte werden abhängig davon, ob sie Informationen über Zeitzonen enthalten, als naiv (naive) oder als bewusst (aware) bezeichnet. Naiven Datetime-Objekten fehlt diese Information, bewusste Datetime-Objekte enthalten diese. Objekte der Module time, datetime und pandas verfügen über ein Zeitzonenattribut, sind also bewusst. `np.datetime64` ist seit NumPy-Version 1.11.0 ein naiver Datentyp, unterstützt aber Zeitzonen aus Gründen der Rückwärtskompatibilität.

:::
Deprecated since version 1.11.0: NumPy does not store timezone information. For backwards compatibility, datetime64 still parses timezone offsets, which it handles by converting to UTC±00:00 (Zulu time). This behaviour is deprecated and will raise an error in the future.  
<https://numpy.org/doc/stable/reference/arrays.datetime.html>
:::

Pandas kann das wohl: https://pandas.pydata.org/docs/reference/api/pandas.DatetimeIndex.tz_localize.html
datetime and time objects have an optional time zone information attribute, tzinfo, that can be set to an instance of a subclass of the abstract tzinfo class. (https://docs.python.org/3/library/datetime.html#) **was mega-kompliziert ist - siehe Hinweis unten --> den Hinweis eventuell hierhier verschieben.** `print(pd.to_datetime(0).timetz())` **so ist tz auf GMT**

