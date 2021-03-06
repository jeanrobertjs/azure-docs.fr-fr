---
title: Systèmes d’exploitation invités pour Azure Stack | Microsoft Docs
description: Ces systèmes d’exploitation invités peuvent être utilisés avec Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: be4d9b3ea7e5715d7c3a4df11b7e8bab4d1d4ca5
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405595"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Systèmes d’exploitation invités pris en charge par Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="windows"></a>Windows

Azure Stack prend en charge les systèmes d’exploitation invités Windows répertoriés dans le tableau suivant :

| Système d’exploitation | Description | Disponible sur la Place de marché |
| --- | --- | --- | --- | --- | --- |
| Windows Server, version 1709 | 64 bits | Core avec conteneurs |
| Windows Server 2016 | 64 bits |  Centre de données, centre de données principal, centre de données avec conteneurs |
| Windows Server 2012 R2 | 64 bits |  Centre de données |
| Windows Server 2012 | 64 bits |  Centre de données |
| Windows Server 2008 R2 SP1 | 64 bits |  Centre de données |
| Windows Server 2008 SP2 | 64 bits |  Apportez votre propre image |
| Windows 10 *(voir remarque 1)* | 64 bits, Professionnel et Entreprise | Apportez votre propre image |

> [!NOTE]
> Pour déployer des systèmes d’exploitation clients Windows 10 sur Azure Stack, vous devez disposer d’une [licence Windows par utilisateur](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) ou en acheter une par le biais du programme Qualified Multitenant Hoster ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Les images de la Place de marché sont disponibles avec un système de licence de paiement à l’utilisation ou BYOL (apportez votre propre licence) (EA/SPLA). L’utilisation des deux sur une instance unique d’Azure Stack n’est pas prise en charge. Au cours du déploiement, Azure Stack injecte une version appropriée de l’agent invité dans l’image.

Les éditions de centre de données sont disponibles au téléchargement sur la Place de marché ; Les clients peuvent apporter leurs propres images de serveur, y compris d’autres éditions. Les images des clients Windows ne sont pas disponibles sur la Place de marché.

## <a name="linux"></a>Linux

Les distributions Linux répertoriées comme disponibles sur la Place de marché incluent l’agent Windows Azure Linux (WALA) nécessaire. Si vous ajoutez votre propre image à Azure Stack, suivez les instructions de l’article [Ajouter des images Linux à Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Les images personnalisées doivent être générées avec la dernière version publique de WALA. Les versions antérieures à 2.2.18 peuvent ne pas fonctionner correctement sur Azure Stack.
>
> [init-cloud](https://cloud-init.io/) n’est pas pris en charge sur Azure Stack pour le moment.

| Distribution | Description | Publisher | Marketplace |
| --- | --- | --- | --- | --- | --- |
| CentOS-based 6.9 | 64 bits | Rogue Wave | OUI |
| CentOS-based 7.4 | 64 bits | Rogue Wave | OUI |
| ClearLinux | 64 bits | ClearLinux.org | OUI |
| Conteneur Linux |  64 bits | CoreOS | Stable |
| Debian 8 Jessie | 64 bits | credativ |  OUI |
| Debian 9 "Stretch" | 64 bits | credativ | OUI |
| Red Hat Enterprise Linux 7.x | 64 bits | Red Hat |Apportez votre propre image |
| SLES 11SP4 | 64 bits | SUSE | OUI |
| SLES 12SP3 | 64 bits | SUSE | OUI |
| Ubuntu 14.04-LTS | 64 bits | Canonical | OUI |
| Ubuntu 16.04-LTS | 64 bits | Canonical | OUI |
| Ubuntu 18.04-LTS | 64 bits | Canonical | Oui |

Pour plus d’informations sur le support de Red Hat Enterprise Linux, consultez l’article [Red Hat and Azure Stack: Frequently Asked Questions](https://access.redhat.com/articles/3413531) (Red Hat et Azure Stack : Forum aux Questions).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la Place de marché Azure Stack, consultez les articles suivants :

[Télécharger des éléments de la Place de marché](azure-stack-download-azure-marketplace-item.md)  
[Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)