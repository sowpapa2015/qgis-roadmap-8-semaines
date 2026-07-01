# 📊 Pack data pour les exercices

Toutes les données utilisées dans les exercices sont **gratuites** et **librement téléchargeables** sur les portails officiels français. Cette page liste précisément :

- les liens directs de téléchargement
- le format et la taille de chaque dataset
- le filtrage à appliquer pour ne garder que la Haute-Garonne (31)
- les valeurs attendues pour vérifier l'intégrité

---

## ⏱️ Pré-requis avant la séance 1

Télécharger **avant le démarrage** ce qui est marqué 🟢 dans la table ci-dessous (priorité 1, environ 500 Mo cumulés). Les autres datasets peuvent être téléchargés au fil des séances.

---

## Vue d'ensemble

| # | Donnée | Source | Format | Taille | Utilisé en | Priorité |
|---|---|---|---|---|---|---|
| 1 | Admin Express COG | IGN | SHP | ~400 Mo | S2 → S8 | 🟢 |
| 2 | INSEE RP — population | INSEE | CSV / XLSX | ~5 Mo | S3 | 🟢 |
| 3 | INSEE BPE — équipements | INSEE | CSV | ~50 Mo | S4 | 🟡 |
| 4 | OpenStreetMap | OSM via QuickOSM | GeoJSON live | ~10 Mo | S5 | 🟡 (plugin) |
| 5 | OCS GE Haute-Garonne | IGN | SHP | ~200 Mo | S6 | 🟡 |
| 6 | Contours IRIS | IGN | SHP | ~50 Mo | S6 | 🟡 |
| 7 | RGE ALTI 5 m | IGN | ASC / TIF | ~1,5 Go | S7 | 🔴 |
| 8 | PLU Toulouse Métropole | Géoportail Urbanisme | SHP | ~10 Mo | S8 bonus | 🔵 optionnel |

**Légende priorités :**
- 🟢 Indispensable avant S1
- 🟡 À télécharger avant la séance concernée
- 🔴 Lourd, télécharger la veille
- 🔵 Optionnel

---

## 📁 Structure de dossier recommandée

```
C:\QGIS_TOULOUSE\
├── DATA\
│   ├── ADMIN_EXPRESS\          # Tout le ZIP décompressé
│   ├── INSEE\
│   │   ├── RP\                 # Population communale
│   │   └── BPE\                # Équipements
│   ├── OCSGE\                  # Occupation du sol 31
│   ├── IRIS\                   # Contours IRIS
│   ├── RGEALTI\                # MNT
│   └── PLU_TLSE\               # PLU Toulouse Métropole (bonus)
├── S1\                          # Travaux séance 1
├── S2\
├── ...
└── S8\
```

Crée cette arborescence dès le départ. Tu ne le regretteras pas.

---

# 🗂️ Détail des sources

## 1. 🟢 Admin Express COG — Communes françaises (IGN)

**Description :** contours administratifs (communes, départements, régions) — la donnée la plus utilisée du parcours.

**Lien officiel :**
```
https://geoservices.ign.fr/adminexpress
```

**Procédure :**
1. Va sur la page ci-dessus
2. Section **« Admin Express »** → choisir **« Admin Express COG France entière édition <millésime récent> »**
3. Format : **Shapefile (.shp)** projection **Lambert-93 (EPSG:2154)**
4. Télécharger le ZIP (~400 Mo)
5. Décompresser dans `C:\QGIS_TOULOUSE\DATA\ADMIN_EXPRESS\`
6. Le fichier clé est `COMMUNE.shp`

**Vérification d'intégrité :**
- ~34 800 communes en France métropolitaine
- 586 communes dans le département 31 (filtrer `INSEE_DEP = '31'`)
- Toulouse (INSEE 31555) doit faire environ 11 830 hectares

**Licence :** [Licence Ouverte Etalab 2.0](https://www.etalab.gouv.fr/licence-ouverte-open-licence) — diffusion libre.

---

## 2. 🟢 INSEE Recensement — Population communale

**Description :** population municipale par commune (millésime le plus récent).

**Lien officiel :**
```
https://www.insee.fr/fr/statistiques/serie/000436391
```
ou via le portail data.gouv.fr :
```
https://www.data.gouv.fr/fr/datasets/populations-legales-communales-et-departementales/
```

**Procédure :**
1. Aller sur la page INSEE ci-dessus
2. Télécharger le fichier **« Populations légales — Recensement »** (.xlsx ou .csv)
3. Placer dans `C:\QGIS_TOULOUSE\DATA\INSEE\RP\`

**Colonnes utiles :**
- `CODGEO` (code INSEE de la commune, format texte 5 caractères)
- `PMUN` (population municipale)
- `PTOT` (population totale)

**Vérification d'intégrité :**
- Toulouse (CODGEO = 31555) doit afficher environ 504 000 habitants
- La Haute-Garonne (somme dpt 31) doit afficher ~1,46 millions d'habitants

**Licence :** Licence Ouverte Etalab 2.0.

---

## 3. 🟡 INSEE BPE — Base Permanente des Équipements

**Description :** liste de tous les équipements (écoles, médecins, commerces, sport, culture...) avec leur localisation et leur commune.

**Lien officiel :**
```
https://www.insee.fr/fr/statistiques/3568638
```
ou directement :
```
https://www.insee.fr/fr/statistiques/<dernier_millesime>?sommaire=...
```
(recherche « Base permanente des équipements » sur insee.fr)

**Procédure :**
1. Aller sur la page « Base permanente des équipements » de l'INSEE
2. Télécharger le fichier **« Tous équipements par commune (CSV) »**
3. Placer dans `C:\QGIS_TOULOUSE\DATA\INSEE\BPE\`

**Colonnes utiles :**
- `DEPCOM` (code INSEE commune)
- `TYPEQU` (type d'équipement, code à 4 caractères)
- Pour les médecins généralistes : `TYPEQU = 'D201'`
- Pour les écoles élémentaires : `TYPEQU = 'C104'`
- Pour les pharmacies : `TYPEQU = 'D301'`

**Astuce :** la BPE est volumineuse au niveau national. Pour aller plus vite, filtre dès la lecture sur le département 31 dans QGIS via le constructeur de requête.

**Licence :** Licence Ouverte Etalab 2.0.

---

## 4. 🟡 OpenStreetMap — Bâti, transports, équipements (via QuickOSM)

**Description :** données collaboratives mondiales — utilisées en S5 pour extraire les arrêts de transport de Toulouse.

**Méthode :** **plugin QuickOSM** dans QGIS. Pas de téléchargement préalable.

**Procédure d'installation du plugin :**
1. Dans QGIS, menu **Extensions → Installer/gérer les extensions**
2. Onglet **Toutes**
3. Recherche : `QuickOSM`
4. Cliquer **Installer le plugin**

**Utilisation en S5 :** une fois zoomé sur Toulouse, utiliser **Vecteur → QuickOSM → QuickOSM**, choisir Clé/Valeur (ex. `public_transport=stop_position`), dans l'étendue du canevas. Le plugin télécharge et charge automatiquement les données.

**Licence :** [ODbL](https://www.openstreetmap.org/copyright) — partage à l'identique avec mention OSM.

---

## 5. 🟡 OCS GE Haute-Garonne — Occupation du sol (IGN)

**Description :** référentiel d'occupation et d'usage du sol au sens de la loi Climat-Résilience 2021. Utilisé pour calculer les taux d'artificialisation.

**Lien officiel :**
```
https://geoservices.ign.fr/ocsge
```

**Procédure :**
1. Aller sur la page OCS GE de l'IGN
2. Choisir **« Téléchargement par département »** → **Département 31 (Haute-Garonne)**
3. Télécharger le ZIP du dernier millésime disponible (~200 Mo)
4. Décompresser dans `C:\QGIS_TOULOUSE\DATA\OCSGE\`
5. Le fichier clé est `OCCUPATION_SOL.shp`

**Codes de classification utilisés en S6 :**
- `CS1.x` → zones bâties (logement, activités, équipements)
- `CS2.x` → autres surfaces construites (routes, parkings...)
- `CS4.x` à `CS5.x` → formations végétales
- `CS6.x` → zones humides
- `CS7.x` → surfaces en eau

**Définition légale de l'artificialisation :** CS1 + CS2 (au sens loi ZAN).

**Vérification d'intégrité :**
- Le département 31 compte plusieurs centaines de milliers de polygones OCS GE
- Sur Toulouse seulement : ~80 000 polygones attendus

**Licence :** Licence Ouverte Etalab 2.0.

---

## 6. 🟡 Contours IRIS — Découpage infra-communal (IGN)

**Description :** maillage infra-communal (~2000 habitants par IRIS). Utilisé en S6 pour les analyses à l'échelle des quartiers.

**Lien officiel :**
```
https://geoservices.ign.fr/contoursiris
```

**Procédure :**
1. Aller sur la page ContoursIRIS de l'IGN
2. Télécharger le ZIP **France métropolitaine** du dernier millésime (~50 Mo)
3. Décompresser dans `C:\QGIS_TOULOUSE\DATA\IRIS\`
4. Le fichier clé est `CONTOURS-IRIS.shp` (ou `IRIS_FE_<millésime>.shp` selon version)

**Filtrage Toulouse Métropole :**
- `INSEE_COM = '31555'` → uniquement les IRIS de Toulouse (environ 117 IRIS)
- ou `left("INSEE_COM", 2) = '31'` → tous les IRIS du département 31

**Licence :** Licence Ouverte Etalab 2.0.

---

## 7. 🔴 RGE ALTI 5 m — Modèle Numérique de Terrain (IGN)

**Description :** modèle d'altitude haute résolution (5 m × 5 m). Volumineux mais essentiel pour S7 (relief, ombrage).

**Lien officiel :**
```
https://geoservices.ign.fr/rgealti
```

**Procédure :**
1. Aller sur la page RGE ALTI de l'IGN
2. Choisir **« Téléchargement par dalles »** → sélectionner les dalles autour de Toulouse
3. Ou **« Téléchargement par département »** → département 31 (mais ~1,5 Go)
4. Format : **ASC** ou **GeoTIFF**
5. Décompresser dans `C:\QGIS_TOULOUSE\DATA\RGEALTI\`

**Alternative légère :** si tu n'as pas la place, télécharge **BD ALTI 25 m** sur la même page IGN. Résolution moindre mais 10× plus léger.

**Vérification d'intégrité :**
- Altitude minimale autour de Toulouse : ~135 m (rives de la Garonne)
- Altitude maximale visible dans le département 31 : >3000 m (sommets pyrénéens)

**Astuce performance QGIS :** clic droit sur la couche raster → **Propriétés → Pyramides → Construire** (pour accélérer l'affichage).

**Licence :** Licence Ouverte Etalab 2.0.

---

## 8. 🔵 PLU Toulouse Métropole — bonus S8 (optionnel)

**Description :** documents d'urbanisme opposables (Plan Local d'Urbanisme intercommunal) de Toulouse Métropole.

**Lien officiel :**
```
https://www.geoportail-urbanisme.gouv.fr/
```

**Procédure :**
1. Aller sur le Géoportail de l'Urbanisme
2. Zoomer sur Toulouse Métropole
3. Cliquer sur le territoire → **Télécharger les documents** (CNIG)
4. Récupérer les zonages, prescriptions, secteurs

**Licence :** Licence Ouverte Etalab 2.0.

---

# 🔍 Vérifications d'intégrité par séance

| Séance | Donnée principale | Valeurs attendues pour validation |
|---|---|---|
| S2 | Admin Express COG | 586 communes du dpt 31, Toulouse = 11 830 ha |
| S3 | INSEE RP | Toulouse ~504 000 hab, médiane communale 31 ~700 hab |
| S4 | INSEE BPE | Plusieurs milliers d'équipements répartis sur le 31 |
| S5 | OSM QuickOSM | Quelques milliers d'arrêts de transport à Toulouse |
| S6 | OCS GE + IRIS | ~117 IRIS sur Toulouse, taux artif hypercentre > 85 % |
| S7 | RGE ALTI | Altitude Toulouse 135-180 m |
| S8 | Recyclage | — |

Si tes valeurs sont **très différentes** de celles attendues, vérifie :
- Le CRS (doit être Lambert-93 EPSG:2154 partout)
- Le filtrage (correctement appliqué sur le 31)
- L'année de référence (millésime cohérent entre toutes les données)

---

# 🆘 Si un lien ne fonctionne plus

Les portails IGN et INSEE évoluent. Si un lien direct est cassé :

1. Va sur **`geoservices.ign.fr`** (page d'accueil)
2. Utilise la barre de recherche en haut avec le nom du référentiel (`Admin Express`, `OCS GE`, `RGE ALTI`...)
3. Tu retrouveras la page de téléchargement actuelle

Pour l'INSEE : `insee.fr` → recherche par nom de dataset.

---

# 📚 Sources complémentaires utiles (pour aller plus loin)

- **Sandre** (réseau hydrographique français) : https://www.sandre.eaufrance.fr/
- **BRGM InfoTerre** (géologie, sols) : https://infoterre.brgm.fr/
- **Copernicus** (Sentinel-2 satellite récent) : https://dataspace.copernicus.eu/
- **OpenDataSoft Toulouse Métropole** (transports Tisséo, services publics) : https://data.toulouse-metropole.fr/
- **Observatoire des Territoires** (cartes thématiques INSEE) : https://www.observatoire-des-territoires.gouv.fr/

---

# 🎁 Pack data minimum si tu es pressé·e

Si tu démarres maintenant et veux juste **commencer la S1 dans l'heure**, télécharge seulement ces 2 fichiers :

1. **Admin Express COG** (400 Mo, ~10 min sur fibre)
2. **INSEE Population communale** (5 Mo, instantané)

Avec ces deux datasets seulement, tu peux faire les **séances 1, 2, 3** sans souci. Les autres datasets se rajoutent au fil des semaines.

---

*Document mis à jour le 29/06/2026. Diffusion libre CC BY-SA 4.0.*
