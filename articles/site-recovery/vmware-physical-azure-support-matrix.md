---
title: Matrice de support pour la réplication des machines virtuelles VMware et des serveurs physiques vers Azure avec Azure Site Recovery | Microsoft Docs
description: Répertorie les systèmes d’exploitation pris en charge et les composants dédiés à la réplication des machines virtuelles VMware et du serveur physique vers Azure à l’aide d’Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: raynew
ms.openlocfilehash: f6f1ceb4f9afaaeb0a436879802144bdebc06280
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378650"
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Matrice de support pour la réplication des machines virtuelles VMware et des serveurs physiques vers Azure

Cet article répertorie les composants et les paramètres pris en charge pour la reprise après sinistre de machines virtuelles VMware vers Azure, avec [Azure Site Recovery](site-recovery-overview.md).

Pour commencer à utiliser Azure Site Recovery avec le scénario de déploiement la plus simple, consultez nos [didacticiels](tutorial-prepare-azure.md). Cliquez [ici](vmware-azure-architecture.md) pour en savoir plus sur l’architecture Azure Site Recovery.

## <a name="replication-scenario"></a>Scénario de réplication

**Scénario** | **Détails**
--- | ---
Machines virtuelles VMware | Réplication de machines virtuelles VMware locales dans Azure. Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Serveurs physiques | Réplication de serveurs physiques Windows/Linux locaux sur Azure. Vous pouvez déployer ce scénario dans le portail Azure.

## <a name="on-premises-virtualization-servers"></a>Serveurs de virtualisation locaux

**Serveur** | **Configuration requise** | **Détails**
--- | --- | ---
VMware | Serveur vCenter 6.7, 6.5, 6.0 ou 5.5, ou vSphere 6.7, 6.5, 6.0 ou 5.5 | Nous vous recommandons d’utiliser une instance de serveur vCenter.<br/><br/> Nous vous recommandons d’héberger les hôtes vSphere et les serveurs vCenter dans le même réseau que le serveur de traitement. Par défaut, les composants du serveur de traitement s’exécutent sur le serveur de configuration. Vous devrez donc opter pour le réseau dans lequel vous installez le serveur de configuration, à moins que vous n’installiez un serveur de traitement dédié.
Physique | N/A

## <a name="site-recovery-configuration-server"></a>Serveur de configuration Site Recovery

Le serveur de configuration est une machine locale qui exécute les composants de Site Recovery, comprenant le serveur de configuration, le serveur de traitement et le serveur cible maître. Pour la réplication de machines virtuelles VMware, vous installez le serveur de configuration avec tous les éléments requis, en utilisant un modèle OVF pour créer une machine virtuelle VMware. Dans le cas de la réplication de serveurs physiques, vous installez la machine du serveur de configuration manuellement.

**Composant** | **Configuration requise**
--- |---
Cœurs d’unité centrale | 8
RAM | 16 Go
Nombre de disques | 3 disques<br/><br/> Les disques comprennent le disque du système d’exploitation, le disque de cache du serveur de traitement et le lecteur de rétention pour la restauration automatique.
Espace disque libre | 600 Go d’espace requis pour le cache du serveur de traitement.
Espace disque libre | 600 Go d’espace requis pour le lecteur de rétention.
Système d’exploitation  | Windows Server 2012 R2 ou Windows Server 2016 |
Paramètres régionaux du système d’exploitation | Anglais (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") doit être installé.
Rôles Windows Server | N’activez pas les éléments suivants : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Stratégies de groupe| N’activez pas les éléments suivants : <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> [En savoir plus](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assurez-vous d’effectuer les tâches suivantes :<br/><br/> - Vérifier l’absence d’un site web par défaut préexistant <br> - Activer [l’authentification anonyme](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Activer le paramètre [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Vérifier qu’aucune application/aucun site web préexistants n’écoutent le port 443<br>
Type de carte réseau | VMXNET3 (en cas de déploiement comme machine virtuelle VMware)
Type d’adresse IP | statique
Ports | 443 utilisé pour l’orchestration du canal de contrôle<br>9443 utilisé pour le transport de données

## <a name="replicated-machines"></a>Machines répliquées

Site Recovery assure la réplication de toutes les charges de travail exécutées sur une machine prise en charge.

**Composant** | **Détails**
--- | ---
Paramètres de la machine | Les ordinateurs qui répliquent vers Azure doivent répondre aux [conditions requises par Azure](#azure-vm-requirements).
Système d’exploitation Windows | Windows Server 2016 64 bits (Server Core, Server avec Expérience utilisateur), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1. </br></br>  [Windows Server 2008 avec au moins SP2 - 32 bits et 64 bits](migrate-tutorial-windows-server-2008.md) (migration uniquement). </br></br> Windows 2016 Nano Server n’est pas pris en charge.
Système d’exploitation Linux | Red Hat Enterprise Linux : 5.2 à 5.11<b>\*\*</b>, 6.1 à 6.10<b>\*\*</b>, 7.0 à 7.5 <br/><br/>CentOS : 5.2 à 5.11<b>\*\*</b>, 6.1 à 6.10<b>\*\*</b>, 7.0 à 7.5 <br/><br/>Serveur LTS Ubuntu 14.04[ (versions du noyau prises en charge)](#ubuntu-kernel-versions)<br/><br/>Serveur LTS Ubuntu 16.04 [ (versions du noyau prises en charge)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (versions du noyau prises en charge)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (versions du noyau prises en charge)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/></br>* *La mise à niveau de machines répliquées de SUSE Linux Enterprise Server 11 SP3 vers SP4 n’est pas pris en charge. Pour effectuer la mise à niveau, désactivez la réplication, puis réactiver-la après la mise à niveau.*</br></br><b>\*\*</b> *Reportez-vous à [Prise en charge des machines virtuelles Linux dans Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) pour en savoir plus sur la prise en charge de Linux et de la technologie open source dans Azure. Azure Site Recovery vous permet de basculer et d’exécuter les serveurs Linux dans Azure. Toutefois, les fournisseurs Linux peuvent se limiter à la prise en charge des versions de leur distribution qui ne sont pas arrivées en fin de vie.*


>[!NOTE]
>
> - Dans les distributions Linux, seuls les noyaux de stockage qui font partie de la version/mise à jour mineure de distribution sont pris en charge.
>
> - La mise à niveau des machines protégées sur des versions de distribution majeures Linux n’est pas prise en charge. Pour effectuer la mettre à niveau, désactivez la réplication, mettez à niveau le système d’exploitation, puis réactivez la réplication.
>
> - Les serveurs exécutant Red Hat Enterprise Linux 5.2 à 5.11 ou CentOS 5.2 à 5.11 doivent avoir les [composants Linux Integration Services(LIS)](https://www.microsoft.com/en-us/download/details.aspx?id=55106) installés pour que les machines démarrent dans Azure.

### <a name="ubuntu-kernel-versions"></a>Version du noyau Ubuntu


**Version prise en charge** | **Version du service Mobilité Azure Site Recovery** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-generic à 3.13.0-153-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-generic à 3.13.0-153-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-130-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic à 3.13.0-149-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-127-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic à 3.13.0-144-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-119-generic |
|||
LTS 16.04 | 9.19 | 4.4.0-21-generic à 4.4.0-131-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-30-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1019-azure|
LTS 16.04 | 9.18 | 4.4.0-21-generic à 4.4.0-130-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic |
LTS 16.04 | 9.17 | 4.4.0-21-generic à 4.4.0-127-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-43-generic |
LTS 16.04 | 9.16 | 4.4.0-21-generic à 4.4.0-119-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-38-generic |


### <a name="debian-kernel-versions"></a>Versions du noyau Debian


**Version prise en charge** | **Version du service Mobilité Azure Site Recovery** | **Version du noyau** |
--- | --- | --- |
Debian 7 | 9.17, 9.18, 9.19 | 3.2.0-4-amd64 à 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-amd64 à 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-amd64 à 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 à 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 à 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.6-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Versions du noyau prises en charge de SUSE Linux Enterprise Server 12

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.19 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.96-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default à 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.96-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default à 4.4.138-94.39-default |

## <a name="linux-file-systemsguest-storage"></a>Stockage invité/système de fichiers Linux

**Composant** | **Pris en charge**
--- | ---
Systèmes de fichiers | ext3, ext4, XFS.
Gestionnaire de volume | LVM2. LVM est pris en charge pour les disques de données uniquement. Les machines virtuelles Azure ont un seul disque de système d’exploitation.
Dispositif de stockage paravirtualisé | Les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.
Unités de bloc d’entrée et de sortie en file d’attente | Non pris en charge.
Serveurs physiques avec le contrôleur de stockage HP CCISS | Non pris en charge.
Répertoires | Ces répertoires (s’ils sont configurés en tant que partitions/systèmes de fichiers séparés) doivent tous se trouver sur le même disque du système d’exploitation, sur le serveur source : /(root), /boot, /usr, /usr/local, /var, /etc.</br></br> /boot doit se trouver sur une partition de disque et ne doit pas être un volume LVM.<br/><br/>
Espace libre requis| 2 Go sur la partition/root <br/><br/> 250 Mo dans le dossier d’installation
XFSv5 | Les fonctionnalités XFSv5 sur des systèmes de fichiers XFS, tels que les sommes de contrôle de métadonnées, sont prises en charge à partir du service Mobilité version 9.10 et versions ultérieures. Utilisez l’utilitaire xfs_info pour vérifier le superbloc XFS pour la partition. Si ftype est défini sur 1, les fonctionnalités XFSv5 sont utilisées.

## <a name="vmdisk-management"></a>Gestion des machines virtuelles/disques

**Action** | **Détails**
--- | ---
Redimensionner le disque sur la machine virtuelle répliquée | Pris en charge.
Ajouter un disque à la machine virtuelle répliquée | Désactivez la réplication pour la machine virtuelle, ajoutez le disque, puis réactivez la réplication. L’ajout d’un disque sur une machine virtuelle de réplication n’est pas pris en charge pour l’instant.

## <a name="network"></a>Réseau

**Composant** | **Pris en charge**
--- | ---
Association de cartes réseau de réseau hôte | Pris en charge pour les machines virtuelles VMware <br/><br/>Non pris en charge pour la réplication des machines physiques
Réseau hôte VLAN | Oui.
Réseau hôte IPv4 | Oui.
Réseau hôte IPv6 | Non.
Association de cartes de réseau invité/serveur | Non.
Réseau invité/serveur IPv4 | Oui.
Réseau invité/serveur IPv6 | Non.
Adresse IP statique du réseau invité/serveur (Windows) | Oui.
Adresse IP statique du réseau invité/serveur (Linux) | Oui. <br/><br/>Les machines virtuelles sont configurées pour utiliser le protocole DHCP lors de la restauration automatique.
Plusieurs cartes réseau invité/serveur | Oui.


## <a name="azure-vm-network-after-failover"></a>Réseau de machines virtuelles Azure (après le basculement)

**Composant** | **Pris en charge**
--- | ---
Azure ExpressRoute | OUI
ILB | OUI
ELB | OUI
Azure Traffic Manager | OUI
Plusieurs cartes réseau | OUI
Adresses IP réservées | OUI
IPv4 | OUI
Conserver l’adresse IP source | OUI
Points de terminaison du service Réseau virtuel Azure<br/> (sans pare-feu de stockage Azure) | OUI
Mise en réseau accélérée | Non 

## <a name="storage"></a>Stockage
**Composant** | **Pris en charge**
--- | ---
Hôte NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques
Hôte SAN (iSCSI/FC) | OUI
vSAN hôte | Oui pour VMware<br/><br/> N/A pour les serveurs physiques
Multipath hôte (MPIO) | Oui, testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pour CLARiiON
Volumes virtuels hôtes (VVols) | Oui pour VMware<br/><br/> N/A pour les serveurs physiques
VMDK invité/serveur | OUI
EFI/UEFI invité/serveur| Partiellement (migration vers Azure pour les machines virtuelles VMware et Windows Server 2012, et versions ultérieures, uniquement) </br></br> Consultez la remarque au bas de la table
Disque de cluster partagé invité/serveur | Non 
Disque chiffré invité/serveur | Non 
NFS invité/serveur | Non 
SMB 3.0 invité/serveur | Non 
RDM invité/serveur | OUI<br/><br/> N/A pour les serveurs physiques
Disque invité/serveur > 1 To | OUI<br/><br/>Jusqu’à 4 095 Go
Disque invité/serveur avec une taille de secteur logique de 4 Ko et une taille de secteur physique de 4 K | OUI
Disque invité/serveur avec une taille de secteur logique de 4 K et une taille de secteur physique de 512 octets | OUI
Volume invité/serveur avec disque à bandes > 4 To <br><br/>Gestion des volumes logiques (LVM)| OUI
Invité/serveur - Espaces de stockage | Non 
Ajout/retrait à chaud de disque d’Invité/de serveur | Non 
Invité/serveur - Exclure le disque | OUI
Multipath invité/serveur (MPIO) | Non 

> [!NOTE]
> Les machines virtuelles VMware à démarrage UEFI exécutant Windows Server 2012 ou une version ultérieure peuvent être migrées vers Azure. Les restrictions suivantes s’appliquent :

> - Seule la migration vers Azure est prise en charge. La restauration automatique vers un site VMware local n’est pas prise en charge.
> - Le disque de système d’exploitation du serveur ne doit pas comprendre plus de 4 partitions.
> - Nécessite la version 9.13 du service Mobilité d’Azure Site Recovery, ou une version ultérieure.
> - Non pris en charge pour les serveurs physiques.

## <a name="azure-storage"></a>Stockage Azure

**Composant** | **Pris en charge**
--- | ---
Stockage localement redondant | OUI
Stockage géo-redondant | OUI
Stockage géo-redondant avec accès en lecture | OUI
Stockage froid | Non 
Stockage chaud| Non 
Objets blob de blocs | Non 
Chiffrement au repos (Storage Service Encryption)| OUI
Stockage Premium | OUI
Service Import/Export | Non 
Pare-feu et réseaux virtuels de stockage Azure configurés dans le compte de stockage de cache/de stockage cible (utilisé pour stocker les données de réplication) | Non 
Comptes de stockage v2 à usage général (niveaux chaud et froid) | Non 

## <a name="azure-compute"></a>Calcul Azure

**Fonctionnalité** | **Pris en charge**
--- | ---
Groupes à haute disponibilité | OUI
HUB | OUI
Disques gérés | OUI

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les exigences des machines virtuelles Azure décrites dans ce tableau. Lorsque Site Recovery vérifie la configuration requise, il échoue si certaines conditions requises ne sont pas remplies.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Système d’exploitation invité | Vérifiez les [systèmes d’exploitation pris en charge](#replicated-machines) pour les machines répliquées. | La vérification est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 64 bits. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go. | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification est mise en échec en cas de défaut de prise en charge.  
Nombre de disques de données | 64 ou moins. | La vérification est mise en échec en cas de défaut de prise en charge.  
Taille de disque de données | Jusqu’à 4 095 Go | La vérification est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
BitLocker | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine. |
nom de la machine virtuelle | De 1 et 63 caractères.<br/><br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. |  Mettez à jour la valeur dans les propriétés de machine de Site Recovery.


## <a name="vault-tasks"></a>Tâches de coffre

**Action** | **Pris en charge**
--- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non 
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non 


## <a name="download-latest-azure-site-recovery-components"></a>Téléchargez les derniers composants Azure Site Recovery

**Nom** | **Description** | **Instructions de téléchargement de la version la plus récente**
--- | --- | --- | --- | ---
Serveur de configuration | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/> Installé sur des serveurs VMware locaux | Pour une nouvelle installation, cliquez [ici](vmware-azure-deploy-configuration-server.md). Pour mettre à niveau un composant existant vers la version la plus récente, cliquez [ici](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Serveur de traitement|Installé par défaut sur le serveur de configuration. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement, et les envoie vers le stockage Azure. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement distincts afin de gérer de plus grands volumes de trafic de réplication.| Pour une nouvelle installation, cliquez [ici](vmware-azure-set-up-process-server-scale.md). Pour mettre à niveau un composant existant vers la version la plus récente, cliquez [ici](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Service Mobilité | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer | Pour une nouvelle installation, cliquez [ici](vmware-azure-install-mobility-service.md). Pour mettre à niveau un composant existant vers la version la plus récente, cliquez [ici](vmware-azure-install-mobility-service.md#update-mobility-service).

Pour en savoir plus sur les derniers correctifs et fonctionnalités, cliquez [ici](https://aka.ms/latest_asr_updates).


## <a name="next-steps"></a>Étapes suivantes
[Découvrez comment](tutorial-prepare-azure.md) préparer Azure à la récupération d’urgence de machines virtuelles VMware.
