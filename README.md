Projet Hébergement Site Statique AWS S3 avec CI/CD GitHub


1. Création d’une page HTML simple
J’ai choisi de faire une page web simple en HTML sans base de données, parce que je voulais un site léger et surtout à faible coût. En utilisant un site statique, je n’ai pas besoin de serveur cher ni de base de données, ce qui me permet de respecter ma contrainte financière tout en présentant efficacement mon projet.

Pourquoi ?
Parce qu’un site statique c’est rapide, facile à déployer, et ça évite de compliquer avec des serveurs ou bases de données.


2. Création d’un bucket S3 sur AWS
J’ai créé un bucket sur AWS S3, c’est comme un dossier dans le cloud. J’ai activé l’option d’hébergement statique pour pouvoir servir mon site sur internet.

Pourquoi ?
S3 est fiable et pas cher. Ça me permet d’avoir mon site dispo 24h/24 sans gérer un serveur.


3. Configuration de la policy pour accès public en lecture seule
J’ai mis une policy sur le bucket pour que tout le monde puisse lire le site mais personne ne peut modifier ou supprimer les fichiers.





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
      "Resource": "arn:aws:s3:::mon-site-devops/*"
    }
  ]
}
Pourquoi ?
Pour que le site soit visible par tous mais protégé contre les modifications qui peuvent casser le site.


4. Création d’un utilisateur IAM avec droits limités
J’ai créé un utilisateur IAM qui a seulement les droits de modifier les fichiers dans mon bucket, pas plus.


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
        "arn:aws:s3:::mon-site-devops",
        "arn:aws:s3:::mon-site-devops/*"
      ]
    }
  ]
}



Pourquoi ?
C’est important pour la sécurité, pour ne pas donner trop de droits qui peuvent faire des erreurs ou des dégâts.



5. Configuration GitHub Actions pour déployer automatiquement
J’ai mis en place un workflow GitHub Actions qui se lance à chaque fois que je pousse une nouvelle version du site sur la branche principale. Ce workflow copie automatiquement les fichiers dans mon bucket S3.





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
      - run: aws s3 sync . s3://mon-site-devops --delete
Pourquoi ?
Ça me permet d’automatiser le déploiement, éviter les erreurs humaines et gagner du temps.




6. Stockage des clés AWS dans GitHub Secrets
Pour que mon workflow ait les accès nécessaires, j’ai mis mes clés AWS dans la partie secrète (Secrets) de GitHub. Comme ça mes clés ne sont pas visibles dans le code.

Pourquoi ?
C’est une bonne pratique de sécurité pour protéger mes identifiants et éviter les risques de piratage.



7. Test du fonctionnement
Après chaque mise à jour, je vérifie que le site est bien accessible et que le déploiement fonctionne sans problème.

Pourquoi ?
Tester permet d’être sûr que mon travail marche bien, et que le site est toujours disponible pour les visiteurs.
