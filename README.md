EMADISSON Aina L1C 265 

Petit resumé des commandes Docker et leurs utilisation:

  # 1)Gestion des conteneurs
    
## Lancer un conteneur
docker run -d --name mon-conteneur -p 80:80 nginx

## Voir les conteneurs en cours
docker ps
docker ps -a  # Voir tous les conteneurs

## Arrêter/démarrer un conteneur
docker stop mon-conteneur
docker start mon-conteneur
docker restart mon-conteneur

## Supprimer un conteneur
docker rm mon-conteneur
docker rm -f mon-conteneur  # Forcer la suppression

## Voir les logs
docker logs mon-conteneur

## Suivre en temps réel
docker logs -f mon-conteneur  

  
  # 2)Gestion des images

## Lister les images
docker images

## Télécharger une image
docker pull nginx:latest

## Construire une image
docker build -t mon-image:latest .

## Supprimer une image
docker rmi mon-image:latest

## Forcer la suppression
docker rmi -f mon-image:latest

  # 3) Netoyage
## Nettoyer les conteneurs arrêtés
docker container prune

## Nettoyer les images non utilisées
docker image prune

## Nettoyer tout le système
docker system prune

  # 4)Deploiement de site sur Docker

## Structure de base d'un fichier prêt au deploiement
   mon-site/
├── Dockerfile
├── docker-compose.yml
├── nginx.conf
├── src/
│   └── (fichiers du site)
└── README.md

## Dockerfile 
  ### Dockerfile pour un simple (html css js...)
FROM nginx:alpine
COPY src/ /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

  ### Dockerfile pour des sites plus avancé avec fichier json 
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

## Docker-commpose.yml (orchestre des conteneurs afin de gerer plusieurs services sur Docker)
version: '3.8'
services:
  web:
    build: .
    ports:
      - "80:80"
    volumes:
      - ./src:/usr/share/nginx/html
    restart: unless-stopped
    environment:
      - NODE_ENV=production
  database:
    image: postgres:13
    environment:
      POSTGRES_DB: monapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db_data:/var/lib/postgresql/data
volumes:
  db_data

  ## Depoiement avec Docker (dans cmd ou terminal)

 ### Construire l'image
docker build -t mon-site:latest .

 ### Tester localement
docker run -p 8080:80 mon-site:latest

 ### Pousser vers un registry (Docker Hub, GitLab, etc.)
docker tag mon-site:latest monusername/mon-site:latest
docker push monusername/mon-site:latest

### Déployer sur le serveur
docker pull monusername/mon-site:latest
docker run -d --name mon-site -p 80:80 monusername/mon-site:latest

  ## Deploiement avec Docker-compose

### Déployer
docker-compose up -d

### Arrêter
docker-compose down

### Mettre à jour
docker-compose pull
docker-compose up -d

### Voir les logs
docker-compose logs -f

   # 5) Maintenance 
## Voir l'utilisation des ressources
docker stats

## Inspecter un conteneur
docker inspect mon-conteneur

## Exécuter une commande dans un conteneur
docker exec -it mon-conteneur bash

## Sauvegarder une base de données
docker exec mon-db pg_dump -U user database > backup.sql
