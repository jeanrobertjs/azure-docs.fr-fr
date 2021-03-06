---
title: Mettre à jour l’antivirus Windows Defender sur Azure Stack
description: Informations sur la façon dont l’antivirus est mis à jour sur Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 09/26/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 59c59705e840d3cdd0d3c5310d84d711cd00c96a
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394007"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Mettre à jour l’antivirus Windows Defender sur Azure Stack

[L’antivirus Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) est une solution anti-programme malveillant qui sécurise votre infrastructure et la protège contre les virus. Chaque composant de l’infrastructure Azure Stack (hôtes Hyper-V et machines virtuelles) est protégé par l’antivirus Windows Defender. Pour garantir une protection toujours optimale, les définitions, le moteur et la plateforme de l’antivirus Windows Defender doivent être régulièrement mis à jour. Le mode d’application des mises à jour dépend de votre configuration.

## <a name="connected-scenario"></a>Scénario connecté

Le [fournisseur de ressources de mise à jour](azure-stack-updates.md#the-update-resource-provider) d’Azure Stack télécharge les mises à jour de l’engin et des définitions du logiciel anti-programme malveillant plusieurs fois par jour. Chaque composant de l’infrastructure Azure Stack récupère ensuite la mise à jour du fournisseur de ressources de mise à jour et l’applique automatiquement.

Pour les mises à jour de la plateforme du logiciel anti-programme malveillant, vous devez appliquer la [mise à jour mensuelle d’Azure Stack](azure-stack-apply-updates.md). La mise à jour mensuelle d’Azure Stack inclut les mises à jour de la plateforme de l’antivirus Windows Defender publiées au cours du mois.

## <a name="disconnected-scenario"></a>Scénario déconnecté

 Appliquez la [mise à jour mensuelle d’Azure Stack](azure-stack-apply-updates.md). La mise à jour mensuelle d’Azure Stack inclut les mises à jour des définitions, de l’engin et de la plateforme de l’antivirus Windows Defender publiées au cours du mois.

## <a name="next-steps"></a>Étapes suivantes

[Apprenez-en davantage sur la sécurité dans Azure Stack](azure-stack-security-foundations.md)
