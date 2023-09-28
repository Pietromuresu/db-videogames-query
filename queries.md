### Query
#### SELECT

1. Selezionare tutte le software house americane (3)
```
SELECT * 
FROM software_houses sh 
WHERE sh.country LIKE 'United States';
```
2. Selezionare tutti i giocatori della cittÃ  di 'Rogahnland' (2)
```
SELECT * 
FROM players p 
WHERE p.city  LIKE 'Rogahnland';
```
3. Selezionare tutti i giocatori il cui nome finisce per "a" (220)
```
SELECT * 
FROM players p 
WHERE p.name  LIKE '%a';
```
4. Selezionare tutte le recensioni scritte dal giocatore con ID = 800 (11)
```
SELECT * 
FROM  reviews r 
WHERE r.player_id  = 800;
```
5. Contare quanti tornei ci sono stati nell'anno 2015 (9)
```
SELECT COUNT(*)  as n_tournaments
FROM  tournaments t 
WHERE t.`year` = 2015;
```
6. Selezionare tutti i premi che contengono nella descrizione la parola 'facere' (2)
```
SELECT *
FROM  awards a 
WHERE a.description  LIKE '%facere%';

```
7. Selezionare tutti i videogame che hanno la categoria 2 (FPS) o 6 (RPG), mostrandoli una sola volta (del videogioco vogliamo solo l'ID) (287)
```
SELECT  DISTINCT cv.videogame_id 
FROM category_videogame cv 
WHERE cv.category_id  = 2 
		OR cv.category_id  = 6;
```
8. Selezionare tutte le recensioni con voto compreso tra 2 e 4 (2947)
```
SELECT *
FROM reviews r 
WHERE r.rating >= 2 
  AND r.rating  <= 4;
```
9. Selezionare tutti i dati dei videogiochi rilasciati nell'anno 2020 (46)
```
SELECT * 
FROM videogames v 
WHERE YEAR(v.release_date) = 2020;
```
10. Selezionare gli id dei videogame che hanno ricevuto almeno una recensione da 5 stelle, mostrandoli una sola volta (443)
```
SELECT DISTINCT r.videogame_id 
FROM reviews r 
WHERE rating = 5;
```

##### BONUS
11. Selezionare il numero e la media delle recensioni per il videogioco con ID = 412 (review number = 12, avg_rating = 3.16 circa)
```
SELECT COUNT(*) as review_number, AVG(r.rating) as avg_rating 
FROM reviews r 
WHERE r.videogame_id = 412;
```
12. Selezionare il numero di videogame che la software house con ID = 1 ha rilasciato nel 2018 (13)
```
SELECT COUNT(*)
FROM videogames v 
WHERE software_house_id = 1
	AND YEAR(release_date) = 2018;
```

#### GROUP BY
1. Contare quante software house ci sono per ogni paese (3)
```
SELECT sh.country, COUNT(*) 
FROM software_houses sh 
GROUP BY sh.country ;
```
2. Contare quante recensioni ha ricevuto ogni videogioco (del videogioco vogliamo solo l'ID) (500)
```
SELECT r.videogame_id, COUNT(*)
FROM reviews r 
GROUP BY r.videogame_id ;
```
3. Contare quanti videogiochi hanno ciascuna classificazione PEGI (della classificazione PEGI vogliamo solo l'ID) (13)
```
SELECT plv.pegi_label_id, COUNT(*)
FROM pegi_label_videogame plv 
GROUP BY plv.pegi_label_id  ;
```
4. Mostrare il numero di videogiochi rilasciati ogni anno (11)
```
SELECT YEAR(v.release_date), COUNT(*)
FROM videogames v 
GROUP BY YEAR(v.release_date);

```
5. Contare quanti videogiochi sono disponbiili per ciascun device (del device vogliamo solo l'ID) (7)
```
SELECT dv.device_id,COUNT(*)
FROM device_videogame dv 
GROUP BY dv.device_id ;
```
6. Ordinare i videogame in base alla media delle recensioni (del videogioco vogliamo solo l'ID) (500)
```
SELECT r.videogame_id, AVG(r.rating) 
FROM reviews r 
GROUP BY r.videogame_id 
ORDER BY AVG(r.rating) DESC;
```

#### JOIN
1. Selezionare i dati di tutti giocatori che hanno scritto almeno una recensione, mostrandoli una sola volta (996)
```
SELECT DISTINCT p.name, p.lastname, p.nickname 
FROM reviews r 
JOIN players p
	ON r.player_id = p.id;
```
2. Sezionare tutti i videogame dei tornei tenuti nel 2016, mostrandoli una sola volta (226)
```
SELECT DISTINCT v.name 
FROM tournaments t 
JOIN tournament_videogame tv 
	ON t.id = tv.tournament_id 
JOIN videogames v 
	ON tv.videogame_id = v.id 
WHERE t.year  = 2016;
```
3. Mostrare le categorie di ogni videogioco (1718)
```
SELECT v.id, v.name, c.name 
FROM videogames v 
JOIN category_videogame cv 
	ON cv.videogame_id = v.id 
JOIN categories c
	ON c.id = cv.category_id 
ORDER BY v.id;
```
4. Selezionare i dati di tutte le software house che hanno rilasciato almeno un gioco dopo il 2020, mostrandoli una sola volta (6)
```
SELECT DISTINCT sh.* 
FROM software_houses sh 
JOIN videogames v 
	ON sh.id = v.software_house_id 
WHERE YEAR(v.release_date) >= 2020;
```
5. Selezionare i premi ricevuti da ogni software house per i videogiochi che ha prodotto (55)
```
SELECT sh.name, v.name, a.name, av.year 
FROM awards a 
JOIN award_videogame av 
	ON a.id = av.award_id 
JOIN videogames v 
	ON av.videogame_id = v.id 
JOIN software_houses sh 
	ON v.software_house_id = sh.id
ORDER BY sh.id;
```
6. Selezionare categorie e classificazioni PEGI dei videogiochi che hanno ricevuto recensioni da 4 e 5 stelle, mostrandole una sola volta (3363)
```
SELECT DISTINCT v.id, v.name, pl.name, c.name 
FROM categories c 
JOIN category_videogame cv 
	ON c.id = cv.category_id 
JOIN videogames v 
	ON cv.videogame_id  = v.id 
JOIN pegi_label_videogame plv 
	ON v.id = plv.videogame_id 
JOIN pegi_labels pl 
	ON plv.pegi_label_id  = pl.id 
JOIN reviews r 
	ON v.id = r.videogame_id 
WHERE r.rating >= 4 AND r.rating <= 5;
```
7. Selezionare quali giochi erano presenti nei tornei nei quali hanno partecipato i giocatori il cui nome inizia per 'S' (474)
```
SELECT DISTINCT v.id, v.name 
FROM players p 
JOIN player_tournament pt 
	ON p.id = pt.player_id 
JOIN tournaments t 
	ON pt.tournament_id = t.id 
JOIN tournament_videogame tv 
	ON t.id  = tv.tournament_id 
JOIN videogames v 
	ON tv.videogame_id = v.id
WHERE p.name  LIKE 's%';
```
8. Selezionare le cittÃ  in cui Ã¨ stato giocato il gioco dell'anno del 2018 (36)
```
SELECT DISTINCT t.city 
FROM awards a 
JOIN award_videogame av 
	ON a.id = av.award_id 
JOIN videogames v 
	ON av.videogame_id = v.id 
JOIN tournament_videogame tv 
	On v.id = tv.videogame_id 
JOIN tournaments t 
	ON tv.tournament_id = t.id 
JOIN player_tournament pt 
	ON t.id = pt.tournament_id 
WHERE a.name LIKE "Gioco dell'anno"	
	AND av.`year` = 2018 ;
```
9. Selezionare i giocatori che hanno giocato al gioco piÃ¹ atteso del 2018 in un torneo del 2019 (3306)
```
SELECT p.name, p.lastname 
FROM players p 
JOIN player_tournament pt 
	ON p.id = pt.player_id 
JOIN tournaments t 
	ON pt.tournament_id = t.id 
JOIN tournament_videogame tv 
	ON t.id = tv.tournament_id 
JOIN videogames v 
	ON tv.videogame_id = v.id
JOIN award_videogame av 
	ON v.id = av.videogame_id 
JOIN awards a 
	ON av.award_id = a.id
WHERE a.name LIKE 'Gioco più atteso' 
	AND av.`year` = 2018
	AND t.`year` = 2019;
```

##### BONUS
10. Selezionare i dati della prima software house che ha rilasciato un gioco, assieme ai dati del gioco stesso (software house id : 5)
```
SELECT *
FROM software_houses sh 
JOIN videogames v ON sh.id = v.software_house_id 
WHERE v.release_date = (
	SELECT MIN(v2.release_date)
	FROM videogames v2
	);

```
11. Selezionare i dati del videogame (id, name, release_date, totale recensioni) con piÃ¹ recensioni (videogame id : potrebbe uscire 449 o 398, sono entrambi a 20)
```
SELECT v.id, v.name, COUNT(*) as co
FROM videogames v 
JOIN reviews r 
	ON v.id = r.videogame_id 
GROUP BY v.id 
ORDER BY co DESC
LIMIT 1;

```
12. Selezionare la software house che ha vinto piÃ¹ premi tra il 2015 e il 2016 (software house id : potrebbe uscire 3 o 1, sono entrambi a 3)
```

```
13. Selezionare le categorie dei videogame i quali hanno una media recensioni inferiore a 2 (10)
```

```