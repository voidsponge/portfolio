---
layout: post
title: "Altissia Bot : Intelligent Language Exercise Automation"
date: 2024-01-08
categories: [Automation, Python, Playwright, EdTech]
tags: [automation, python, playwright, selenium-alternative, bot]
image: https://img.shields.io/badge/status-active-success?style=for-the-badge
description: "Un bot intelligent pour automatiser les exercices de langue Altissia avec une stratégie à double phase."
---

# 🤖 Altissia Bot : Intelligent Language Exercise Automation

![Python](https://img.shields.io/badge/python-3.7+-blue.svg)
![Playwright](https://img.shields.io/badge/playwright-1.40+-green.svg)
![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)
![Code style](https://img.shields.io/badge/code%20style-black-000000.svg)

[:simple-github: Voir le code sur GitHub](https://github.com/voidsponge/altiez){ .md-button .md-button--primary }

---
!!! danger "⚠️ DISCLAIMER"
Ce projet est développé à des fins éducatives uniquement. Utilisez-le de manière responsable et en conformité avec les conditions d'utilisation d'Altissia. **L'auteur décline toute responsabilité en cas d'utilisation abusive.**

## 🚀 Introduction

**Altissia Bot** n'est pas qu'un simple script de clic. C'est une solution d'automatisation **intelligente** conçue pour résoudre les exercices de langue Altissia.

Grâce à une stratégie de **double phase** (Collecte -> Remplissage) et à l'utilisation de **Playwright**, il navigue, détecte les champs, mémorise les bonnes réponses et complète les exercices à une vitesse surhumaine, tout en contournant les mécanismes de détection de bot.

---

## ⚡ Fonctionnalités Clés

| Module | Description |
| :--- | :--- |
| **📝 Two-Phase Strategy** | Phase 1 : Collecte les réponses en échouant volontairement. Phase 2 : Remplit tout parfaitement. |
| **🧠 Smart Selectors** | Détection robuste des champs via CSS et Text Selectors, résistant aux changements de DOM mineurs. |
| **🕵️ Anti-Detection** | Navigation pseudo-humaine et gestion des sessions pour éviter les blocages de sécurité. |
| **🌐 Headless Mode** | Fonctionne en arrière-plan sans ouvrir de fenêtre, idéal pour les tâches automatisées discrètes. |
| **💾 Answer Storage** | Mémorisation temporaire des réponses (trous multiples supportés) pour un rejeu immédiat. |
| **🔄 Auto-Retry** | Gestion automatique des erreurs de chargement et des timeouts réseau. |

---

## 🏗️ Architecture Technique

Le bot repose sur une architecture moderne utilisant Playwright :

1.  **Perception (Playwright) :** Analyse du DOM en temps réel pour identifier les questions et les champs de saisie.
2.  **Logique (Python) :** Machine à états qui gère le flux "Login -> Navigation -> Collecte -> Remplissage".
3.  **Action (Input) :** Simulation d'événements clavier et souris réalistes pour interagir avec la plateforme.

---

## 🛠️ Installation & Démarrage

### Prérequis
* Python 3.7+
* Compte Altissia valide

### 1. Installation

**Terminal**
```bash
# Clone the repository
git clone https://github.com/voidsponge/altissia-bot.git
cd altissia-bot

# Install dependencies
pip install .

# Install Playwright browsers
playwright install chromium
```

### 2. Configuration

Copiez le fichier `.env.example` en `.env` et ajoutez vos identifiants :

**Configuration (.env)**
```env
ALTISSIA_USERNAME=your_email@example.com
ALTISSIA_PASSWORD=your_password
ALTISSIA_URL=https://www.altissia.com/
```

---

## 🔥 Modes d'Utilisation

Altissia Bot s'adapte à vos besoins, que vous souhaitiez superviser ou laisser faire.

### ⌨️ Mode Interactif

Recommandé pour la première utilisation ou pour déboguer. Vous voyez le navigateur s'ouvrir et agir.

**Lancement :**

```bash
python run.py
```

### 👻 Mode Headless (Invisible)

Idéal pour l'automatisation en arrière-plan une fois que tout est configuré.

**Lancement :**

```bash
python run.py --headless
```

### 🤖 Mode Automatique

Pour cibler un exercice spécifique sans intervention.

**Lancement :**

```bash
python run.py --auto --exercise 1
```

**Exemple de sortie :**
```text
✅ 10 questions collected!

📋 COLLECTED ANSWERS:
  Question 1 : House of Commons
  Question 2 : freedom / expression (2 blanks)
  Question 3 : Parliament
  ...

🔄 PHASE 2: Automatic filling
ℹ️  Return MANUALLY to the start of the exercise
```

---

## 💻 Stack Technologique

* 🐍 **Python 3.7+** (Core Logic)
* 🎭 **Playwright** (Browser Automation)
* 🔧 **Black & Flake8** (Code Quality)
* 📦 **Pip** (Dependency Management)

---

*Projet réalisé par Elhoyr / Tokzen / VoidSponge*

https://github.com/Elohyrr
https://github.com/Tokzeen
