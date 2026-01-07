
# 🛡️ Tutoriel : Déployer un SOC "Ready-to-Use" avec Elastic Stack 8.x & Docker

## 🎯 Objectif

Déployer en quelques minutes une infrastructure de Security Operation Center (SOC) capable de collecter, analyser et visualiser des logs en temps réel.

Le défi technique : Ce guide contourne la complexité de la sécurité native d'Elastic 8.x (qui interdit désormais l'usage du compte elastic pour Kibana) en implémentant l'authentification moderne par Service Account Token.

## 🏗️ Architecture

-   **Elasticsearch 8.11** : Stockage et indexation.
    
-   **Logstash** : Pipeline d'ingestion universel (TCP & Beats).
    
-   **Kibana** : Interface SIEM pour l'investigation.
    
-   **Docker** : Conteneurisation complète.
    

----------

## ⚡ Étape 1 : Préparation de l'environnement (Setup Rapide)

Nous allons créer l'arborescence et générer les fichiers de configuration Logstash en une seule commande pour éviter les erreurs de montage (Read-Only) classiques sous Docker.

**Copiez-collez ce bloc dans votre terminal :**

Bash

```
# 1. Création de l'arborescence
mkdir -p ~/soc-elk/logstash/config
mkdir -p ~/soc-elk/logstash/pipeline
mkdir -p ~/soc-elk/filebeat

# 2. Config Système Logstash (Fix obligatoire pour Docker)
cat <<EOF > ~/soc-elk/logstash/config/logstash.yml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]
EOF

# 3. Pipeline d'ingestion (Port 5000 pour tests, 5044 pour Agents)
cat <<EOF > ~/soc-elk/logstash/pipeline/logstash.conf
input {
  tcp {
    port => 5000
    codec => json_lines
  }
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    user => "elastic"
    password => "MonSuperMotDePasse123!"
    index => "logstash-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
EOF

# 4. Création du fichier Docker Compose vide
touch ~/soc-elk/docker-compose.yml

# 5. Accès au dossier
cd ~/soc-elk

echo "✅ Environnement prêt ! Passons à la configuration Docker."

```

----------

## 🐳 Étape 2 : Le Manifeste Docker Compose

Ouvrez le fichier `docker-compose.yml` (`nano docker-compose.yml`) et collez la configuration suivante.

**Points clés de cette config :**

1.  **Sécurité** : Utilisation de `ELASTICSEARCH_SERVICEACCOUNTTOKEN` au lieu de `USERNAME` pour Kibana.
    
2.  **Stabilité** : Montage des volumes Logstash en mode `rw` (lecture-écriture).
    

YAML

```
version: '3.7'

services:
  # --- ELASTICSEARCH ---
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.1
    container_name: soc-elasticsearch
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
      - xpack.security.enabled=true
      - xpack.security.http.ssl.enabled=false # Désactivé pour simplifier le lab local
      - ELASTIC_PASSWORD=MonSuperMotDePasse123!
    ports:
      - "9200:9200"
    networks:
      - elk

  # --- LOGSTASH ---
  logstash:
    image: docker.elastic.co/logstash/logstash:8.11.1
    container_name: soc-logstash
    volumes:
      # Mode RW indispensable pour l'init de Logstash
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml:rw
      - ./logstash/pipeline:/usr/share/logstash/pipeline:ro
    ports:
      - "5044:5044"
      - "5000:5000"
    depends_on:
      - elasticsearch
    networks:
      - elk

  # --- KIBANA ---
  kibana:
    image: docker.elastic.co/kibana/kibana:8.11.1
    container_name: soc-kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
      # ⚠️ C'est ici que la magie opère : Token au lieu du user root
      - ELASTICSEARCH_SERVICEACCOUNTTOKEN=${KIBANA_TOKEN}
      # Clés de chiffrement (Obligatoires depuis la v8)
      - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=dzhwrt74f6859g9d5746367859g9d574
      - XPACK_REPORTING_ENCRYPTIONKEY=dzhwrt74f6859g9d5746367859g9d574
      - XPACK_SECURITY_ENCRYPTIONKEY=dzhwrt74f6859g9d5746367859g9d574
    networks:
      - elk

networks:
  elk:
    driver: bridge

```

----------

## 🚀 Étape 3 : Déploiement et Sécurité

Pour que Kibana puisse démarrer, nous devons générer un jeton de confiance valide auprès d'Elasticsearch.

1.  **Démarrer la base de données uniquement :**
    
    Bash
    
    ```
    docker-compose up -d elasticsearch
    
    ```
    
    _(Attendre 30 secondes que le statut passe à "healthy" ou actif)_
    
2.  Générer le Token de Service :
    
    Exécutez cette commande pour créer le lien sécurisé :
    
    Bash
    
    ```
    docker exec -it soc-elasticsearch /usr/share/elasticsearch/bin/elasticsearch-service-tokens create elastic/kibana kibana-token
    
    ```
    
    👉 **Copiez la chaîne de caractères retournée.**
    
3.  Lancer la stack complète :
    
    Remplacez <VOTRE_TOKEN> par la chaîne copiée :
    
    Bash
    
    ```
    export KIBANA_TOKEN="<VOTRE_TOKEN>"
    docker-compose up -d
    
    ```
    

----------

## 🧪 Étape 4 : Validation du SOC (Le "Smoke Test")

Une infrastructure sans données ne sert à rien. Voici comment valider le flux de bout en bout.

### 1. Le "Ping" Logistique

Simulons une application envoyant un log critique via le réseau.

Depuis votre terminal :

Bash

```
echo '{"app": "SOC-Test", "message": "Flux de données opérationnel", "level": "INFO"}' | nc localhost 5000

```

_Si la commande passe sans erreur, le port 5000 est ouvert et Logstash écoute._

### 2. Configuration de la Vue dans Kibana

1.  Allez sur **[http://localhost:5601](https://www.google.com/search?q=http://localhost:5601)**
    
    -   User : `elastic`
        
    -   Pass : `MonSuperMotDePasse123!`
        
2.  Menu **☰** > **Stack Management** > **Data Views**.
    
3.  Cliquez sur **Create data view**.
    
4.  Nom : `logstash-*` (Kibana doit trouver l'index créé par notre test précédent).
    
5.  Timestamp : Sélectionnez `@timestamp`.
    

### 3. Visualisation (Attention au piège !)

1.  Allez dans le menu **Discover**.
    
2.  ⚠️ Important : Par défaut, Kibana affiche les "Last 15 minutes".
    
    👉 Changez la période (en haut à droite) sur "Today".
    
3.  Vous verrez apparaître votre log de test :
    
    -   `message`: "Flux de données opérationnel"
        

----------

## 🔮 Bonus : Aller plus loin (Vrais Logs)

Votre SOC est prêt. Pour ingérer de vrais logs (SSH, Système), il suffit d'ajouter le conteneur **Filebeat** au `docker-compose.yml` :

YAML

```
  filebeat:
    image: docker.elastic.co/beats/filebeat:8.11.1
    user: root
    volumes:
      - /var/log:/var/log:ro            # Lecture des logs hôte
      - /var/run/docker.sock:/var/run/docker.sock:ro
    command: filebeat -e -E output.elasticsearch.enabled=false -E output.logstash.hosts=['logstash:5044']
    depends_on:
      - logstash
    networks:
      - elk

```

