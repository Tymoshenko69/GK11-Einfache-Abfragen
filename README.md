# GK11 — Einfache Abfragen

**Fach:** Informationssysteme  
**Thema:** Relationale Datenbanken, einfache SQL-Abfragen (SELECT, ORDER BY, LIMIT, LIKE)

---

## 1. SQL Island

### Grundlegende Fragestellungen

**Was ist eine Datenbank und wo wird sie verwendet? Nenne Beispiele.**  
Eine Datenbank ist eine strukturierte, dauerhafte Sammlung von Daten, die effizient gespeichert, verwaltet und abgefragt werden kann. Beispiele: Kundendatenbanken von Webshops, Buchungssysteme von Airlines, Patientendaten in Krankenhäusern, Benutzerkonten sozialer Netzwerke.

**Was ist eine relationale Datenbank? Wodurch zeichnet sie sich aus?**  
Eine relationale Datenbank speichert Daten in Tabellen (Relationen), die aus Zeilen (Datensätzen) und Spalten (Attributen) bestehen. Tabellen können über Schlüssel (Primär- und Fremdschlüssel) miteinander in Beziehung gesetzt werden. Dadurch lassen sich Redundanzen vermeiden und Daten konsistent halten.

**Was ist ein Datenbankmanagementsystem (DBMS)?**  
Ein DBMS ist die Software, die den Zugriff auf eine Datenbank verwaltet — sie regelt Speicherung, Abfrage, Sicherheit, Mehrbenutzerzugriff und Datenkonsistenz. Beispiele: MySQL, MariaDB, PostgreSQL.

**Weitere Fachbegriffe**

- **Tabelle:** Sammlung gleichartiger Datensätze
- **Zeile / Datensatz (Tupel):** ein einzelner Eintrag in einer Tabelle
- **Spalte / Attribut:** eine Eigenschaft der Datensätze
- **Primärschlüssel:** eindeutige Kennung eines Datensatzes
- **Fremdschlüssel:** Verweis auf den Primärschlüssel einer anderen Tabelle
- **Schema:** Struktur/Aufbau einer Datenbank

**Wie werden zusammengehörige bzw. gleichartige Daten gespeichert?**  
Zusammengehörige Daten (z. B. alle Attribute eines Passagiers) stehen in einer **Zeile**. Gleichartige Daten (z. B. alle Nachnamen) stehen in einer **Spalte**.

**Warum sollte man statt einer Datenbank nicht Excel verwenden?**  
Datenbanken bieten Mehrbenutzerzugriff, hohe Datenmengen mit guter Performance, Datenintegrität/-konsistenz, eine mächtige Abfragesprache (SQL) und vermeiden Redundanz — Excel stößt hier schnell an Grenzen.

**Was ist SQL? Ist SQL case-sensitive? Wie werden Namen gekennzeichnet?**  
SQL (Structured Query Language) ist die Standardsprache zur Abfrage und Bearbeitung relationaler Datenbanken. SQL-**Befehle** sind nicht case-sensitive (werden aber zur besseren Lesbarkeit meist in CAPS geschrieben), **Tabellen-, Spaltennamen und Werte** können hingegen case-sensitive sein (abhängig vom System). Namen können mit Backticks (`` ` ``) gekennzeichnet werden, z. B. `` `planes` ``.

**Wie funktionieren die SQL-Befehle SELECT, DISTINCT, WHERE, AND, OR, NOT, BETWEEN, ORDER BY, LIKE, LIMIT?**

- **SELECT**: wählt anzuzeigende Spalten aus
- **DISTINCT**: entfernt doppelte Werte aus dem Ergebnis
- **WHERE**: filtert Zeilen nach einer Bedingung
- **AND / OR / NOT**: verknüpfen bzw. negieren Bedingungen
- **BETWEEN**: filtert Werte innerhalb eines Bereichs
- **ORDER BY**: sortiert das Ergebnis (aufsteigend `ASC` oder absteigend `DESC`)
- **LIKE**: filtert mit Mustern (`%` = beliebig viele Zeichen, `_` = ein Zeichen)
- **LIMIT**: begrenzt die Anzahl der zurückgegebenen Zeilen

**Quelle:** [SQL Island](https://sql-island.informatik.uni-kl.de)

---

## 2. flightdatabase

### Verwendete Tabellenstruktur

**Tabelle `passengers`:** `id`, `firstname`, `lastname`, `airline`, `flightnr`, `rownr`, `seatposition`

**Tabelle `planes`:** `id`, `manufacturer`, `type`, `lengthoverall`, `span`, `maxspeed`, `initialserviceyear`, `maxseats`, `seatsperrow`

Weitere Tabellen (`airlines`, `airports`, `countries`, `flights`) mit angenommener Struktur — siehe Hinweis am Ende dieses Abschnitts.

### Beispielabfragen (aus der Angabe)

```sql
-- Was ist das kürzeste SQL auf eine Tabelle? * steht als Platzhalter für alle Spalten
SELECT * FROM planes;

-- Statt dem * können auch nur bestimmte Spalten ausgegeben werden.
SELECT manufacturer, type FROM planes;

-- Ist SQL Case-Sensitive?
-- Befehle nicht, Tabellennamen, Spaltennamen, Werte schon
-- Befehle zur Übersichtlichkeit in CAPS
SELECT * FROM planes;

-- Wie können Werte gefiltert werden?
SELECT * FROM planes WHERE id >= 50;
SELECT * FROM planes WHERE manufacturer = 'Airbus';

-- Wie filtern wir leere Werte/Zellen?
SELECT * FROM `planes` WHERE maxspeed IS NULL;
SELECT * FROM `planes` WHERE maxspeed IS NOT NULL;

-- Alle Flugzeuge mit einer Maxspeed zwischen 400 und 500
SELECT * FROM `planes` WHERE maxspeed > 400 AND maxspeed < 500;
SELECT * FROM `planes` WHERE maxspeed BETWEEN 400 AND 500;

-- Alle Flugzeuge mit einer Maxspeed kleiner als 400 oder größer 500
SELECT * FROM `planes` WHERE maxspeed < 400 OR maxspeed > 500;

-- Alle Airbus oder Boeing Flugzeuge mit einer Maxspeed kleiner als 500
-- Berechnungen können im SELECT durchgeführt werden, AS benennt Spalten um
SELECT *, 500 - maxspeed AS Differenz
FROM `planes`
WHERE maxspeed < 500 AND (manufacturer = 'Airbus' OR manufacturer = 'Boeing');

-- Alle Flugzeuge von einem Hersteller, der mit B beginnt
SELECT * FROM planes WHERE manufacturer LIKE 'B%';

-- Wie viele Einträge hat diese Tabelle?
SELECT COUNT(*) FROM planes;
```

### Eigene Aufgabenstellungen

**Aufgabe 1:** Vorname und Nachname aller Passagiere auf Sitz „F", sortiert nach Nachname

```sql
SELECT firstname, lastname
FROM passengers
WHERE seatposition = 'F'
ORDER BY lastname;
```

**Aufgabe 2:** Flugzeuge, die nach 2000 auf den Markt gekommen sind und länger als 73 m sind

```sql
SELECT *
FROM planes
WHERE initialserviceyear > 2000 AND lengthoverall > 73;
```

**Aufgabe 3:** Flugzeuge, die breiter als lang sind

```sql
SELECT *
FROM planes
WHERE span > lengthoverall;
```

**Aufgabe 4:** Flugzeuge mit mehr als 30 Sitzreihen, inkl. Anzahl der Reihen über 30  
Da `planes` keine eigene Spalte für die Anzahl der Sitzreihen besitzt, wird diese aus `maxseats / seatsperrow` berechnet.

```sql
SELECT *, (maxseats / seatsperrow) - 30 AS ReihenUeber30
FROM planes
WHERE (maxseats / seatsperrow) > 30;
```

**Aufgabe 5:** Passagiere in Reihe 5–15 mit „e" im Vornamen

```sql
SELECT *
FROM passengers
WHERE rownr BETWEEN 5 AND 15
AND firstname LIKE '%e%';
```

---

## 3. Kreuzworträtsel

| Nr. | Frage                                                                | Ergebnis    |
| --- | -------------------------------------------------------------------- | ----------- |
| 1   | Nachname eines Passagiers der mit 'S' beginnt, Vorname 'Cadman'      | SHORT       |
| 2   | Vorname, ID zwischen 400–560, Nachname mit doppel R                  | BRANDEN     |
| 3   | Vorname, Ostfriesische Lufttransport, Sitz D                         | CRUZ        |
| 4   | Vorname, Nachname 'Hill', Sitz 'C'                                   | KELLY       |
| 5   | Vorname mit 3 Buchstaben, beginnend mit K                            | KIM         |
| 6   | Erster 'Whitaker' alphabetisch, Vorname                              | CECILIA     |
| 7   | Vorname beginnt mit X, Nachname endet mit y                          | XANDRA      |
| 8   | Kanadische Airline mit 'B' in der ID                                 | HAWKAIR     |
| 9   | Jamaikanische Airline                                                | AIR JAMAICA |
| 10  | ID einer japanischen Airline mit 'Asia' im Namen                     | EG          |
| 11  | Russischer Flughafen mit 4 'a' im Namen                              | CONSTANTINE |
| 12  | Erster österreichischer Flughafen (nach Code sortiert)               | ANT         |
| 13  | Flughafen in Salzburg                                                | W A MOZART  |
| 14  | Land mit x und b im Namen                                            | LUXEMBOURG  |
| 15  | Letztes Land mit z im Namen                                          | ZIMBABWE    |
| 16  | Stadt, in der der Flug am 25.7.2010 landete                          | YAROSLAVL   |
| 17  | Tschechische Airline, 2009 oder 2010 geflogen                        | QT          |
| 18  | Letzter Passagier (Luxair, alphabetisch nach Nachname), Vorname      | NIGEL       |
| 19  | Nachname, von Mena nach Reykholar Airport, Vorname 2 Zeichen         | MALONE      |
| 20  | ID des Passagiers mit doppel r und doppel t im Nachnamen, Sitz 10–20 | 1286        |
| 21  | Land, Flugnummer = Code für "Not Found" (404)                        | PF          |
|     | Lösungswort:                                                         | Steel       |

---

## Quellen

- SQL Island: [https://sql-island.informatik.uni-kl.de](https://sql-island.informatik.uni-kl.de)
- phpMyAdmin — flightdatabase (INSY_flightdata), Projekteserver NEU
