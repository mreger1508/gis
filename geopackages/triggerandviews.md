# Trigger & Views in Geopackages
Da Geopackages auf SQLite basieren, ist es möglich, Trigger und Views in Geopackages zu erstellen - ähnlich wie in PostGIS und PostgreSQL.
Es gibt allerdings einige Besonderheiten, die dabei zu beachten sind. Im folgenden werden die nötigen Schritte beschrieben.<br>
## Geopackage öffnen
Über die QGIS-DB-Verwaltung kann man sich mit einem Geopackage verbinden. Dazu muss man zuerst im Browser-Fenster nach dem gewünschten Geopackage suchen und mit einem Rechtsklick aus dem Menü **Verbindung hinzufügen** auswählen:<br>
<img src="./data/gpkg_connection.png" width="300"><br>
Danach ist das Geopackage unter **GeoPackage** im Browserfenster mit den registrierten Tabellen sichtbar:<br>
<img src="./data/gpkg_browser.png" width="300"><br>
Nun kann man über die DB-Verwaltung in QGIS über die markierte Schaltfläche oben links ein SQL-Abfragefenster zum ausgewählten Geopackage öffnen<br>
<img src="./data/gpkg_db_manager.png" width="700"><br>
Im SQL-Abfragefenster können nun beliebige Abfragen, die mit SQLite kompatibel sind, ausgeführt werden. Im Gegensatz zu PostgreSQL/PostGIS muss kein Schema vor den Tabellennamen. Bitte beachten, dass die Syntax zwar ähnlich zu PostgreSQL/PostGIS ist, aber nicht alle Funktionalitäten komplett identisch sind. Die Ausgabe wird unter dem Fenster der SQL-Eingabe angezeigt.<br>
<img src="./data/gpkg_query.png" width="700"><br>
## Erstellung von Views (oder Tabellen)
Eine View in einem Geopackage zu erstellen funktioniert grundsätzlich ähnlich wie in PostgreSQL/PostGIS:<br>
`CREATE VIEW viewname AS ...gewünschtes SQL-Statement (z.B. SELECT * FROM ...)...`<br>
Der Unterschied beim Geopackge ist allerdings, dass die View erst im Geopackage registriert werden muss, damit sie in QGIS sichtbar wird. Das muss in der Tabelle **gpkg_contents** passieren. Um die Eintragung vorzunehmen, kann folgendes SQL-Statement ausgeführt werden:<br>
### Wenn es sich um eine geometrielose View oder Tabelle handelt:
An dieser Stelle muss **attributes** als Data-Type angegeben werden, aber keine **srs_id**, da es kein Koordinatensystem braucht, wenn keine Geometrie vorliegt.
```
insert into gpkg_contents (table_name, data_type, identifier, srs_id)
values ('viewname', 'attributes', 'viewname', 0)
```
### Wenn es sich um eine View oder Tabelle mit Geometrie handelt:
Hier muss **features** als Data-Type angegeben werden und außerdem die ID des Koordinatensystems (z.B. 25832).
```
insert into gpkg_contents (table_name, data_type, identifier, srs_id)
values ('viewname', 'features', 'viewname', 25832)
```
Außerdem muss die Geometriespalte in **gpkg_geometry_columns** angemeldet werden:
```
insert into gpkg_geometry_columns (table_name, column_name, geometry_type_name, srs_id, z, m)
  values ('viewname','geom','GEOMETRY',25832,0,0)
```
**GEOMETRY** muss durch den passenden Geometrietypen ersetzt werden (z.B. (Multi-)Point, (Multi-)Linestring, (Multi-)Polygon). Das SRS kann auch ein anderes sein als 25832.
Zustäzlich dazu muss noch der Feature Count bei der Anlage neuer Tabellen angelegt werden (gilt nicht für Views):
```
insert into gpkg_ogr_contents (table_name, feature_count)
values('tablename', (select count(fid) from tablename))
```
In allen Code-Snippets muss **viewname** oder **tablename** durch den entsprechenden Namen der View oder der Tabelle ersetzt werden.
## Löschen von Views (oder Tabellen)
Um Views (oder Tabellen) aus dem Geopackage zu entfernen, eignet sich folgendes SQL-Statement:<br>
```
DROP VIEW viewname
```
Dadurch verschwindet allerdings noch nicht der Eintrag in **gpkg_contents**. Um das zu erreichen, eignet sich folgendes SQL-Statement:<br>
```
DELETE FROM gpkg_contents WHERE table_name = viewname
```
**viewname** ist jeweils durch den Namen der View (oder Tabelle) zu ersetzen.
## Trigger erstellen
Um automatische Änderungen beim Einfügen, Löschen oder Updaten von Features im Geopackage einzurichten, können Trigger erstellt werden. Das kann durch das folgende SQL-Statement erfolgen:
```
CREATE TRIGGER triggername
AFTER UPDATE OF spaltenname ON tabellenname
BEGIN
  UPDATE tabellenname
  SET spaltenname = gewünschter Wert
END; 
```
Mit dem gezeigten Code kann ein Trigger erstellt werden, der beim Updaten von Features in Kraft tritt. Soll der Trigger beim Löschen ausgelöst werden, muss **AFTER UPDATE** durch **AFTER DELETE** ersetzt werden. Soll der Trigger beim Einfügen neuer Features ausgelöst werden, dann muss **AFTER UPDATE** durch **AFTER INSERT** ersetzt werden.<br>
Der Trigger muss nicht in **gpkg_contents** registriert werden.
## Trigger löschen
Der Trigger kann durch folgendes Statement wieder entfernt werden:
```
DROP TRIGGER triggername;
```
## zusätzliche Infos
- Geopackages können auch mit anderer Software bearbeitet werden (z.B. SQLite Studio)
- 
