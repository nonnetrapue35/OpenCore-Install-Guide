---
home: true
heroImage: /dortania-logo-clear.png
heroText: Dortania's OpenCore Install Guide
actionText: Getting Started→
actionLink: prerequisites.md

meta:
- name: description
  content: Current supported version 0.7.0
---

# Qu'est-ce qu'OpenCore et à qui s'adresse ce guide ?

OpenCore est ce que nous appelons un "chargeur de démarrage" - c'est un logiciel complexe que nous utilisons pour préparer nos systèmes à macOS - spécifiquement en injectant de nouvelles données pour macOS telles que SMBIOS, les tables ACPI et les kexts. La différence entre cet outil et d'autres comme Clover est qu'il a été conçu dans un souci de sécurité et de qualité, ce qui nous permet d'utiliser de nombreuses fonctions de sécurité présentes sur les vrais Macs, telles que [Protection de l'Intégrité du Système](https://support.apple.com/en-ca/HT204899) and [FileVault](https://support.apple.com/en-ca/HT204837). Un aperçu plus approfondi est disponible ici :[Pourquoi OpenCore plutôt que Clover et d'autres](why-oc.md)

Ce guide se concentre sur deux points principaux :

* Installer macOS sur un PC basé sur X86
* Vous apprendre ce qui fait fonctionner votre Hack

Pour cette raison, vous devrez lire, apprendre et même utiliser Google. Il ne s'agit pas d'une simple installation en un clic.

N'oubliez pas qu'OpenCore est encore nouveau et actuellement en version bêta. Bien qu'il soit assez stable, et sans doute beaucoup plus stable que Clover dans presque tous les domaines, il est encore fréquemment mis à jour, et des parties de la configuration changent assez souvent (c'est-à-dire que de nouvelles particularités peuvent remplacer d'autres).

Enfin, les personnes qui ont des problèmes peuvent se rendre à la fois au [r/subreddit Hackintosh ](https://www.reddit.com/r/hackintosh/) et [r/Discord Hackintosh ](https://discord.gg/u8V7N5C) pour plus d'aide.
