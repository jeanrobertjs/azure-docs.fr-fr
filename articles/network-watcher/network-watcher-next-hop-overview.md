---
title: Présentation du tronçon suivant dans Azure Network Watcher | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la fonctionnalité de tronçon suivant de Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 28eacdce922e26d391cf34f78cb03ead9c6887a1
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391264"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Utiliser le tronçon suivant pour diagnostiquer les problèmes de routage de machines virtuelles

Le trafic d’une machine virtuelle est envoyé vers une destination en fonction des routes effectives associées à une carte réseau. Le tronçon suivant obtient le type de tronçon suivant et l’adresse IP d’un paquet à partir d’une machine virtuelle et d’une carte réseau spécifiques. Le fait de connaître le tronçon suivant permet de déterminer si le trafic est redirigé vers la destination appropriée, ou si le trafic n’est envoyé nulle part. Une mauvaise configuration des routes, où le trafic est dirigé vers un emplacement local ou une appliance virtuelle, peut provoquer des problèmes de connectivité. Le tronçon suivant renvoie également la table d’itinéraires associée au tronçon suivant. Si la route est définie en tant que route définie par l’utilisateur, cette route est renvoyée. Sinon, le tronçon suivant renvoie **Route du système**.

![vue d’ensemble du tronçon suivant](./media/network-watcher-next-hop-overview/figure1.png)

Les tronçons suivants qui peuvent être retournés par la fonctionnalité de tronçon suivant sont les suivants :

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Aucun

Pour plus d’informations sur chaque type de tronçon suivant, consultez [Vue d’ensemble du routage](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser le tronçon suivant pour diagnostiquer les problèmes de routage réseau de machines virtuelles, consultez [Diagnostiquer les problèmes de routage réseau de machines virtuelles](diagnose-vm-network-routing-problem.md).
