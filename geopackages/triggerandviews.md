# Trigger & Views in Geopackages
Da Geopackages auf SQLite basieren, ist es möglich, Trigger und Views in Geopackages zu erstellen - ähnlich wie in PostGIS und PostgreSQL.
Es gibt allerdings einige Besonderheiten, die dabei zu beachten sind. Im folgenden werden die nötigen Schritte beschrieben.
## Geopackage öffnen
Über die QGIS-DB-Verwaltung kann man sich mit einem Geopackage verbinden. Dazu muss man zuerst im Browser-Fenster nach dem gewünschten Geopackage suchen und mit einem Rechtsklick aus dem Menü **Verbindung hinzufügen** auswählen:<br>
<img src="./data/gpkg_connection.png" width="300"><br>
Danach ist das Geopackage unter **GeoPackage** im Browserfenster mit den registrierten Tabellen sichtbar:<br>
<img src="./data/gpkg_browser.png" width="300"><br>
Nun kann man über die DB-Verwaltung in QGIS über die markierte Schaltfläche oben links ein SQL-Abfragefenster zum ausgewählten Geopackage öffnen<br>
<img src="./data/gpkg_db_manager.png" width="700"><br>
Im SQL-Abfragefenster können nun beliebige Abfragen, die mit SQLite kompatibel sind, ausgeführt werden. Im Gegensatz zu PostgreSQL/PostGIS muss kein Schema vor den Tabellennamen. Bitte beachten, dass die Syntax zwar ähnlich zu PostgreSQL/PostGIS ist, aber nicht alle Funktionalitäten komplett identisch sind.
<img src="./data/gpkg_db_manager.png" width="700"><br>
