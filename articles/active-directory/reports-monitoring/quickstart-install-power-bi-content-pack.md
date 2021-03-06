---
title: Installer le pack de contenu Azure AD Power BI | Microsoft Docs
description: Découvrez comment installer le pack de contenu Azure AD Power BI
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ba2784663a585aebb82ee149be3b54e73920f6dd
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816648"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Démarrage rapide : Installer le pack de contenu Azure Active Directory Power BI

|  |
|--|
|Actuellement, le pack de contenu Azure AD Power BI utilise les API Azure AD Graph pour récupérer des données à partir de votre locataire Azure AD. Par conséquent, vous pouvez voir certaines différences entre les données disponibles dans le pack de contenu et les données récupérées à l’aide des [API Microsoft Graph pour les rapports](concept-reporting-api.md). |
|  |

Le pack de contenu Power BI pour Azure Active Directory vous donne la possibilité d’obtenir des informations détaillées sur l’activité de votre répertoire active directory. Vous pouvez télécharger le pack de contenu exemple et l’utiliser pour créer des rapports sur toutes les activités au sein de votre répertoire grâce à la puissante expérience de visualisation qu’offre Power BI. Vous pouvez créer votre propre tableau de bord et le partager facilement avec d’autres personnes de votre organisation. 

Dans ce démarrage rapide, vous allez apprendre à installer le pack de contenu.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

* Un compte Microsoft Power BI. Il s’agit du même compte que votre compte Office 365 ou Azure AD. 
* Votre ID de locataire Azure AD. Il s’agit de l’**ID de répertoire** de votre répertoire obtenu sur la [page de propriétés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) du portail Azure.
* Une licence Azure AD Premium (P1/P2). 

## <a name="install-azure-ad-power-bi-content-pack"></a>Installer le pack de contenu Azure AD Power BI 

1. Connectez-vous à [Power BI](https://app.powerbi.com/groups/me/getdata/services) avec votre compte Power BI. Il s’agit du même compte que votre compte Office 365 ou Azure AD.

2. Recherchez les **journaux d’activité Azure Active Directory** dans la page **Applications** et choisissez **Obtenir maintenant**. 

   ![Pack de contenu Power BI Azure Active Directory](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. Dans la fenêtre contextuelle, tapez votre ID de locataire Azure AD, entrez **7** pour le nombre de jours de la requête, puis sélectionnez **Suivant**.
    
   ![Pack de contenu Power BI Azure Active Directory](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Une fois le tableau de bord des journaux d’activité Azure Active Directory créé, sélectionnez-le.

   ![Pack de contenu Power BI Azure Active Directory](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Étapes suivantes

* [Utiliser le pack de contenu Power BI](howto-power-bi-content-pack.md).
* [Résoudre les problèmes liés aux erreurs de pack de contenu](troubleshoot-content-pack.md).
