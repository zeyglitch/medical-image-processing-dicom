# Traitement d'images médicales : analyse d'IRM au format DICOM

Projet réalisé dans le cadre du cours de traitement d'images médicales (ISIS Castres, 2025-2026).

## Ce que montre ce projet

Une chaîne complète sur des coupes d'IRM : lecture du format DICOM, **mesure** de la qualité d'une image (contraste, histogramme, entropie), **comparaison quantitative** de plusieurs techniques de rehaussement, puis **segmentation** par seuillage et morphologie mathématique.

## Démarche et résultats

**Mesures sur une coupe cérébrale** (384 × 274, `int16`, 92 niveaux utilisés sur 16 bits, 41,8 % de fond) : contraste RMS de la tête 27,79, entropie 4,5238 bits (maximum 6,5236). Le contraste de Michelson (1,0000) est peu informatif.

**Rehaussement du contraste** (contraste RMS de la tête, échelle 0 à 1 ; entropie) :

| Traitement | Entropie (bits) | Contraste RMS |
|---|---|---|
| Originale | 4,5238 | 0,3054 |
| Étirement linéaire P1–P99 | 4,3218 | 0,3428 |
| Étirement linéaire P10–P90 | 3,9862 | 0,3685 |
| Égalisation globale | 4,5238 | 0,1667 |
| Égalisation restreinte à la tête | 4,3389 | 0,2865 |
| CLAHE | non comparable | 0,2957 |

Seul l'étirement linéaire augmente le contraste de la tête, au prix de l'entropie. L'égalisation globale le fait chuter car le fond (41,8 % des pixels) accapare la plage dynamique ; la restreindre à la tête ou utiliser le CLAHE évite cet effondrement.

**Morphologie** : nettoyage d'une image binaire bruitée, comparaison de rayons (119 objets avant, 38 / 30 / 27 / 26 après pour les rayons 1 à 4), prolongement des bords pour éviter les artefacts.

**Détection de 4 marqueurs sur une coupe du cou** : zone d'analyse localisée automatiquement, seuil d'Otsu appliqué à la queue de l'histogramme (43,0). 4 objets exactement pour des seuils de 43,0 à 60,3 (5 objets dès 0,9× le seuil), diamètres équivalents de 3,8 à 6,4 mm.

**Comptage d'objets sur une image satellite** : 39 détections pour 39 piscines comptées à la main. Vérification visuelle boîte par boîte, saisie à la main (non automatisée) : précision et rappel de 100 %. Un Otsu global classait 77,3 % des pixels en « piscine » ; appliqué à la queue de l'histogramme, il en retient 1,7 %.

## Limites

- Une seule coupe 2D par partie : aucune généralisation à d'autres coupes ou patients.
- Seuils vérifiés par un contrôle de robustesse, mais pas validés sur des données indépendantes.
- Précision et rappel des piscines : issus de ma vérification visuelle, pas d'annotations indépendantes.
- L'entropie ne mesure pas la lisibilité clinique des tissus.
- Projet de traitement d'images, pas un outil de diagnostic.

## Stack

Python · pydicom · scikit-image · NumPy · SciPy · pandas · Matplotlib

## Reproduire

Les données ne sont pas versionnées : les IRM proviennent du dépôt public [datalad/example-dicom-structural](https://github.com/datalad/example-dicom-structural) (jeu anonymisé ; voir la licence de ce dépôt). Elles ne sont pas redistribuées ici ; `cell.png` et `moliets.png` ont été fournies avec le cours. Placer les fichiers dans `./data` (ou adapter `DATA_DIR`, première cellule), puis :

```bash
pip install -r requirements.txt
```

Le notebook détecte automatiquement s'il tourne sous Google Colab ou en local.

Les sorties du notebook contiennent des figures issues de ces données, pour permettre de voir les résultats sans les rejouer.

## Auteur

**Mathieu Jonniaux** — ISIS Castres (partenaire INSA), FIE4 DSIA
[github.com/zeyglitch](https://github.com/zeyglitch) · [LinkedIn](https://www.linkedin.com/in/mathieu-jonniaux)
