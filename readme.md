# ![](./SRC/CLIENT_DATA/images/winscp_logo_small.png "WinSCP") WinSCP #

## ToC ##

* [Paketinfo](#paketinfo)
* [Paket erstellen](#paket_erstellen)
  * [Makefile und spec.json](#makefile_und_spec)
  * [Mustache](#mustache)
  * [Verzeichnisstruktur](#verzeichnisstruktur)
  * [Makefile-Parameter](#makefile_parameter)
  * [spec.json](#spec_json)
* [Installation](#installation)
* [Properties](#properties)
* [Allgemeines](#allgemeines)
  * [Aufbau des Paketes](#aufbau_des_paketes)
  * [Nomenklatur](#nomenklatur)
  * [Unattended-Switches](#unattended_switches)
* [Lizenzen](#lizenzen)
  * [Dieses Paket](#lic_paket)
  * [WinSCP](#lic_winscp)
  * [psDetail](#lic_psdetail)
  * [GetRealName](#lic_getrealname)
* [Anmerkungen/ToDo](#anmerkungen_todo)


<div id="paketinfo"></div>

Diese OPSI-Paket fuer **WinSCP** wurde fuer die Repositories vom OPSI4Institutes (*O4I*) und
des *Max-Planck-Instituts fuer Mikrostrukturphysik* erstellt.  
Die Erstellung der verschiedenen Pakete aus den Quellen erfolgt durch ein
einfaches *Makefile*.


<div id="paket_erstellen"></div>

## Paket erstellen ##

Dieser Abschnitt beschaeftigt sich mit der Erstellung des OPSI-Paketes aus
dem Source-Paket und nicht mit dem OPSI-Paket selbst.


<div id="makefile_und_spec"></div>

### Makefile und spec.json ###

Da aus den Quellen verschiedene Versionen des Paketes mit entsprechenden Anpassungen
generiert werden sollen (mpimsp, o4i; testing/release) wurde hierfuer ein
**<code>Makefile</code>** erstellt. Darueber hinaus steuert **<code>spec.json</code>** 
die Erstellung der Pakete.

Im Idealfall ist beim Erscheinen einer neuen Release des *WinSCP* lediglich die
**<code>spec.json</code>** anzupassen.

Dieses Paket unterstuetzt prinzipiell die Software-Installation und -Deinstallation
sowohl fuer **MSI**- als auch fuer **INNO**-Setup. Der Installer fier WinSCP
steht jedoch ausschliesslich als INNO-Paket zur Verfuegung, weshalb der MSI-Code
hier nicht zum Zuge kommt

<div id="pystache"></div>

<div id="mustache"></div>

### Mustache ###

Als Template-Engine kommt **Mustache** zum Einsatz.  
Im Detail wird hier eine Go-Implementierung verwendet. Die Software ist auf 
[Github](https://github.com/cbroglie/mustache) zu finden. Binaries 
für Linux und Windows liegen diesem Paket bei.

Das in vorherigen Versionen dieses Paketes (<11) verwendete `pystache` kommt
nicht mehr zum Einsatz und wurde aus den Quellen entfernt.


<div id="verzeichnisstruktur"></div>

### Verzeichnisstruktur ###

Die erstellten Pakete werden im Unterverzeichnis **<code>BUILD</code>** abgelegt.

Einige Files (derzeit <code>control, preinst, postinst</code>) werden bei der Erstellung erst aus _<code>.in</code>_-Files
generiert, welche sich in den Verzeichnissen <code>SRC/OPSI</code> und <code>SRC/CLIENT_DATA</code> befinden.
Die <code>SRC</code>-Verzeichnisse sind in den OPSI-Paketen nicht mehr enthalten.


<div id="makefile_parameter"></div>

### Makefile-Parameter ###
Der vorliegende Code erlaubt die Erstellung von OPSI-Paketen fuer die Releases
gemaess der Angaben in <code>spec.json</code>. Es kann jedoch bei der Paketerstellung
ein alternatives Spec--File uebergeben werden:

> *<code>SPEC=&lt;spec_file&gt;</code>*

Das Paket kann mit *"batteries included"* erstellt werden. In dem Fall erfolgt 
der Download der Software beim Erstellen des OPSI-Paketes und nicht erst bei
dessen Installation:

> *<code>ALLINC=&lt;true|false&gt;</code>*

Standard ist hier die Erstellung des vollstaendigen Paketes (```ALLINC=true```).

Bei der Installation des Paketes im Depot wird ein eventuell vorhandenes 
```files```-Verzeichnis zunaechst gesichert und vom ```postinst```-Skript
spaeter wiederhergestellt. Diese Verzeichnis beeinhaltet die eigentlichen
Installationsfiles. Sollen alte Version aufgehoben werden, kann das ueber
einen Parameter beeinflusst werden:

> *<code>KEEPFILES=&lt;true|false&gt;</code>*

Standardmaessig sollen die Files geloescht werden.

OPSI erlaubt des Pakete im Format <code>cpio</code> und <code>tar</code> zu erstellen.  
Als Standard ist <code>cpio</code> festgelegt.  
Das Makefile erlaubt die Wahl des Formates ueber die Umgebungsvariable bzw. den Parameter:

> *<code>ARCHIVE_FORMAT=&lt;cpio|tar&gt;</code>*


<div id="spec_json"></div>

### spec.json ###

Haeufig beschraenkt sich die Aktualisierung eines Paketes auf das Aendern der 
Versionsnummern und des Datums etc. In einigen Faellen ist jedoch auch das Anpassen
weiterer Variablen erforderlich, die sich auf verschiedene Files verteilen.  
Auch das soll durch das Makefile vereinfacht werden. Die relevanten Variablen
sollen nur noch in <code>spec.json</code> angepasst werden. Den Rest uebernimmt *<code>make</code>*


<div id="installation"></div>

## Installation ##

Die Software selbst wird - sofern bei der Paketerstellung nicht anders vorgegeben - 
mit diesem Paket vertrieben (*"batteries included"*).

Fuer `~dl~-Pakete ist die Software nicht enthalten. Fuer diese Pakete erfolgt
der Download bei der Installation im Depot durch das postinst-Script vom Hersteller.
Ein manueller Download ist dann nicht erforderlich. Auf dem Depot-Server ist 
hierfuer `curl` bzw. `wget` erforderlich.


<div id="properties"></div>

## Properties ##

Die Installation der Software laesst sich ueber eine Reihe von Properties beeinflussen:

| Property                            | Default         | Description               |
|-------------------------------------|-----------------|--------------------------------------------------------------------------------------|
| `custom_post_install`               | "none"          | Define filename for include script in custom directory after installation |
| `custom_post_uninstall`             | "none"          | Define filename for include script in custom directory after deinstallation |
| `kill_running`                      | False           | kill running instance (for software on_demand); verfuegbar wenn in spec.json aktiviert |
| `install_translations`              | True            | Install translation files; requires ~55-60 MB disk space |
| `configure_user_settings`           | False           | Modify registry configuration for all users |
| `config_updates_period`             | "never"         | Update check period (requires user_settgins = true to modify existing user configurations) |
| `config_interface_style`            | "do not change" | Interface style (*commander* or *explorer*; requires user_settgins = true to modify existing user configurations) |
| `config_interface_theme`            | "do not change" | Configure interface theme (*auto*, *light*; or *dark*, requires user_settgins = true) |
| `link_desktop`                      | False           | generate or delete desktop link |
| `log_level`                         | "default"       | Loglevel for this package |
| `silent_option`                     | "very silent"   | Show (silent) or hide (very silent) progressbar of (un)installer |


<div id="allgemeines"></div>

## Allgemeines ##

<div id="aufbau_des_paketes"></div>

### Aufbau des Paketes ###
* **<code>variables.opsiinc</code>** - Da Variablen ueber die Scripte hinweg mehrfach
verwendet werden, werden diese (bis auf wenige Ausnahmen) zusammengefasst hier deklariert.
* **<code>product_variables.opsiinc</code>** - die producktspezifischen Variablen werden
hier definiert
* **<code>setup.opsiscript </code>** - Das Script fuer die Installation.
* **<code>uninstall.opsiscript</code>** - Das Uninstall-Script
* **<code>delsub.opsiinc</code>**- Wird von Setup und Uninstall gemeinsam verwendet.
Vor jeder Installation/jedem Update wird eine alte Version entfernt. (Ein explizites
Update-Script existiert derzeit nicht.)
* **<code>checkinstance.opsiinc</code>** - Pruefung, ob eine Instanz der Software laeuft.
Gegebenenfalls wird das Setup abgebrochen. Optional kann eine laufende Instanz 
zwangsweise beendet werden.
* **<code>checkvars.sh</code>** - Hilfsscript fuer die Entwicklung zur Ueberpruefung,
ob alle verwendeten Variablen deklariert sind bzw. nicht verwendete Variablen
aufzuspueren.
* **<code>bin/</code>** - Hilfprogramme; hier: **psdetail**, **GetRealName**.
* **<code>images/</code>** - Programmbilder fuer OPSI

<div id="nomenklatur"></div>

### Nomenklatur ###
Praefixes in der Produkt-Id definieren die Art des Paketes:

* **0_**, **test_** - Es handelt sich um ein Test-Paket. Beim Uebergang zur Produktions-Release
wird der Praefix entfernt.
* **o4i_** - Das Paket ist zur Verwendung im Opsi4Institutes-Repository vorgesehen.

Die Reihenfolge der Praefixes ist relevant; die Markierung als Testpaket ist 
stets fuehrend.

<div id="unattended_switches"></div>

### Unattended-Switches ###
Es handelt sich um ein *INNO*-Setup-Paket mit den hier gebraeuchlichen Parametern.

siehe auch: http://www.jrsoftware.org/isinfo.php


<div id="lizenzen"></div>

## Lizenzen ##

<div id="lic_paket"></div>

###  Dieses Paket ###

Dieses OPSI-Paket steht unter der *GNU General Public License* **GPLv3**.

Ausgenommen von dieser Lizenz sind die unter **<code>bin/</code>** zu findenden
Hilfsprogramme. Diese unterliegen ihren jeweiligen Lizenzen.

<div id="lic_winscp"></div>

### WinSCP ###
Die Software und das verwendete Logo stehen unter der GPLv3.

Die Variationen fuer das OPSI-Paket wurden von mir unter Verwendung weiterer
freier Grafiken erstellt

<div id="lic_psdetail"></div>

### psdetail ###
**Autor** der Software: Jens Boettge <<boettge@mpi-halle.mpg.de>> 

Die Software **psdetail.exe**  wird als Freeware kostenlos angeboten und darf fuer 
nichtkommerzielle sowie kommerzielle Zwecke genutzt werden. Die Software
darf nicht veraendert werden; es duerfen keine abgeleiteten Versionen daraus 
erstellt werden.

Es ist erlaubt Kopien der Software herzustellen und weiterzugeben, solange 
Vervielfaeltigung und Weitergabe nicht auf Gewinnerwirtschaftung oder Spendensammlung
abzielt.

Haftungsausschluss:  
Der Auto lehnt ausdruecklich jede Haftung fuer eventuell durch die Nutzung 
der Software entstandene Schaeden ab.  
Es werden keine ex- oder impliziten Zusagen gemacht oder Garantien bezueglich
der Eigenschaften, des Funktionsumfanges oder Fehlerfreiheit gegeben.  
Alle Risiken des Softwareeinsatzes liegen beim Nutzer.

Der Autor behaelt sich eine Anpassung bzw. weitere Ausformulierung der Lizenzbedingungen
vor.

Fuer die Nutzung wird das *.NET Framework ab v3.5*  benoetigt.

<div id="lic_getrealname"></div>

### GetRealName ###
**Autor** der Software: Jens Boettge <<boettge@mpi-halle.mpg.de>> 

Die Software **GetRealName.exe**  wird als Freeware kostenlos angeboten und darf fuer 
nichtkommerzielle sowie kommerzielle Zwecke genutzt werden. Die Software
darf nicht veraendert werden; es duerfen keine abgeleiteten Versionen daraus 
erstellt werden.

Es ist erlaubt Kopien der Software herzustellen und weiterzugeben, solange 
Vervielfaeltigung und Weitergabe nicht auf Gewinnerwirtschaftung oder Spendensammlung
abzielt.

Haftungsausschluss:  
Der Auto lehnt ausdruecklich jede Haftung fuer eventuell durch die Nutzung 
der Software entstandene Schaeden ab.  
Es werden keine ex- oder impliziten Zusagen gemacht oder Garantien bezueglich
der Eigenschaften, des Funktionsumfanges oder Fehlerfreiheit gegeben.  
Alle Risiken des Softwareeinsatzes liegen beim Nutzer.

<div id="anmerkungen_todo"></div>

## Anmerkungen/ToDo ##
[...]

-----
Jens Boettge <<boettge@mpi-halle.mpg.de>>, 2021-10-22 10:26:03 +0200
