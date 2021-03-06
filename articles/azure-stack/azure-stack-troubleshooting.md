---
title: Résolution des problèmes de Microsoft Azure Stack | Microsoft Docs
description: Résolution des problèmes d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c463599190c5bfaac47a70dbca7b8a67dc830f3a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363473"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Résolution des problèmes de Microsoft Azure Stack

Ce document fournit des informations de résolution des problèmes courants pour Azure Stack. 

> [!NOTE]
> Étant donné que le Kit de développement technique Azure Stack (ASDK) est proposé comme environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services client Microsoft. Si vous rencontrez un problème, veillez à consulter le [Forum MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) pour obtenir une assistance et des informations supplémentaires.  

Les recommandations pour la résolution des problèmes qui sont décrites dans cette section proviennent de différentes sources ; elles pourront peut-être résoudre votre problème en particulier. Les exemples de code sont fournis en l’état et les résultats attendus ne sont pas garantis. Cette section est susceptible de faire l’objet de modifications et de mises à jour fréquentes au fur et à mesure que des améliorations sont apportées au produit.

## <a name="deployment"></a>Déploiement
### <a name="deployment-failure"></a>Échec du déploiement
Si vous rencontrez un problème lors de l’installation, vous pouvez relancer le déploiement à partir de l’étape qui n’a pas abouti en utilisant l’option de réexécution du script de déploiement.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>À la fin du déploiement du Kit ASDK, la session PowerShell est toujours ouverte et ne présente aucune sortie.
Ce comportement est probablement tout simplement le résultat du comportement par défaut d’une fenêtre de commande PowerShell, lorsqu’elle a été sélectionnée. Le déploiement du kit de développement s’est déroulé correctement, mais le script a été interrompu au moment de la sélection de la fenêtre. Vous pouvez vérifier que l’installation est terminée en recherchant le mot « select » dans la barre de titre de la fenêtre de commande.  Appuyez sur la touche Échap pour la désélectionner ; le message d’achèvement devrait alors s’afficher.

## <a name="virtual-machines"></a>Machines virtuelles
### <a name="default-image-and-gallery-item"></a>Élément de la galerie et image par défaut
Vous devez ajouter un élément de la galerie et une image Windows Server avant de pouvoir déployer des machines virtuelles dans Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Après le redémarrage de l’hôte Azure Stack, certaines machines virtuelles ne démarrent pas automatiquement
Vous remarquerez peut-être que les services Azure Stack ne sont pas immédiatement disponibles après le redémarrage de votre hôte.  Cela est dû au fait que la vérification de la cohérence des fournisseurs de ressources et des [machines virtuelles d’infrastructure](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) Azure Stack demande un certain temps. Toutefois, ils finissent par démarrer automatiquement.

Vous remarquerez peut-être aussi que les machines virtuelles clientes ne démarrent pas automatiquement après le redémarrage de l’hôte du Kit de développement Azure Stack. Ce problème est connu ; quelques étapes manuelles suffisent pour les mettre en ligne :

1.  Sur l’hôte du Kit de développement Azure Stack, démarrez **Gestionnaire du cluster de basculement** dans le menu Démarrer.
2.  Sélectionnez le cluster **S-Cluster.azurestack.local**.
3.  Sélectionnez **Rôles**.
4.  Les machines virtuelles clientes apparaissent avec l’état *enregistré*. Lorsque toutes les machines virtuelles d’infrastructure sont en cours d’exécution, cliquez avec le bouton droit sur les machines virtuelles clientes et sélectionnez **Démarrer** pour reprendre la machine virtuelle.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>J’ai supprimé des machines virtuelles, mais je vois toujours les fichiers de VHD sur le disque. Ce comportement est-il attendu ?
Oui. Ce comportement est normal. Il a été conçu ainsi pour les raisons suivantes :

* La suppression d’une machine virtuelle n’entraîne pas celle des VHD. Les disques sont des ressources distinctes dans le groupe de ressources.
* Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement sur Azure Resource Manager, mais les disques qu’il contient éventuellement restent conservés dans le stockage jusqu’à l’exécution du nettoyage de la mémoire.

Si vous voyez des VHD « orphelins », il est important de savoir s’ils font partie du dossier d’un compte de stockage supprimé. Si le compte de stockage n’a pas été supprimé, il est normal qu’ils soient toujours présents.

Pour en savoir plus sur la configuration du seuil de rétention et de la récupération à la demande, consultez la page [Gérer les comptes de stockage](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Stockage
### <a name="storage-reclamation"></a>Récupération du stockage
Il peut s’écouler jusqu’à 14 heures avant que la capacité récupérée ne s’affiche dans le portail. La récupération d’espace dépend de différents facteurs, notamment le pourcentage d’utilisation des fichiers conteneurs internes dans le magasin d’objets blob de blocs. Par conséquent, selon la quantité de données supprimées, il n’y a pas de garantie quant à la quantité d’espace récupérable lors de l’exécution du récupérateur de mémoire.

