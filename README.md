


Projet Hébergement Site Statique AWS S3 avec CI/CD GitHub



1. Développement du site statique

Création d’un site HTML/CSS léger, optimisé pour un hébergement S3.

Architecture sans back-end → réduction des coûts et amélioration des performances.

2. Mise en place du bucket S3 (Static Website Hosting)

Création d’un bucket apple-sitewebaws.

Activation du mode Static Website Hosting.

Configuration du point d’entrée index.html.

Objectif technique : utiliser S3 comme service d’hébergement scalable et hautement disponible, sans serveur.

3. Configuration de la sécurité S3 (Bucket Policy)
Bucket Policy : Accès public en lecture seule
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::apple-sitewebaws/*"
    }
  ]
}


Permet uniquement GET.

Empêche toute modification ou suppression via le public.

Principe appliqué : sécurité minimale nécessaire (Least Privilege).

4. Création d’un utilisateur IAM dédié avec permissions S3 limitées

Permissions autorisées :

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::apple-sitewebaws",
        "arn:aws:s3:::apple-sitewebaws/*"
      ]
    }
  ]
}


Objectif : permettre uniquement les actions nécessaires au pipeline CI/CD.

5. Mise en place du pipeline CI/CD avec GitHub Actions

Workflow déclenché automatiquement sur chaque push dans main.

name: Deploy to S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-3

      - run: aws s3 sync . s3://apple-sitewebaws --delete

Fonctionnement technique

Authentification sécurisée via GitHub Secrets.

Synchronisation automatique → aws s3 sync.

Suppression des fichiers obsolètes (--delete).

Processus 100% automatisé.

6. Tests et validation

Vérification après chaque pipeline : accessibilité du site, cohérence du contenu, statut S3.

Tests de synchronisation GitHub → AWS.

Validation que la policy IAM et les accès publics fonctionnent correctement.

Résultat

Déploiement entièrement automatisé via GitHub Actions.

Architecture serverless économique basée sur S3.

Sécurisation IAM + Bucket Policy selon les bonnes pratiques AWS.

Site statique disponible publiquement avec haute disponibilité.



## Vidéo de démonstration

Voici une vidéo qui montre que mon projet fonctionne bien :  
[Voir la vidéo ici](https://drive.google.com/file/d/1jaGdij7iAg3uLPDz97n4vS-a7tlA2PQB/view?usp=sharing)

