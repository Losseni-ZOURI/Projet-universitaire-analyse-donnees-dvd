# Projet universitaire analyse donnees dvd
Ce projet universitaire a pour objectif d’analyser les données d’un loueur de DVD afin d’aider à définir une stratégie d’implantation, de tarification et d’horaires d’ouverture.




# La partie SQL du projet a pour but d’extraire les indicateurs nécessaires au reporting Excel. Les requêtes sont regroupées en trois axes : catégories de films, vision clients et vision locations. Chaque requête est commentée pour faciliter la lecture et la réutilisation

## Catégories de films :
### Nombre de locations par catégorie :
select C.Name as "Categorie" ,count(rental_id) as "Nombre de locations"
    from Rental R , Inventory I , Film F , Film_Category FC  , Category C
    Where R.inventory_id = I.inventory_id
    AND I.film_id = F.film_id
    AND F.film_id = FC.film_id
    AND FC.Category_id =C.Category_id
        Group by C.Name

### Liste des films qui n’ont généré aucune location.

SELECT F.title  AS "Nom film",
    F.film_id AS "Id film"
FROM film F
LEFT JOIN inventory I
    ON F.film_id=I.film_id
LEFT JOIN rental R
    ON I.inventory_Id = R.inventory_id
WHERE R.rental_id IS NULL
Group by 1;

## Vision clients :
### Nombre de locations par client.

SELECT C.customer_Id"Id client",C.first_name As "Nom", C.last_name AS "Preom",
    COUNT(R.rental_id) AS "Nombre de location"
FROM Customer C
LEFT JOIN Rental R
    ON C.customer_id = R.customer_id
GROUP BY 1, 2, 3
ORDER BY 4 DESC;

### Montant total dépensé par client.
SELECT C.customer_Id,C.first_name As "Nom", C.last_name AS "Prenom",
    SUM(P.amount) AS "Montant total"
FROM Customer C
LEFT JOIN Payment P
    ON C.customer_id = P.customer_id
GROUP BY 1,2,3
ORDER BY 4 DESC;

### Date de la première location de chaque client.
SELECT C.customer_id as "Id client",C.first_name as"Nom",C.last_name AS "Prenom",
    MIN(R.rental_date) as "Premiere location"
    FROM customer C
        LEFT join rental R
         ON c.customer_id = r.customer_id
Group by 1,2,3
Order by 4;


## Vision locations :
### Nombre de locations par jour de la semaine
Select Count(rental_id)as "Nombre de location",
Case Strftime("%w",rental_date)
When "0" then "Dimanche"
When "1" then "Lundi"
When "2" then "Mardi"
When "3" then "Mercredi"
When "4" then "Jeudi"
When "5" then "Vendredi"
When "6" then "Samedi"
End as "Jour de la semaine"
From Rental
Group by 2
Order by 1;
