# Comdirect Postbox Downloader

Lädt Dokumente aus der comdirect-Postbox herunter.

Benötigt wird mindestens **Python 3.10**. Das Tool wird per Kommandozeile gestartet und bedient. Es ist weiterhin erforderlich, eine `settings.ini` aus `settings.ini.example` anzulegen.

Es werden sowohl das Photo-PushTAN- als auch Mobile-TAN-Verfahren unterstützt. Das klassische PhotoTAN-Verfahren ist implementiert, aber noch nicht getestet.

# Setup

Im Verzeichnis einmalig ausführen:

```powershell
python -m pip install -Ur requirements.txt
```

Danach `settings.ini` konfigurieren und `main.py` starten:

```powershell
python main.py
```

## Zugangsdaten und Secrets

`user` und `clientId` dürfen in `settings.ini` stehen. `pwd` und `clientSecret` sollten dort nicht dauerhaft gespeichert werden.

Wenn `pwd` oder `clientSecret` in `settings.ini` fehlen, liest das Tool sie aus dem konfigurierten Secret-Backend. Fehlen sie auch dort, werden sie beim Start per Passwortabfrage abgefragt, gespeichert und im aktuellen Programmlauf verwendet.

- **Windows**: Bei `secretBackend=auto` werden Secrets via `keyring` im Windows Credential Manager gespeichert.
- **Linux / Raspberry Pi**: Bei `secretBackend=auto` werden Secrets in einer lokalen `.env` im Projektverzeichnis gespeichert.
- **secretBackend**: `auto`, `keyring` oder `env`.
- **secretNamespace**: Service- oder Namensraum für das Secret-Backend, z. B. `Banking/comdirect`.

`settings.ini` und `.env` enthalten lokale Daten und dürfen nicht committet werden.

## Settings

Siehe `settings.ini.example` als Beispieldatei. Wichtige Optionen:

- **outputDir**: Zielverzeichnis für heruntergeladene Dokumente.
- **dryRun**: Bei `True` wird nichts heruntergeladen.
- **appendIfNameExists**: Bei Namenskollisionen wird zuerst das Dokumentdatum an den Dateinamen angehängt.
- **useSubFolders**: Bestehende technische Sortierung nach MIME-Typ, z. B. `pdf` oder `html`.
- **useThematicSubFolders**: Thematische Sortierung nach comdirect-Dokumenttyp.
- **incrementalSync**: Erkennt lokal vorhandene Dokumente und überspringt sie beim Download.
- **downloadOnlyFilenames**: Lädt nur Dokumente herunter, deren Dokumenttyp in `downloadOnlyFilenamesArray` enthalten ist.
- **downloadOnlyFilenamesArray**: Liste der comdirect-Dokumenttypen. Unterstützt werden das bisherige Set-Format und CSV.
- **downloadSource**: `archivedOnly`, `notArchivedOnly` oder `all`.

`useSubFolders` und `useThematicSubFolders` sind unabhängig:

- `useSubFolders=True` sortiert technisch nach MIME-Typ: `pdf` / `html`.
- `useThematicSubFolders=True` sortiert thematisch nach comdirect-Dokumenttyp.
- Wenn beide aktiv sind, wird zuerst der Dokumenttyp-Ordner und darunter optional `pdf` / `html` verwendet.

Die thematischen Ordnernamen werden nicht pluralisiert. Sie entsprechen exakt den Dokumenttypen, z. B.:

```ini
outputDir=U:\\MyMoney\\Banking\\comdirect\\
```

Beispielausgabe bei `useThematicSubFolders=True`:

```text
U:\MyMoney\Banking\comdirect\Finanzreport\
U:\MyMoney\Banking\comdirect\Jahressteuerbescheinigung\
U:\MyMoney\Banking\comdirect\Wertpapierabrechnung\
U:\MyMoney\Banking\comdirect\Steuermitteilung\
U:\MyMoney\Banking\comdirect\Gutschrift\
U:\MyMoney\Banking\comdirect\Dividendengutschrift\
U:\MyMoney\Banking\comdirect\Ertragsgutschrift\
U:\MyMoney\Banking\comdirect\Kosteninformation\
```

Bei `useThematicSubFolders=True` und `useSubFolders=True` landet ein PDF-Finanzreport z. B. unter:

```text
U:\MyMoney\Banking\comdirect\Finanzreport\pdf\
```

## Inkrementeller Sync

Bei `incrementalSync=True` prüft das Tool vor dem Download, ob ein Dokument bereits lokal vorhanden ist. Dafür wird dieselbe Zielpfad-Logik verwendet wie beim Download. Zusätzlich werden ältere Ablagevarianten ohne thematischen Ordner sowie mit und ohne `pdf`-/`html`-Unterordner geprüft.

Menüpunkt 3 zeigt dadurch an, wie viele Online-Dokumente durch die Filter ausgewählt, bereits lokal vorhanden oder noch fehlend sind. Menüpunkt 4 lädt nur fehlende Dokumente herunter.

## Transparenz

Dieses Fork wurde bei den Erweiterungen für Secret-Handling, thematische Unterordner und inkrementellen Sync mit Unterstützung von OpenAI Codex bearbeitet. Die Änderungen wurden dabei gezielt klein gehalten und an der bestehenden Projektstruktur ausgerichtet.

## Verwendet

- Python 3.10+
- pathvalidate
- pillow
- requests
- rich
- keyring
