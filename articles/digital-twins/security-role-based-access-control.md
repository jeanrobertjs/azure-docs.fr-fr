---
title: Présentation du contrôle d’accès en fonction du rôle dans Azure Digital Twins | Microsoft Docs
description: Découvrez l’authentification dans Digital Twins avec le contrôle d’accès en fonction du rôle.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014786"
---
# <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Azure Digital Twins permet un contrôle d’accès précis à des données, ressources et actions spécifiques de votre graphe spatial. En effet, il utilise une gestion granulaire des rôles et des autorisations appelée contrôle d’accès en fonction du rôle. RBAC se compose de _rôles_ et d’_attributions de rôles_. Les rôles identifient le niveau d’autorisations. Les attributions de rôles associent un rôle à un utilisateur ou un appareil.

À l’aide de RBAC, l’autorisation peut être accordée à :

- Un utilisateur.
- Un appareil.
- Un principal du service.
- Une fonction définie par l’utilisateur. 
- Tous les utilisateurs qui appartiennent à un domaine. 
- Un locataire.
 
Le degré d’accès peut également être ajusté.

RBAC est unique, car les autorisations sont héritées vers le bas du graphe spatial.

## <a name="what-can-i-do-with-rbac"></a>Que puis-je faire avec le contrôle d’accès en fonction du rôle (RBAC) ?

Un développeur peut utiliser RBAC pour effectuer les opérations suivantes :

* Accorder à un utilisateur la possibilité de gérer les appareils pour un bâtiment entier ou uniquement pour une pièce ou un étage spécifique.
* Octroyer à un administrateur l’accès global à tous les nœuds d’un graphe spatial ou uniquement à une section du graphe.
* Accorder à un spécialiste du support un accès en lecture au graphe, à l’exception des clés d’accès.
* Accorder à chaque membre d’un domaine un accès en lecture à tous les objets du graphe.

## <a name="rbac-best-practices"></a>Meilleures pratiques relatives à RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>contrôleur

### <a name="role-definitions"></a>Définitions de rôles

Une définition de rôle est une collection d’autorisations, et est parfois appelée rôle. La définition de rôle répertorie les opérations autorisées, notamment créer, lire, mettre à jour et supprimer. Elle spécifie également les types d’objets auxquels ces autorisations s’appliquent.

Les rôles suivants sont disponibles dans Azure Digital Twins :

* **Administrateur d’espace** : autorisation Créer, lire, mettre à jour et supprimer pour l’espace spécifié et tous les nœuds en dessous. Autorisation globale.
* **Administrateur d’utilisateurs** : autorisation Créer, lire, mettre à jour et supprimer pour les utilisateurs et leurs objets connexes. Autorisation de lecture pour les espaces.
* **Administrateur d’appareils** : autorisation Créer, lire, mettre à jour et supprimer pour les appareils et leurs objets connexes. Autorisation de lecture pour les espaces.
* **Administrateur de clés** : autorisation Créer, lire, mettre à jour et supprimer pour les clés d’accès. Autorisation de lecture pour les espaces.
* **Administrateur de jetons** : autorisation Lire et mettre à jour pour les clés d’accès. Autorisation de lecture pour les espaces.
* **Utilisateur** : autorisation Lire pour les espaces, les capteurs et les utilisateurs, incluant leurs objets connexes.
* **Spécialiste du support** : autorisation Lire pour tout sauf pour les clés d’accès.
* **Installateur d’appareils** : autorisation Lire et mettre à jour pour les appareils et les capteurs, incluant leurs objets connexes. Autorisation de lecture pour les espaces.
* **Appareil de passerelle** : autorisation Créer pour les capteurs. Autorisation Lire pour les appareils et les capteurs, incluant leurs objets connexes.

>[!NOTE]
> Pour récupérer les définitions complètes des rôles précédents, interrogez l’API système/rôles.

### <a name="object-types"></a>Types d’objets

`ObjectIdType` fait référence au type d’identité auquel un rôle est attribué. En dehors des types `DeviceId` et `UserDefinedFunctionId`, les types correspondent à une propriété d’un objet Azure Active Directory (Azure AD) :
  
* Le type `UserId` attribue un rôle à un utilisateur.
* Le type `DeviceId` attribue un rôle à un appareil.
* Le type `DomainName` attribue un rôle à un nom de domaine. Chaque utilisateur avec le nom de domaine spécifié a les droits d’accès du rôle correspondant.
* Le type `TenantId` attribue un rôle à un locataire. Chaque utilisateur qui appartient à l’ID de locataire Azure AD spécifié a les droits d’accès du rôle correspondant.
* Le type `ServicePrincipalId` attribue un rôle à un ID d’objet de principal de service.
* Le type `UserDefinedFunctionId` attribue un rôle à une fonction définie par l’utilisateur.

> [!div class="nextstepaction"]
> [Rechercher l’ID d’objet d’un utilisateur](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Obtenir l’ID d’objet d’un principal de service](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Récupérer l’ID d’objet d’un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Affectations de rôles

Pour accorder des autorisations à un destinataire, créez une attribution de rôle. Pour révoquer les autorisations, supprimez l’attribution de rôle. Une attribution de rôle Azure Digital Twins associe un objet, tels qu’un utilisateur ou un locataire Azure AD, à un rôle et un espace. Les autorisations sont accordées à tous les objets qui appartiennent à cet espace. L’espace inclut la totalité du graphe spatial en dessous.

Par exemple, un utilisateur reçoit une attribution de rôle avec le rôle `DeviceInstaller` pour le nœud racine d’un graphe spatial, qui représente un bâtiment. L’utilisateur peut alors lire et mettre à jour les appareils pour ce nœud et tous les autres espaces enfants dans le bâtiment.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurité Azure Digital Twins, consultez la section [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md).
