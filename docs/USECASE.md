1. Contexte organisationnel
Quel ministère / institution ? (Défense, Intérieur, Affaires Étrangères, Sécurité Civile, autre ?)
Qui est votre "sponsor" / commanditaire ? (Direction, département, service)
Y a-t-il plusieurs ministères impliqués ? (Défense + Intérieur par exemple)
---
2. Utilisateurs finaux
Combien d'utilisateurs simultanés ? (5 ? 50 ? 500 ?)
Niveaux d'accès ? (Lecture seule / analyses / injection données ?)
Qui décide des alertes à déclencher ? (Algorithme ou validation humaine ?)
Existe-t-il un workflow de crise défini ? (escalade, notification, action)

---
3. Objectifs opérationnels
Scénario d'usage n°1 : Détection d'anomalie ; Exemple : "Un aéroport français a un pic anormal de mouvements militaires → alerter le préfet"
Scénario d'usage n°2 : Renseignement géopolitique ; Exemple : "Affichage en temps réel de présences militaires étrangères en Méditerranée → aide à décision diplomatique"
Scénario d'usage n°3 : Surveillance infrastructure critique ; Exemple : "Suivi de la sécurité des centrales nucléaires (statut, incidents) + incidents réseau = détecter cyberattaque"
Scenario d'usage n°4 : Support opérationnel d'événement ; Exemple : "JO 2024 : suivi des mouvements transport, sécurité, contexte géopolitique sur 1 région"
 
---
4. Données & sources
Qui fournit les données ? (partenaires gouv, API publiques, partenaires privés)
Format des données ? (API temps-réel, fichiers batch, feeds RSS, bases DB)
Classification de l'info ? (Diffusion Restreinte, Limité à l'Établissement, confidentiel défense)
Latence acceptable ? (temps-réel -1s ? 5min ? 1h ?)
Complétude requise ? (couverture 100% France ou zones prioritaires ?)
CCTV publiques : gares, métro, centre-ville (RGPD ?) → quelle utilité réelle ?
Réseaux sociaux : Telegram FR, Twitter/X OSINT → détection précoce ?
Sources médias : agrégation AFP, Reuters, journalistes locaux
Signalement citoyen : crowdsourcing (comme Waze sécurité) ?
Données financières : mouvements suspects liés à financement terrorisme (TRACFIN) ?
---
5. Périmètre géographique
Y a-t-il des zones de défense (ZD) à couvrir en priorité ?
Les DROM-COM (Réunion, Guadeloupe, Guyane) sont concernés ?
Besoin de coopération alliance NATO / EU sur espaces partagés (Méditerranée) ?
Métropole française uniquement (+ Corse ?)
Zones côtières étendues (200 km au large ?)
Espace aérien France + approches (FIR Brest, Marseille ?)
Europe entière (pour contexte géopolitique)
Monde entier (surveillance présences étrangères partout ?)
Zones d'intérêt spécifiques (Afrique du N, Méditerranée, Arctique ?)
