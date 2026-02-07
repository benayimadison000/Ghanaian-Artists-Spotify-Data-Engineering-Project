# Spotify Analytics Lakehouse — Azure Databricks Data-Engineering Project - Ghanaian Artists

## 📌 Overview
This project implements a full end‑to‑end **Lakehouse architecture** on **Azure Databricks**, ingesting Spotify artist, album, track, and collaborator data into a structured analytics environment.

The pipeline follows the **Bronze → Silver → Gold** medallion architecture and produces rich analytical datasets for visualization and insights.

This repository contains:

- Databricks notebooks for ingestion, transformation, and analytics  
- Documentation of the Lakehouse architecture  
- Aggregation logic for artist, album, track, and collaboration analytics  
- A reproducible structure for cloud‑based data engineering projects  

---

## 🏗️ Architecture

### Medallion Layers
| Layer | Purpose |
|-------|---------|
| **Bronze** | Raw ingestion of Spotify API responses (JSON) |
| **Silver** | Cleaned, normalized tables for artists, albums, tracks, and collaborators |
| **Gold** | Aggregated analytics tables for dashboards and insights |

### Cloud Components
- **Azure Databricks** — compute & notebook environment  
- **Azure Data Lake Storage Gen2** — Bronze/Silver/Gold storage  
- **Azure Key Vault** — secure secret management  
- **Service Principal** — secure authentication for ADLS mounts  
- **Databricks Repos** — GitHub integration  

---

## 📂 Repository Structure
s
spotify-analytics-lakehouse/ │ ├── notebooks/ │   ├── 01_bronze_ingestion.py │   ├── 02_silver_tracks.py │   ├── 03_silver_artists.py │   ├── 04_silver_albums.py │   ├── 05_silver_collaborators.py │   ├── 06_gold_artist_master.py │   ├── 07_gold_track_analytics.py │   └── 08_gold_album_analytics.py │ ├── docs/ │   ├── architecture.md │   └── data_model.png │ ├── config/ │   └── config_template.json   # sanitized template (no secrets) │ ├── .gitignore └── README.md


> **Note:** Real config files and secrets are excluded from GitHub.  
> Only a safe template is included.

---

## 🔐 Security & Secrets
All sensitive values (client IDs, secrets, tenant IDs, storage keys) are stored in **Azure Key Vault** and accessed in Databricks using:

```python
dbutils.secrets.get(scope="spotify-scope", key="client-secret")


Silver Layer — Normalization
The Silver layer flattens and cleans the raw JSON into analytics‑ready tables:
Silver Tables
- artists_silver
- albums_silver
- tracks_silver
- collaborators_silver
Transformations include:
- Exploding nested arrays
- Renaming flattened fields
- Deduplication
- Type casting
- Normalizing album/track structures

Gold Layer — Analytics
The Gold layer produces aggregated datasets for dashboards and insights.
Artist Master Table
Includes:
- Total albums
- Total tracks
- Explicit ratio
- Average track duration
- Market reach
- Collaboration metrics
- Release timeline
- Genres

Track Analytics
Includes:
- Duration stats
- Explicit content
- Album metadata
- Market availability
Album Analytics
Includes:
- Tracks per album
- Explicit track count
- Release dates
- Disc structure


Example Aggregations
  Albums per Artist

album_aggs = (
    albums_silver
    .filter(F.col("album_type") == "album")
    .groupBy("artist_id")
    .agg(
        F.countDistinct("album_id").alias("total_albums"),
        F.min("release_date").alias("earliest_release_date"),
        F.max("release_date").alias("latest_release_date")
    )
)

  Explicit Track Ratio

explicit_aggs = (
    tracks_silver.groupBy("artist_id")
    .agg(
        F.sum(F.col("explicit").cast("int")).alias("explicit_track_count"),
        F.count("*").alias("total_tracks")
    )
    .withColumn("explicit_ratio", F.col("explicit_track_count") / F.col("total_tracks"))
)

Visualization
The Gold tables are designed for:
- Databricks Visualization

Common dashboards include:
- Artist popularity & growth
- Album release timelines
- Collaboration networks
- Genre diversity

How to Run the Project
- Clone this repo using Databricks Repos
- Configure your Key Vault secrets
- Update config_template.json and create your local config
- Run notebooks in order:
- 01 → 02 → 03 → … → 08
- Validate Silver tables
- Generate Gold analytics
- Connect Power BI or Databricks SQL to Gold tables

License
Bernard Ayikpa-Madison
This project is for educational and portfolio purposes.


Acknowledgements
- Spotify Web API
- Azure Databricks
- Azure Data Lake Storage Gen2
- Azure Key Vault





