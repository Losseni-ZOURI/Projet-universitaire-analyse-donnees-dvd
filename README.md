# Projet universitaire analyse de la base Sakila
Présentation du projet:

Ce projet s’appuie sur la base de données fictive Sakila, qui représente l’activité d’un loueur de DVD. L’objectif est d’exploiter ces données afin d’identifier des tendances utiles pour construire un reporting clair et exploitable dans Excel.

L’analyse porte sur trois axes principaux :
les catégories de films,

la vision clients,

la vision locations.

Le but est de mettre en évidence les profils clients les plus représentatifs, les périodes de location les plus actives et les catégories de films les plus demandées.

# Contexte

Avec le développement du streaming, les loueurs de DVD ont quasiment disparu.
L’idée étudiée ici est de créer un espace de location de DVD “comme autrefois”, où les clients pourraient choisir un film, s’installer dans une petite salle et profiter d’un moment convivial.

La base Sakila permet d’analyser l’activité d’un ancien loueur de DVD afin d’en tirer des pistes d’exploitation pertinentes.

Objectifs de l’analyse
L’analyse vise à répondre aux questions suivantes :

Quels sont les clients les plus représentatifs en nombre de locations ?

Quels clients dépensent le plus en location ?

À quelle date chaque client a-t-il effectué sa première location ?

Quels jours de la semaine enregistrent le plus de locations ?

Quelles sont les périodes de la journée les plus favorables à la location ?

Quelles catégories de films sont les plus louées ?

Quels films n’ont généré aucune location ?

Données utilisées
Les données proviennent de la base fictive Sakila.
Cette base contient notamment des informations sur :

les films,

les catégories de films,

les inventaires,

les locations,

les clients,

les paiements.

# Source des données : https://fxjollois.github.io/donnees/sakila/

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
