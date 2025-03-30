# Trigger & Views in Geopackages
Da Geopackages auf SQLite basieren, ist es möglich, Trigger und Views in Geopackages zu erstellen - ähnlich wie in PostGIS und PostgreSQL.
Es gibt allerdings einige Besonderheiten, die dabei zu beachten sind. Im folgenden werden die nötigen Schritte beschrieben.
## Geopackage öffnen
Über die QGIS-DB-Verwaltung kann man sich mit einem Geopackage verbinden. Dazu muss man zuerst im Browser-Fenster nach dem gewünschten Geopackage suchen und mit einem Rechtsklick aus dem Menü **Verbindung hinzufügen** auswählen:
![alt text](./data/gpkg_connection.png)
