# Catalogue des données — couche Gold

## Présentation

La couche Gold expose des vues destinées à l’analyse et au reporting. Elle rassemble les données nettoyées des sources CRM et ERP dans un modèle en étoile comprenant deux dimensions et une table de faits.

Les clés `customer_key` et `product_key` sont calculées à la volée par `ROW_NUMBER()` dans les vues. Ce sont des clés de substitution générées à la lecture, et non des colonnes stockées dans des tables Gold physiques.

## `gold.dim_customers`

**Rôle :** une ligne par client issu du CRM, enrichie avec les données démographiques et géographiques disponibles dans l’ERP.

| Colonne | Type SQL | Description |
|---|---|---|
| `customer_key` | `BIGINT` | Clé de substitution générée par la vue pour les jointures analytiques. |
| `customer_id` | `INT` | Identifiant du client provenant du CRM. |
| `customer_number` | `NVARCHAR(50)` | Identifiant métier du client (`cst_key`). |
| `first_name` | `NVARCHAR(50)` | Prénom du client. |
| `last_name` | `NVARCHAR(50)` | Nom de famille du client. |
| `country` | `NVARCHAR(50)` | Pays issu des données de localisation ERP, s’il est disponible. |
| `marital_status` | `NVARCHAR(50)` | Statut marital standardisé dans Silver. |
| `gender` | `NVARCHAR(50)` | Genre ; la valeur CRM est privilégiée et la valeur ERP sert de repli. |
| `birthdate` | `DATE` | Date de naissance issue des données client ERP. |
| `create_date` | `DATE` | Date de création de l’enregistrement client dans le CRM. |

## `gold.dim_products`

**Rôle :** une ligne par produit actuellement retenu, enrichie avec sa catégorie et sa sous-catégorie ERP. La vue exclut les versions de produits dont `prd_end_dt` n’est pas nul.

| Colonne | Type SQL | Description |
|---|---|---|
| `product_key` | `BIGINT` | Clé de substitution générée par la vue pour les jointures analytiques. |
| `product_id` | `INT` | Identifiant du produit provenant du CRM. |
| `product_number` | `NVARCHAR(50)` | Identifiant métier du produit (`prd_key`). |
| `product_name` | `NVARCHAR(50)` | Nom du produit. |
| `category_id` | `NVARCHAR(50)` | Identifiant de catégorie issu des données CRM transformées. |
| `category` | `NVARCHAR(50)` | Catégorie du produit issue de l’ERP. |
| `subcategory` | `NVARCHAR(50)` | Sous-catégorie du produit issue de l’ERP. |
| `maintenance` | `NVARCHAR(50)` | Indicateur de maintenance provenant de la classification ERP. |
| `cost` | `INT` | Coût du produit dans les données CRM. |
| `product_line` | `NVARCHAR(50)` | Ligne de produit standardisée dans Silver. |
| `start_date` | `DATE` | Date de début de la version de produit. |

## `gold.fact_sales`

**Rôle :** une ligne par ligne de vente issue de `crm_sales_details`. Les clés client et produit relient chaque vente aux dimensions Gold.

| Colonne | Type SQL | Description |
|---|---|---|
| `order_number` | `NVARCHAR(50)` | Numéro de commande. |
| `product_key` | `BIGINT` | Clé de la dimension produit correspondante, si trouvée. |
| `customer_key` | `BIGINT` | Clé de la dimension client correspondante, si trouvée. |
| `order_date` | `DATE` | Date de commande. |
| `shipping_date` | `DATE` | Date d’expédition. |
| `due_date` | `DATE` | Date d’échéance. |
| `sales_amount` | `INT` | Montant des ventes pour la ligne de commande. |
| `quantity` | `INT` | Quantité vendue. |
| `price` | `INT` | Prix unitaire de la ligne de vente. |

Les trois vues sont définies dans [`scripts/gold/ddl_gold.sql`](../scripts/gold/ddl_gold.sql). Les contrôles portant sur leur intégrité sont disponibles dans [`tests/quality_checks_gold.sql`](../tests/quality_checks_gold.sql).
