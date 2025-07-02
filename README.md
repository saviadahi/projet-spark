# 🔍 Projet PySpark – Recherche des Amis Communs

## 1. Introduction

Ce projet applique les concepts de programmation distribuée avec **Apache Spark**, en résolvant un problème courant des réseaux sociaux : **trouver les amis communs entre deux utilisateurs**.  
Il illustre l’utilisation de **PySpark** pour traiter efficacement un graphe social.

---

## 2. Objectifs du projet

- Manipuler PySpark (API Python de Spark)
- Modéliser un graphe social à partir d’un fichier texte
- Identifier les amis communs entre deux utilisateurs donnés
- Utiliser MapReduce et les traitements distribués
- Comprendre le fonctionnement des RDDs (Resilient Distributed Datasets)

---

## 3. Présentation d'Apache Spark

### 🔷 Qu’est-ce que Spark ?

Apache Spark est un moteur de traitement distribué open-source conçu pour l’analyse de données à grande échelle. Il offre des performances élevées grâce à son traitement **en mémoire (in-memory processing)**.

### 🔷 Concepts clés

- **RDD** : Structure de données distribuée et tolérante aux pannes
- **DataFrame** : Abstraction tabulaire pour les opérations SQL
- **MapReduce** : Paradigme de traitement distribué
  - **Map** : transforme les données
  - **Reduce** : agrège les résultats par clé

### 🔷 Avantages

- **Rapidité** : jusqu’à 100× plus rapide que Hadoop
- **Polyvalence** : supporte Python, Scala, Java, R
- **Intégration** : avec Hadoop, Kafka, etc.

---

## 4. Présentation des données

### 🔹 Format d’entrée :

```
<user_id> <Nom> <friend_id1>,<friend_id2>,...
```

Exemple :
```
1 Sidi 2,3,4
2 Mohamed 1,3,5
```

Cela signifie que :
- L’utilisateur `1` (Sidi) est ami avec `2`, `3` et `4`.
- Les amitiés sont **symétriques**.

---

## 5. Méthodologie & Étapes du Projet

1. Chargement du graphe social
2. Extraction des paires d’amis normalisées `(min, max)`
3. Regroupement des paires
4. Calcul de l’intersection des listes d’amis
5. Filtrage de la paire spécifique `1<Sidi>` et `2<Mohamed>`

---

## 6. Architecture de traitement avec Spark

Spark utilise des **RDDs** répartis sur plusieurs nœuds.  
On suit le modèle **MapReduce** :

- `map()` → extraction des couples `(user, ami)`
- `reduceByKey()` → intersection des amis pour chaque paire

---

## 7. Implémentation technique (extraits)

```python
df = spark.read.text("social_graph.txt").select(
    split(col("value"), " ")[0].cast("integer").alias("user_id"),
    split(col("value"), " ")[1].alias("name"),
    split(split(col("value"), " ")[2], ",").cast("array<integer>").alias("friends")
)

pairs_df = df.select(
    "user_id", "name", explode("friends").alias("friend_id")
).withColumn(
    "pair", array(sort_array(array(col("user_id"), col("friend_id"))))
)

common_friends_df = pairs_df.groupBy("pair").agg(
    collect_set("friend_id").alias("common_friends")
).filter(size(col("common_friends")) > 0)
```

---

## 8. Résultats & Analyse

### Données de test

| user_id | nom     | amis     |
|---------|---------|----------|
| 1       | Sidi    | 2,3,4    |
| 2       | Mohamed | 1,3,5    |
| 3       | Ali     | 1,2,4    |

### Résultat :

```
1<Sidi>2<Mohamed>3
```

➡️ Cela signifie que **Sidi (1)** et **Mohamed (2)** ont **Ali (3)** comme ami commun.

---

## 9. Conclusion

Ce projet a permis de :

- Mettre en œuvre le traitement parallèle avec Spark
- Appliquer MapReduce à un graphe social
- Utiliser PySpark pour modéliser un problème réel

La méthode est applicable à d’autres cas comme :
- La détection de communautés
- La suggestion d’amis
- L’analyse de réseaux sociaux à grande échelle

---

## 📁 Structure du dépôt

```
spark-common-friends/
├── src/main.py
├── data/friends.txt
├── test/test_output.txt
├── README.md
├── requirements.txt
└── .gitignore
```

---

## 🔗 Auteurs

- Nom : **À compléter**
- Classe : **À compléter**
- Encadrant : **À compléter**
