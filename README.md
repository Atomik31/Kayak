![Kayak](kayak_logo.png)

# Kayak — Plan your trip

Projet réalisé dans le cadre du bloc 1 de la certification CDSD (Jedha).

---

## Contexte

Kayak veut proposer à ses utilisateurs une fonctionnalité de recommandation de destinations en France, en tenant compte des conditions météo sur les 7 prochains jours et de la qualité des hébergements disponibles.

L'idée est simple : croiser les prévisions météo de 35 villes avec le top 3 des hôtels les mieux notés sur Booking, et présenter ça sur une carte interactive.

---

## Ce que j'ai fait

**Collecte des données**

- Géolocalisation des 35 villes via l'API Nominatim (OpenStreetMap)
- Récupération des prévisions 7 jours via OpenWeatherMap One Call API 3.0
- Scraping du top 3 hôtels par ville sur Booking.com avec Selenium + Parsel

**Calcul du Beauty Score**

Pour chaque ville, je calcule un score sur 100 à partir de 3 critères météo :
- `rain_score` : pénalité par mm de pluie cumulée (idéal = 0 mm)
- `temp_score` : pénalité par degré d'écart avec 20°C (idéal = 20°C)
- `humidity_score` : pénalité par % d'humidité au-dessus de 40% (idéal = 40%)

Le beauty score est la moyenne des trois, entre 0 et 100.

**Pipeline ETL**

1. Les données brutes sont stockées dans un bucket **AWS S3** (data lake)
2. Une étape de nettoyage valide les doublons, les plages de valeurs, les coordonnées GPS et normalise les chaînes
3. Les données nettoyées sont chargées dans une base **Neon DB** (PostgreSQL)

**Visualisation**

2 cartes Plotly interactives :
- Beauty score des 35 villes (hover : top 3 hôtels par ville)
- Température moyenne 7 jours (hover : top 3 hôtels par ville)

---

## Stack

- Python — Pandas, Requests, Selenium, Parsel
- OpenWeatherMap API 3.0 / Nominatim
- AWS S3
- Neon DB (PostgreSQL) + psycopg2
- Plotly Express

---

## Structure

```
Kayak/
├── data/
│   ├── processed/
│   │   ├── villes_meteo.csv
│   │   └── df_top3_hotels.csv
├── docs/
│   └── consignes.md
├── notebooks/
│   └── 01-Plan_your_trip_with_Kayak.ipynb
├── .env               # clés API (non versionné)
├── requirements.txt
└── README.md
```

---


