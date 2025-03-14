# db-gestore-eventi

DB Gestore Eventi
nome repo: db-gestore-eventi

Il nostro cliente vuole creare una  piattaforma per la gestione degli eventi che organizza.

Il gestionale permetterà agli utenti di gestire eventi  e vedere le prenotazioni a loro collegati. 
Ci saranno 4 tipi di utenti, con ruoli diversi: 
- chi può amministrate tutti gli utenti
- chi può accedere al gestionale per creare o modificare eventi
- chi può solo accedere al gestionale senza la possibilità di modifica dei dati
- chi non ha accesso al gestionale e può solo prenotarsi agli eventi

Ogni evento potrà avere più tag  per definirne la tipologia.
Il cliente organizza eventi solo in una serie di location esclusive che tramite il gestionale vuole tenere sempre sott’occhio, per capire quali eventi si terranno in ognuna di esse.

🎯 Obiettivo
Progettare il database per la piattaforma in questione, creando il diagramma ER  e poi svolgendo le query fornite.

1️⃣ Milestone 1 (Diagramma ER)
Create il diagramma ER. Pensiamo a quali entità (tabelle) creare per il nostro database, definiamo le colonne e i tipi di dato per ognuna di esse e infine stabiliamo le relazioni.


query da eseguire come esercitamento:

1) Selezionare tutti gli eventi gratis, cioè con prezzo nullo (19)

 SELECT * FROM `events` WHERE price IS NULL

2)Selezionare tutte le location in ordine alfabetico (82)

SELECT * FROM `locations` ORDER BY name ASC;

3)Selezionare tutti gli eventi che costano meno di 20 euro e durano meno di 3 ore (38)

SELECT * FROM `events` WHERE price < 20 AND duration < '03:00:00';

4)Selezionare tutti gli eventi di dicembre 2023 (25)

SELECT * FROM `events` WHERE start BETWEEN '2023-11-31' AND '2024-01-01';

5)Selezionare tutti gli eventi con una durata maggiore alle 2 ore (823)

SELECT * FROM events WHERE TIME_TO_SEC(duration) > 7200; 

SELECT * FROM events WHERE duration > '02:00:00';

6)Selezionare tutti gli eventi, mostrando nome, data di inizio, ora di inizio, ora di fine e
durata totale (1040)

SELECT name, start, duration, price FROM events;

7)Selezionare tutti gli eventi aggiunti da “Fabiano Lombardo” (id: 1202) (132)

SELECT * FROM `events` WHERE user_id = 1202;

8)Selezionare il numero totale di eventi per ogni fascia di prezzo (81)
SELECT 
    FLOOR(price) AS fascia_prezzo, 
    COUNT(*) AS numero_eventi
FROM events
GROUP BY fascia_prezzo
ORDER BY fascia_prezzo;

9)Selezionare tutti gli utenti admin ed editor (9)

SELECT * FROM `users` WHERE role_id IN (1,2);

10)Selezionare tutti i concerti (eventi con il tag “concerti”) (72)

SELECT * FROM `event_tag` WHERE tag_id IN(1);

11)Selezionare tutti i tag e il prezzo medio degli eventi a loro collegati (11)

SELECT 
    t.name AS tags, 
    AVG(e.price) AS prezzo_medio
FROM events e
JOIN event_tag et ON e.id = et.event_id
JOIN tags t ON et.tag_id = t.id
GROUP BY t.name;

12)Selezionare tutte le location e mostrare quanti eventi si sono tenute in ognuna di
esse (82)

SELECT l.name AS location, COUNT(e.id) AS numero_eventi FROM events e JOIN locations l ON e.location_id = l.id GROUP BY l.name;

13)Selezionare tutti i partecipanti per l’evento “Concerto Classico Serale” (slug:
concerto-classico-serale, id: 34) (30)

SELECT bookings.*
FROM bookings
WHERE bookings.event_id = 34;

14)Selezionare tutti i partecipanti all’evento “Festival Jazz Estivo” (slug:
festival-jazz-estivo, id: 2) specificando nome e cognome (13)

SELECT users.first_name, users.last_name FROM bookings JOIN users ON bookings.user_id = users.id WHERE bookings.event_id = 2;

15)Selezionare tutti gli eventi sold out (dove il totale delle prenotazioni è uguale ai
biglietti totali per l’evento) (18)

SELECT e.id, e.name, e.slug, e.total_tickets, COUNT(b.id) AS totale_prenotazioni
FROM events e
JOIN bookings b ON e.id = b.event_id
GROUP BY e.id, e.name, e.slug, e.total_tickets
HAVING COUNT(b.id) = e.total_tickets;

16)Selezionare tutte le location in ordine per chi ha ospitato più eventi (82)

SELECT locations.name AS locations, COUNT(events.id) AS numero_eventi FROM locations JOIN events ON locations.id = events.location_id GROUP BY locations.name ORDER BY numero_eventi DESC;

17)Selezionare tutti gli utenti che si sono prenotati a più di 70 eventi (74)

SELECT u.id, COUNT(b.id) AS numero_prenotazioni FROM users u JOIN bookings b ON u.id = b.user_id GROUP BY u.id HAVING COUNT(b.id) > 70;

18)Selezionare tutti gli eventi, mostrando il nome dell’evento, il nome della location, il
numero di prenotazioni e il totale di biglietti ancora disponibili per l’evento (1040)

SELECT 
    e.name AS evento,
    l.name AS location,
    COUNT(b.id) AS numero_prenotazioni,
    (e.total_tickets - COUNT(b.id)) AS biglietti_disponibili
FROM events e
JOIN locations l ON e.location_id = l.id
LEFT JOIN bookings b ON e.id = b.event_id
GROUP BY e.id, l.id
ORDER BY e.name;