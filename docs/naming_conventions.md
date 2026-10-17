# Conventions de nommage

Ce document décrit les conventions utilisées dans les schémas et scripts SQL de ce projet.

## Principes généraux

- Les noms d’objets SQL sont en anglais et en minuscules.
- Les mots sont séparés par des tirets bas (`snake_case`).
- Les noms doivent décrire clairement l’objet et éviter les mots réservés de SQL.
- Les schémas de données suivent les couches `bronze`, `silver` et `gold`.

## Tables et vues

### Bronze

Les noms reprennent le système source et le nom de l’entité, selon le format `<source>_<entite>`. Les préfixes utilisés ici sont `crm` et `erp`.

Exemples : `bronze.crm_cust_info`, `bronze.erp_cust_az12`.

### Silver

Les noms conservent la correspondance avec les entités de la source afin de faciliter le suivi des transformations. Les noms restent alignés sur ceux de Bronze.

Exemples : `silver.crm_sales_details`, `silver.erp_loc_a101`.

### Gold

Les noms indiquent le rôle analytique de la vue, puis le domaine métier :

- `dim_<entite>` pour une dimension ;
- `fact_<entite>` pour une table de faits.

Exemples utilisés dans le projet : `gold.dim_customers`, `gold.dim_products` et `gold.fact_sales`.

## Colonnes

### Clés de substitution

Les clés de substitution des dimensions se terminent par `_key`. Dans les vues Gold, elles sont calculées avec `ROW_NUMBER()`.

Exemples : `customer_key`, `product_key`.

### Colonnes techniques

Les colonnes de métadonnées générées par l’entrepôt commencent par `dwh_`, suivi d’un nom descriptif.

Exemple : `dwh_create_date`, qui indique la date et l’heure de création de l’enregistrement dans la couche Silver.

## Procédures de chargement

Les procédures de chargement suivent le format `load_<couche>` et sont placées dans le schéma correspondant à la couche chargée.

Exemples : `bronze.load_bronze` et `silver.load_silver`.

La couche Gold de ce projet est composée de vues créées par `scripts/gold/ddl_gold.sql` ; elle n’a pas de procédure de chargement dédiée.
