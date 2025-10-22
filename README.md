# README — Exercices SQL (jeu de données e-commerce)

## 🧩 Description

Ce document contient :

- les **instructions pour lancer** la base de données et charger le jeu de données (`seed.sql`)
- la **liste des questions** auxquelles vous devrez répondre à l’aide de requêtes SQL

🎯 L’objectif : pratiquer les `SELECT`, filtres, `JOIN`, `GROUP BY`, agrégats, dates, sous-requêtes et fenêtres.

---

## 🧱 Prérequis

Avant de commencer, assurez-vous d’avoir :

- Docker & Docker Compose installés sur votre machine ;
- le fichier `docker-compose.yml` du projet, contenant un service MySQL nommé `mysql` (ou adaptez le nom du service si nécessaire) ;
- un fichier `seed.sql` contenant le jeu de données, placé à la racine du projet ;
- un fichier `.env` contenant les variables d’environnement (notamment `MYSQL_ROOT_PASSWORD`) si votre `docker-compose.yml` en dépend.

---

## 🧹 Reset de l'environnement Docker (projet)

### Arrêter et supprimer les conteneurs

```bash
docker compose down
```

### Supprimer les volumes

```bash
docker compose down -v
```

> ⚠️ Cela supprimera toutes les données de la base de données.

### Supprimer les images

```bash
docker compose down --rmi all
```

### Reset complet (conteneurs + volumes + images)

```bash
docker compose down -v --rmi all
```

## 🧹 Reset de l'environnement Docker (machine)

# 1. Arrêter TOUS les conteneurs Docker sur votre machine

```bash
docker stop $(docker ps -aq)
```

# 2. Supprimer TOUS les conteneurs

```bash
docker rm $(docker ps -aq)
```

# 3. Supprimer TOUS les volumes

```bash
docker volume rm $(docker volume ls -q)
```

# 4. Supprimer TOUTES les images

```bash
docker rmi $(docker images -q) --force
```

# OU utilisez la commande ultime qui fait tout en une fois :

```bash
docker system prune -a --volumes --force
```

La dernière commande docker system prune -a --volumes --force est
l'équivalent d'un reset total de Docker sans demander de confirmation.

⚠️ ATTENTION : Cela va supprimer TOUS vos conteneurs, volumes, images et
réseaux Docker, pas seulement ceux de ce projet !

## 🚀 Étapes pour lancer la base et injecter les données

1. Placez le fichier `seed.sql` à la racine du projet (même dossier que `docker-compose.yml`).

2. Démarrez les conteneurs :

   ```bash
   docker compose up -d
   ```

3. Chargez le jeu de données dans la base MySQL :

   ```bash
   # remplacez le nom du conteneur "mysql" s’il diffère dans votre docker-compose
   docker exec -i mysql mysql -uroot -psupersecret < seed.sql
   ```

4. Alternative — depuis le shell MySQL dans le conteneur :

   ```bash
   docker exec -it mysql mysql -uroot -p
   # puis dans le client mysql :
   SOURCE /chemin/vers/seed.sql;
   ```

5. Vérifiez que tout est bien chargé :

   ```bash
   docker exec -it mysql mysql -uroot -p"$MYSQL_ROOT_PASSWORD" -e "SHOW DATABASES; USE app_db; SHOW TABLES;"
   ```

---

## 📊 Visualiser le schéma sur dbdiagram.io

Pour mieux comprendre la structure de la base de données, vous pouvez visualiser le schéma de manière interactive sur [dbdiagram.io](https://dbdiagram.io).

### Méthode 1 : Copier-coller le code DBML

1. Rendez-vous sur [https://dbdiagram.io/d](https://dbdiagram.io/d)
2. Cliquez sur "New Diagram" ou "Import"
3. Sélectionnez "From DBML"
4. Copiez le contenu du fichier `schema.dbml` (disponible dans ce projet)
5. Collez-le dans l'éditeur de dbdiagram.io
6. Le schéma s'affichera automatiquement avec toutes les tables et relations

### Méthode 2 : Générer le DBML depuis MySQL

Si vous souhaitez générer le DBML directement depuis votre base de données :

```bash
# Installer sql2dbml (nécessite Node.js)
npm install -g sql2dbml

# Générer le DBML depuis MySQL
docker exec mysql mysqldump -uroot -p"$MYSQL_ROOT_PASSWORD" --no-data app_db > schema.sql
sql2dbml schema.sql --mysql > schema.dbml
```

### Ce que vous verrez

Le diagramme affichera :
- Les **7 tables** : `categories`, `products`, `customers`, `orders`, `order_items`, `payments`, `reviews`
- Les **relations** entre tables (clés étrangères)
- Les **types de données** et contraintes
- Une vue d'ensemble claire de l'architecture de la base de données

---

## 📘 Consignes

Pour **chaque question**, vous devez :

1. Écrire la **requête SQL complète** que vous avez utilisée.
2. Afficher le **résultat de la requête**.
3. Si le résultat contient **plus de 5 lignes**, affichez uniquement les **5 premières lignes** pour éviter de surcharger la sortie.

Commencez par les questions **1 à 10** (niveau basique), puis continuez avec **11 à 20** (jointures et agrégations), et terminez par **21 à 30** (dates, fenêtres et sous-requêtes).

---

## 🧠 Liste des questions

### Niveau 1 — bases (comptages, filtres simples)

1. Combien de produits existe-t-il ?
2. Quel est le prix moyen d’un produit ?
3. Quel est le prix minimum et le prix maximum parmi les produits ?
4. Quels sont les produits à moins de 30€ (affichez le nom et le prix) triés par prix croissant ?
5. Quels clients ont été créés en 2025 ? (affichez nom complet et ville)
6. Listez les articles de la catégorie “Audio” (nom et prix).
7. Quel est le stock total de tous les produits confondus ?
8. Quels sont les 5 produits les plus chers (nom et prix) ?
9. Quels sont les clients vivant à Paris ou Lyon ?
10. Listez les commandes dont le statut est **PENDING** ou **CANCELLED**.

---

### Niveau 2 — jointures & agrégations

11. Combien de produits par catégorie (triez par ordre décroissant) ?
12. Quel est le prix moyen par catégorie (arrondi à 2 décimales) ?
13. Quelles sont les 3 villes ayant le plus de clients ?
14. Combien de commandes par statut ?
15. Pour chaque produit, quelle est la quantité totale vendue (excluez les commandes annulées/pending) ?
16. Calculez le chiffre d’affaires par catégorie (excluez annulées/pending).
17. Calculez la marge brute réalisée par produit (Σ quantité × (unit_price − cost)) en excluant les commandes annulées/pending.
18. Quelle est la moyenne d’articles par commande (hors annulées/pending) ?
19. Quels produits n’ont jamais été vendus ?
20. Quelle est la note moyenne par produit (si des avis existent) ?

---

### Niveau 3 — dates, sous-requêtes, fenêtres

21. Quel est le chiffre d’affaires par mois pour l’année 2025 ?
22. Quels sont les **top 5 clients** par chiffre d’affaires (affichez nom, ville, CA) ?
23. Quels clients n’ont passé **aucune commande** ?
24. Quelle est la part (%) du chiffre d’affaires par catégorie ?
25. Quelle commande contient le plus grand nombre d’articles (quantité totale) ?
26. Parmi les produits de la catégorie “Audio”, quels produits ont un prix supérieur à la médiane de la catégorie ?
27. Affichez les ventes cumulées (CA cumul) par mois.
28. Quels produits ont une marge unitaire (`price − cost`) supérieure à 25€ ?
29. Quels clients ont passé **au moins 2 commandes valides** (non annulées/pending) ?
30. Quel est le **panier moyen** (chiffre d’affaires moyen par commande valide) ?

---

## 💡 Pour aller plus loin

- Comparez `price` et `unit_price` pour comprendre la différence entre **prix catalogue** et **prix de vente**.
- Essayez d’imaginer des **retours ou annulations partielles** pour complexifier vos requêtes `JOIN` et `WHERE`.
- Créez une **vue SQL** qui calcule le **chiffre d’affaires mensuel cumulé**.
