# Pourquoi OpenCore plutôt que Clover et autres

Cette section contient un bref aperçu de la raison pour laquelle la communauté a fait la transition vers OpenCore, et vise à dissiper quelques mythes communs dans la communauté. Ceux qui veulent juste une machine macOS peuvent sauter cette page.

[[toc]]

## Fonctionnalités d'OpenCore

* Plus de support OS !
  * OpenCore supporte maintenant plus de versions d'OS X et de macOS en natif, sans les douloureux hacks que Clover et Chameleon ont dû mettre en place.
  * Cela inclut des systèmes d'exploitation aussi anciens que 10.4, Tiger, et même les dernières versions de 11, Big Sur !
* En moyenne, les systèmes OpenCore démarrent plus rapidement que ceux utilisant Clover car moins de correctifs inutiles sont effectués.
* Meilleure stabilité globale car les correctifs peuvent être beaucoup plus précis :
  * [mise à jour macOS 10.15.4](https://www.reddit.com/r/hackintosh/comments/fo9bfv/macos_10154_update/)
  * Les correctifs AMD OSX ne nécessitent pas de mise à jour à chaque mise à jour de sécurité mineure.
* Meilleure sécurité globale sous plusieurs formes :
  * Pas besoin de désactiver la Protection de l'Intégrité du Système (SIP)
  * Support intégré de FileVault 2
  * [Vaulting](https://dortania.github.io/OpenCore-Post-Install/universal/security.html#Vault) permettant de créer des snapshots EFI empêchant toute modification non désirée.
 * Prise en charge réelle du démarrage sécurisé
    * UEFI et la variante d'Apple
* La commutation BootCamp et la sélection du périphérique de démarrage sont prises en charge par la lecture des variables NVRAM définies par Startup Disk, comme sur un vrai Mac.
* Supporte les raccourcis clavier de démarrage via `boot.efi` - maintenez `Option` ou `ESC` au démarrage pour choisir un périphérique de démarrage, `Cmd+R` pour entrer en restauration ou `Cmd+Opt+P+R` pour réinitialiser la NVRAM.

### Support logiciel

La principale raison pour laquelle quelqu'un peut vouloir passer d'un autre chargeur de démarrage à un autre est en fait le support logiciel :

* Les kext ne sont plus testés pour Clover :
  * Vous avez un problème avec un kext ? De nombreux développeurs, y compris le groupe [Acidanthera](https://github.com/acidanthera) (créateur de la plupart de vos kexts préférés) ne fournira pas de support à moins qu'il ne s'agisse d'OpenCore.
* * De nombreux pilotes de firmware ont été fusionnés dans OpenCore :
  * [Support APFS](https://github.com/acidanthera/AppleSupportPkg)
  * [Support FileVault](https://github.com/acidanthera/AppleSupportPkg)
  * [Corrections du firmware](https://github.com/acidanthera/AptioFixPkg)
* [Patches AMD OSX](https://github.com/AMD-OSX/AMD_Vanilla/tree/opencore):
  * Vous avez du matériel basé sur AMD ? Les correctifs du noyau nécessaires pour démarrer macOS ne prennent plus en charge Clover - ils ne prennent désormais en charge qu'OpenCore.

### Injection de Kext 

Pour mieux comprendre le système d'injection de kext d'OpenCore, nous devons d'abord examiner le fonctionnement de Clover :

1. Correctifs SIP ouverts
2. Correctifs pour activer le code zombie de XNU pour l'injection de kext.
3. Correctif de la condition de course avec l'injection de kext
4. Injection de kexts
5. Rattachement de SIP

Les choses à noter avec la méthode de Clover :

* Appeler le code zombie de XNU qui n'a pas été utilisé depuis 10.7, c'est vraiment impressionnant qu'Apple n'ait pas encore supprimé ce code.
  * Les mises à jour de l'OS cassent souvent ce patch, comme récemment avec les versions 10.14.4 et 10.15.
* Désactive SIP et tente de le réactiver, je pense qu'il n'y a pas grand chose à dire.
* Probabilité de rupture avec macOS 11.0 (Big Sur)
* Supporte OS X jusqu'à la version 10.5

Voyons maintenant la méthode d'OpenCore :

1. Prend le noyau existant pré-relié et les kexts prêts à être injectés.
2. Reconstruit le cache dans l'environnement EFI avec les nouveaux kexts.
3. Ajoute ce nouveau cache

Les choses à noter avec la méthode d'OpenCore :

* Agnostique au système d'exploitation car le format du noyau prélié est resté le même depuis 10.6 (v2), beaucoup plus difficile à casser.
  * OpenCore supporte également le noyau pré-connecté (v1, trouvé dans 10.4 et 10.5), sans cache, Mkext et KernelCollections, ce qui signifie qu'il supporte toutes les versions Intel de OS X/macOS.
* Meilleure stabilité car il y a beaucoup moins de correctifs à apporter.

## Les défauts d'OpenCore

La majorité des fonctionnalités de Clover sont en fait supportées dans OpenCore sous la forme d'une particularité. Cependant, lors de la transition, vous devez prêter une attention particulière aux fonctionnalités manquantes d'OpenCore car cela peut vous affecter ou non :

* Ne supporte pas le démarrage des systèmes d'exploitation basés sur MBR.
  * La solution consiste à charger en chaîne REFInd une fois dans OpenCore.
* Ne supporte pas les patchs VBIOS basés sur l'UEFI.
  * Ceci peut être fait dans macOS cependant
* Ne prend pas en charge l'injection automatique de DeviceProperty pour les anciens GPU.
  * c'est-à-dire InjectIntel, InjectNvidia et InjectAti.
  * Ceci peut toutefois être fait manuellement : [Correctifs GPU](https://dortania.github.io/OpenCore-Post-Install/gpu-patching/)
* Ne supporte pas le patch de conflit d'IRQ
  * Peut être résolu avec [SSDTTime](https://github.com/corpnewt/SSDTTime)
* Ne prend pas en charge la génération des états P et C pour les anciens processeurs.
* Ne prend pas en charge les correctifs ACPI de Target Bridge.
* Ne supporte pas l'injection d'UUID matériel
* Ne prend pas en charge l'auto-détection pour de nombreux chargeurs de démarrage Linux.
  * Peut être résolu en ajoutant une entrée dans `BlessOverride`
* Ne prend pas en charge la plupart des correctifs XCPM de Clover.
  * c'est-à-dire les correctifs XCPM Ivy Bridge
* Ne permet pas de cacher des lecteurs spécifiques
* Ne supporte pas la modification des paramètres dans le menu d'OpenCore
* Ne corrige pas la valeur UID de PCIRoot
* Ne supporte pas l'injection et la correction de l'ACPI pour macOS seulement.

## Les mythes courants (en cours de traduction)

### Is OpenCore unstable as it's a beta?

Short Answer: No

Long Answer: No

OpenCore's version number does not represent the quality of the project. Instead, it's more of a way to see the stepping stones of the project. Acidanthera still has much they'd like to do with the project including overall refinement and more feature support.

For example, OpenCore goes through proper security audits to ensure it complies with UEFI Secure Boot, and is the only Hackintosh bootloader to undergo these rigorous reviews and have such support.

Version 0.6.1 was originally designed to be the official release of OpenCore as it would have proper UEFI/Apple Secure Boot, and would be the 1 year anniversary of OpenCore's release as a public tool. However, due to circumstances around macOS Big Sur and the rewriting of OpenCore's prelinker to support it, it was decided to push off 1.0.0 for another year.

Current road map:

* 2019: Year of Beta
* 2020: Year of Secure Boot
* 2021: Year of Refinement

So please do not see the version number as a hindrance, instead as something to look forward to.

### Does OpenCore always inject SMBIOS and ACPI data into other OSes?

By default, OpenCore will assume that all OSes should be treated equally in regards to ACPI and SMBIOS information. The reason for this thinking consists of three parts:

* This allows for proper multiboot support, like with [BootCamp](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootcamp.html)
* Avoids poorly made DSDTs and encourages proper ACPI practices
* Avoids edge cases where info is injected several times, commonly seen with Clover
  * i.e. How would you handle SMBIOS and ACPI data injection once you booted boot.efi, but then get kicked out? The changes are already in memory and so trying to undo them can be quite dangerous. This is why Clover's method is frowned upon.

However, there are quirks in OpenCore that allow for SMBIOS injection to be macOS-limited by patching where macOS reads SMBIOS info from. The `CustomSMIOSGuid` quirk with `CustomSMBIOSMode` set to `Custom` can break in the future and so we only recommend this option in the event of certain software breaking in other OSes. For best stability, please disable these quirks.

### Does OpenCore require a fresh install?

Not at all in the event you have a "Vanilla" installation – what this refers to is whether the OS has tampered in any way, such as installing 3rd party kexts into the system volume or other unsupported modifications by Apple. When your system has been heavily tampered with, either by you or 3rd party utilities like Hackintool, we recommend a fresh install to avoid any potential issues.

Special note for Clover users: please reset your NVRAM when installing with OpenCore. Many of Clover variables can conflict with OpenCore and macOS.

* Note: Thinkpad laptops are known to be semi-bricked after an NVRAM reset in OpenCore, we recommend resetting NVRAM by updating the BIOS on these machines.

### Does OpenCore only support limited versions of macOS?

As of OpenCore 0.6.2, you can now boot every Intel version of macOS going all the way back to OS X 10.4! Proper support however will depend on your hardware, so please verify yourself: [Hardware Limitations](macos-limits.md)

::: details macOS Install Gallery

Acidanthera has tested many versions, and I myself have run many versions of OS X on my old HP DC 7900 (Core2 Quad Q8300). Here's just a small gallery of what I've tested:

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.4-Tiger.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.5-Leopard.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.6-Snow-Loepard.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.7-Lion.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.8-MountainLion.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.9-Mavericks.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.10-Yosemite.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.12-Sierra.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.13-HighSierra.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.15-Catalina.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/11-Big-Sur.png)

:::

### Does OpenCore support older hardware?

As of right now, the majority of Intel hardware is supported so long as the OS itself does! However please refer to the [Hardware Limitations page](macos-limits.md) for more info on what hardware is supported in what versions of OS X/macOS.

Currently, Intel's Yonah and newer series CPUs have been tested properly with OpenCore.

### Does OpenCore support Windows/Linux booting?

OpenCore works in the same fashion as any other boot loader, so it respects other OSes the same way. For any OSes where their bootloader has an irregular path or name, you can simply add it to the BlessOverride section.

### Legality of Hackintoshing

Where hackintoshing sits is in a legal grey area, mainly that while this is not illegal we are in fact breaking the EULA. The reason this is not illegal:

* We are downloading macOS from [Apple's servers directly](https://github.com/acidanthera/OpenCorePkg/blob/0.6.9/Utilities/macrecovery/macrecovery.py#L125)
* We are doing this as a non-profit organization for teaching and personal use
  * People who plan to use their Hackintosh for work or want to resell them should refer to the [Psystar case](https://en.wikipedia.org/wiki/Psystar_Corporation) and their regional laws

While the EULA states that macOS should only be installed on real Macs or virtual machines running on genuine Macs ([sections 2B-i and 2B-iii](https://www.apple.com/legal/sla/docs/macOSBigSur.pdf)), there is no enforceable law that outright bans this. However, sites that repackage and modify macOS installers do potentially risk the issue of [DMCA takedowns](https://en.wikipedia.org/wiki/Digital_Millennium_Copyright_Act) and such.

* **Note**: This is not legal advice, so please make the proper assessments yourself and discuss with your lawyers if you have any concerns.

### Does macOS support Nvidia GPUs?

Due to issues revolving around Nvidia support in newer versions of macOS, many users have somehow come to the conclusion that macOS never supported Nvidia GPUs and don't at this point. However, Apple actually still maintains and supports Macs with Nvidia GPUs in their latest OS, like the 2013 MacBook Pro models with Kepler GPUs.

The main issue has to do with any newer Nvidia GPUs, as Apple stopped shipping machines with them and thus they never had official OS support from Apple. Instead, users had to rely on Nvidia for 3rd party drivers. Due to issues with Apple's newly introduced Secure Boot, they could no longer support the Web Drivers and thus Nvidia couldn't publish them for newer platforms limiting them to mac OS 10.13, High Sierra.

For more info on OS support, see here: [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
