


Projet Hébergement Site Statique AWS S3 avec CI/CD GitHub



1. Création d’une page HTML simple
J’ai choisi de créer une page web simple en HTML, sans base de données, parce que je voulais un site léger et à faible coût. Un site statique est rapide à charger, facile à déployer, et ne nécessite pas de serveur ou base de données, ce qui évite des coûts supplémentaires.
Pourquoi ? Parce qu’un site statique est simple et efficace, parfait pour mon projet et mes contraintes financières.



3. Création d’un bucket S3 sur AWS
J’ai créé un bucket sur AWS S3 appelé apple-sitewebaws, qui fonctionne comme un dossier dans le cloud. J’ai activé l’option d’hébergement statique pour pouvoir rendre mon site accessible sur internet.
Pourquoi ? S3 est un service fiable, économique, et disponible 24h/24 sans que j’aie besoin de gérer un serveur.



5. Configuration de la policy pour accès public en lecture seule
J’ai appliqué une policy JSON sur le bucket pour que tout le monde puisse consulter le site, mais personne ne puisse modifier ou supprimer les fichiers.

json
Copier
Modifier
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
Pourquoi ? Pour que le site soit visible par tous en lecture seule, et protéger les fichiers contre toute modification accidentelle ou malveillante.



4. Création d’un utilisateur IAM avec droits limités
J’ai créé un utilisateur IAM avec uniquement les droits nécessaires pour modifier les fichiers dans mon bucket S3, et rien d’autre.

json
Copier
Modifier
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
Pourquoi ? C’est important pour la sécurité, afin de ne pas donner plus de droits que nécessaire, et éviter les erreurs ou abus.




5. Configuration de GitHub Actions pour déployer automatiquement
J’ai mis en place un workflow GitHub Actions qui se déclenche à chaque fois que je pousse une modification sur la branche principale. Ce workflow synchronise automatiquement les fichiers vers mon bucket S3.

yaml
Copier
Modifier
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
Pourquoi ? Automatiser le déploiement évite les erreurs humaines, me fait gagner du temps et garantit que la dernière version est toujours en ligne.



6. Stockage des clés AWS dans GitHub Secrets
Pour que GitHub Actions puisse accéder à AWS, j’ai enregistré mes clés d’accès AWS dans la section secrète “Secrets” de mon dépôt GitHub. Ainsi, mes clés ne sont pas visibles dans le code.
Pourquoi ? Protéger mes identifiants est essentiel pour éviter tout risque de piratage ou fuite.



8. Test du fonctionnement
Après chaque mise à jour, je vérifie que le site est bien accessible et que le déploiement s’est bien passé.
Pourquoi ? Tester à chaque étape me permet d’être sûre que tout fonctionne correctement et que les visiteurs peuvent accéder au site sans problème.



## Vidéo de démonstration

Voici une vidéo qui montre que mon projet fonctionne bien :  
[Voir la vidéo ici](https://drive.google.com/file/d/1jaGdij7iAg3uLPDz97n4vS-a7tlA2PQB/view?usp=sharing)

