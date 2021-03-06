---
title: À propos de la passerelle VPN pour Azure Stack | Microsoft Docs
description: En savoir plus et configurer les passerelles VPN que vous utilisez avec Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: 8f45535b0e75c3f95a56148ea92a343bb0d8a9f5
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079257"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>À propos de la passerelle VPN pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Avant que vous puissiez envoyer du trafic réseau entre votre réseau virtuel Azure et votre site local, vous devez créer une passerelle de réseau virtuel pour votre réseau virtuel.

Une passerelle VPN est un type de passerelle de réseau virtuel qui envoie le trafic chiffré à travers une connexion publique. Vous pouvez utiliser des passerelles VPN pour envoyer en toute sécurité le trafic entre un réseau virtuel dans Azure Stack et un réseau virtuel dans Azure. Vous pouvez également envoyer en toute sécurité le trafic entre un réseau virtuel et un autre réseau connecté à un périphérique VPN.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez le type de passerelle que vous voulez créer. Azure Stack prend en charge un seul type de passerelle de réseau virtuel : le type **Vpn**.

Chaque réseau virtuel peut avoir deux passerelles de réseau virtuel, mais une seule de chaque type. Selon les paramètres que vous choisissez, vous pouvez créer plusieurs connexions à une passerelle VPN unique. La configuration d’une connexion sur plusieurs sites en est un exemple.

Avant de créer et configurer des passerelles VPN pour Azure Stack, passez en revue les [considérations relatives à la mise en réseau Azure Stack](/articles/azure-stack/user/azure-stack-network-differences.md) pour savoir dans quelle mesure les configurations pour Azure Stack diffèrent de celles pour Azure.

>[!NOTE]
>Dans Azure, le débit de la bande passante pour la référence SKU de passerelle VPN que vous choisissez doit être réparti entre toutes les connexions connectées à la passerelle. Toutefois, dans Azure Stack, la valeur de la bande passante pour la référence SKU de la passerelle VPN est appliquée à chaque ressource de connexion connectée.
>
> Par exemple :
> * dans Azure, la référence SKU de passerelle VPN de base peut prendre en charge environ 100 Mbits/s de débit agrégé. Si vous créez deux connexions pour cette passerelle VPN, dont une connexion avec une bande passante de 50 Mbits/s, 50 Mbits/s sont disponibles pour l’autre connexion.
> * Dans Azure Stack, *chaque* connexion à la référence SKU de passerelle VPN de base se voit allouer un débit de 100 Mbits/s.

## <a name="configuring-a-vpn-gateway"></a>Configuration d’une passerelle VPN

Une connexion par passerelle VPN s’appuie sur plusieurs ressources qui sont configurées avec des paramètres spécifiques. La plupart des ressources peuvent être configurées séparément, mais elles doivent être configurées dans un certain ordre dans certains cas.

### <a name="settings"></a>Paramètres

Les paramètres que vous avez choisis pour chaque ressource sont essentiels à la création d’une connexion réussie.

Pour plus d’informations sur les ressources et paramètres spécifiques pour la passerelle VPN, consultez [À propos des paramètres de passerelle VPN pour Azure Stack](azure-stack-vpn-gateway-settings.md). Cet article vous aidera à comprendre les éléments suivants :

* Les types de passerelle, les types de VPN et les types de connexion.
* Les sous-réseaux de passerelle, les passerelles de réseau local et les autres paramètres de ressource que vous souhaiterez peut-être prendre en compte.

### <a name="deployment-tools"></a>Outils de déploiement

Vous pouvez créer et configurer des ressources à l’aide d’un outil de configuration, comme le portail Azure. Vous pouvez ultérieurement décider de passer à un autre outil, tel que PowerShell, pour configurer des ressources supplémentaires ou modifier les ressources existantes, le cas échéant. Il n’est pour le moment pas possible de configurer toutes les ressources et tous les paramètres des ressources dans le portail Azure. Les instructions fournies dans les articles dédiés à chaque topologie de connexion indiquent si un outil de configuration spécifique est requis.

## <a name="connection-topology-diagrams"></a>Diagrammes de topologie de connexion

Il est important de savoir qu’il existe différentes configurations disponibles pour les connexions aux passerelles VPN. Déterminez la configuration qui correspond le mieux à vos besoins. Dans les sections ci-dessous, vous pouvez afficher des informations et des diagrammes de topologie sur les connexions de passerelle VPN suivantes :

* Modèle de déploiement disponible
* Outils de configuration disponibles
* Liens vous redirigeant directement vers un article, si disponibles

Les graphiques et les descriptions dans les sections suivantes peuvent vous aider à sélectionner une topologie de connexion répondant à vos besoins. Le graphique présente les principales topologies de base, mais il est possible de créer des configurations plus complexes à l’aide des diagrammes.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site à site et multi-sites (tunnel VPN IPsec/IKE)

### <a name="site-to-site"></a>De site à site

Une connexion par passerelle VPN site à site (S2S) est une connexion via un tunnel VPN IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion requiert un appareil VPN local disposant d’une adresse IP publique. L’appareil VPN ne peut pas se trouver derrière un traducteur d'adresses réseau. Les connexions S2S peuvent être utilisées pour les configurations hybrides et entre différents locaux.

![Exemple de configuration d’une connexion VPN de site à site](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multi-sites

Ce type de connexion est une variante de la connexion site à site. Vous créez plusieurs connexions VPN à partir de votre passerelle de réseau virtuel, généralement en vous connectant à plusieurs sites locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de VPN basé sur l’itinéraire (équivalent d’une passerelle dynamique pour les réseaux virtuels classiques). Chaque réseau virtuel ne pouvant disposer que d’une seule passerelle de réseau virtuel, toutes les connexions passant par la passerelle partagent la bande passante disponible. Cela est souvent appelé connexion « multi-sites ».

![Exemple de connexion multisites de passerelle VPN Azure](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>SKU de passerelle

Lorsque vous créez une passerelle de réseau virtuel pour Azure Stack, vous spécifiez la référence SKU de passerelle que vous voulez utiliser. Les références SKU de passerelle VPN suivantes sont prises en charge :

* De base
* Standard
* HighPerformance

Lorsque vous sélectionnez une référence SKU de passerelle supérieure, par exemple Standard à la place de De base, ou HighPerformance à la place de Standard ou De base, un plus grand nombre de processeurs et une bande passante réseau plus importante sont alloués à la passerelle. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel.

Azure Stack ne prend pas en charge la référence SKU de passerelle UltraPerformance, utilisée exclusivement avec Express Route.

Prenez en compte les éléments suivants lorsque vous sélectionnez la référence (SKU) :

* Azure Stack ne prend pas en charge les passerelles basées sur les stratégies.
* Le protocole de passerelle frontière (BGP) n’est pas pris en charge pour la référence SKU de base.
* Les configurations de passerelle VPN et ExpressRoute coexistantes ne sont pas prises en charge dans Azure Stack.
* Les connexions VPN S2S en mode actif-actif avec des passerelles peuvent uniquement être configurées sur la référence SKU HighPerformance.

## <a name="estimated-aggregate-throughput-by-sku"></a>Débit agrégé estimé par SKU

Le tableau suivant présente les types de passerelle et le débit total estimé par référence de passerelle.

|   | Débit de passerelle VPN *(1)* | Tunnels IPsec max de passerelle VPN *(2)* |
|-------|-------|-------|
|**Référence SKU de base** ***(3)***    | 100 Mbits/s  | 10    |
|**Référence Standard**       | 100 Mbits/s  | 10    |
|**Référence hautes performances** | 200 Mbits/s    | 5 |

**Notes de la table :**

*Note (1)* Le débit du VPN n’est pas garanti pour les connexions intersites via Internet. Il s’agit de la mesure du débit maximal possible.  
*Note (2)* Le nombre maximal de tunnels est le total par déploiement Azure Stack pour TOUS les abonnements.  
*Note (3)* Le routage BGP n’est pas pris en charge pour la référence SKU de base.

## <a name="next-steps"></a>Étapes suivantes

[Paramètres de configuration de la passerelle VPN pour Azure Stack](azure-stack-vpn-gateway-settings.md)
