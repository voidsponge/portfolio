# 💀 PwnIA : Autonomous Offensive AI Agent (v8)

![Version](https://img.shields.io/badge/version-8.0-red?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.11-blue?style=for-the-badge&logo=python)
![Docker](https://img.shields.io/badge/Docker-Kali_Rolling-blue?style=for-the-badge&logo=docker)
![AI](https://img.shields.io/badge/Model-Gemini_2.5_Flash-orange?style=for-the-badge&logo=google)

[:simple-github: Voir le code sur GitHub](https://github.com/voidsponge/pwnia){ .md-button .md-button--primary }

---

!!! danger "⚠️ DISCLAIMER"
    Ce projet est une preuve de concept (PoC) développée à des fins éducatives et de recherche en cybersécurité. Il est conçu pour être utilisé uniquement sur des environnements autorisés (CTF, Cyber Ranges, Réseaux privés). **L'auteur décline toute responsabilité en cas d'utilisation malveillante.**

## 🚀 Introduction

**PwnIA** n'est pas un simple script d'automatisation. C'est un **Agent Autonome** capable de conduire un audit de sécurité offensif (Red Teaming) de bout en bout.

Piloté par le modèle **Gemini 2.5 Flash**, il possède des "yeux" pour voir le Web, des "mains" pour exécuter des outils Kali Linux, et une "mémoire" pour ne jamais perdre le fil de sa mission.

Il peut agir en **autonomie totale** ou devenir votre **binôme de hacking** (CTF Companion) via une interface de chat interactive.

---

## ⚡ Fonctionnalités Clés

| Module | Description |
| :--- | :--- |
| **🧠 Mission Brain** | Gestion d'état persistante. L'agent sait toujours où il en est (Ports, Vulns, Loot) et apprend de ses erreurs. |
| **💬 Interactive Shell** | Mode conversationnel pour discuter stratégie, demander des scans précis et résoudre des CTF en duo. |
| **👁️ Computer Vision** | Utilisation de **Selenium** pour capturer et analyser visuellement les pages Web cibles. |
| **⚡ Nuclei Scanner** | Intégration du scanner le plus rapide du marché pour détecter les failles Web critiques. |
| **☢️ Metasploit RPC** | Pilotage complet du framework Metasploit pour lancer des exploits complexes. |
| **💾 Save & Resume** | Sauvegarde contextuelle complète : l'agent "se souvient" de la session précédente après un redémarrage. |
| **📝 Auto-Reporting** | Génération automatique d'un rapport HTML professionnel en fin de mission. |

---

## 🏗️ Architecture Technique

L'agent repose sur une architecture modulaire dockerisée :

1.  **Perception (Input) :** Nmap (Réseau), Nuclei (Web), Vision (Screenshots).
2.  **Cognition (LLM) :** Gemini 2.5 analyse les résultats et consulte sa **Mémoire RAG** (ChromaDB) remplie de techniques de hacking.
3.  **Décision :** L'agent met à jour son plan d'attaque dans le `MissionBrain`.
4.  **Action (Output) :** Exécution de scripts Python générés à la volée ou de commandes Shell.

---

## 🛠️ Installation & Démarrage

### Prérequis
* Docker & Docker Compose
* Une clé API Google Gemini (`GOOGLE_API_KEY`)

### 1. Installation

```bash title="Terminal"
git clone https://github.com/voidsponge/pwnia.git
cd pwnia
docker build -t pwnia-gold .

```

### 2. Lancement de l'environnement avec dashboard

```bash title="Docker Run"
docker run -it --rm --network host \
  -v $(pwd)/pwn_memory:/app/chroma_db \
  -v $(pwd):/app \
  -e GOOGLE_API_KEY="TA_CLE_ICI" \
  -p 8501:8501 \
  pwnia-gold bash

```

### 3. Lancement de l'environnement en Shell

```bash title="Docker Run"
docker run -it --rm --network host \
  -v $(pwd)/pwn_memory:/app/chroma_db \
  -v $(pwd):/app \
  -e GOOGLE_API_KEY="TA_CLE_ICI" \
  pwnia-gold

```

---

## 🔥 Modes d'Utilisation

PwnIA s'adapte à votre style de travail. Choisissez votre interface :

=== "⌨️ Mode Interactif (CLI)"

```
Idéal pour les **CTF** et le travail de précision. Vous discutez avec l'agent dans le terminal. Il agit comme un copilote expert.

**Lancement :**

docker run -it --rm --network host \
  -v $(pwd)/pwn_memory:/app/chroma_db \
  -v $(pwd):/app \
  -e GOOGLE_API_KEY="TA_CLE_ICI" \
  pwnia-gold

```

**Exemple de session :**
```text
(PwnIA) > Ready. Waiting for orders.
User > On s'attaque à la box 10.10.11.23. Commence par un scan rapide.

(PwnIA) > [RUNNING] nmap -sC -sV -p- --min-rate 1000 10.10.11.23
(PwnIA) > Scan terminé. Ports ouverts : 22 (SSH), 80 (HTTP).
          Le port 80 héberge un "Apache 2.4.41".
          Veux-tu que je lance Nuclei ou que j'inspecte le code source ?

User > Inspecte la page et sauvegarde l'état, je reprends demain.

(PwnIA) > [VISION] Screenshot pris. Analyse en cours...
          [MEMORY] État sauvegardé dans 'session_ctf_box1.json'. À demain !
```

=== "🖥️ Mode Dashboard (Auto)"

```
Idéal pour le **Red Teaming** de masse ou la surveillance. L'agent travaille seul et affiche les résultats sur une interface web.

**Lancement :**
docker run -it --rm --network host \
  -v $(pwd)/pwn_memory:/app/chroma_db \
  -v $(pwd):/app \
  -e GOOGLE_API_KEY="TA_CLE_ICI" \
  -p 8501:8501 \
  pwnia-gold bash

streamlit run dashboard.py
python3 pwn_agent.py 
```

Rendez-vous sur `http://localhost:8501`.

* **Auto-Pilot :** Entrez simplement la cible "auto "ip" ou "url", l'agent gère tout (Recon -> Exploitation -> Report).
* **Live Feed :** Voir les actions et les captures d'écran en temps réel.

---

## 🧠 Mémoire & Persistance

L'une des plus grandes forces de **PwnIA** est sa capacité à apprendre et à retenir l'information.

### Sauvegarde de Contexte (Long-term Memory)

Contrairement aux scripts classiques qui oublient tout à la fermeture, PwnIA utilise une base de données vectorielle (ChromaDB) et des fichiers JSON d'état.

* **Commandes de mémoire :**
* `save <nom_session>` : Snapshot complet de la connaissance actuelle (IPs, technos, mots de passe trouvés).
* `load <nom_session>` : Restaure l'agent exactement là où vous l'avez laissé.
* `learn <fichier>` : Donne un write-up ou une doc technique à l'agent pour qu'il apprenne une nouvelle technique d'attaque spécifique pour le futur.



---

## 🛡️ Sécurité & Éthique

* **Pas de Persistance Malveillante :** L'agent est configuré pour l'audit. Il ne crée pas de backdoors persistantes sur les cibles.
* **Sandbox Docker :** L'agent tourne dans un conteneur isolé pour éviter toute fuite vers le système hôte.
* **Human-in-the-loop :** En mode interactif, aucune commande destructrice n'est lancée sans validation explicite.

---

## 💻 Stack Technologique

* 🐍 **Python 3.11** (Core Logic)
* 🐳 **Docker** (Environment Kali Linux)
* 🧠 **Google Gemini** (Reasoning & Code Gen)
* 🕷️ **Selenium** (Computer Vision)
* 🗄️ **ChromaDB** (RAG Memory)
* ⚡ **Nuclei & Metasploit** (Offensive Tools)

---

*Projet réalisé par VoidSponge.*
