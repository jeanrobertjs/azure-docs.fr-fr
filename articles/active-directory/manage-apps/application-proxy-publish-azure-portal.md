---
title: Publier des applications avec le proxy d’application Azure AD | Microsoft Docs
description: Publiez des applications locales dans le cloud avec le proxy d’application Azure AD dans le portail Azure.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 973a6201a227e6c2e295e6e5ea2f40c302572504
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42141318"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publier des applications avec le Proxy d’application Azure AD

Le service Proxy d’application Azure Active Directory (AD) vous aide à prendre en charge les personnes qui travaillent à distance en publiant des applications locales afin de les rendre accessibles sur Internet. Vous pouvez publier ces applications sur le portail Azure et fournir un accès à distance sécurisé à partir de l’extérieur de votre réseau.

Cet article vous guide au long des étapes de publication d’une application locale avec un proxy d’application. Une fois que vous aurez suivi cet article, vos utilisateurs seront en mesure d’accéder à votre application à distance. Et vous serez prêt à configurer des fonctionnalités supplémentaires pour l’application, comme l’authentification unique, les exigences de sécurité ou les informations personnalisées.

Si vous n’êtes pas familiarisé avec le proxy d’application, vous pouvez en savoir plus en consultant l’article [Offrir un accès à distance sécurisé aux applications locales](application-proxy.md).

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez déjà installé et inscrit un connecteur. Si vous n’avez pas encore réalisé ces étapes, consultez [Bien démarrer avec le proxy d’application et l’installation du connecteur](application-proxy-enable.md).

## <a name="publish-an-on-premises-app-for-remote-access"></a>Publier une application locale pour un accès à distance

Suivez ces étapes pour publier vos applications avec le proxy d’application. Si vous n’avez pas déjà téléchargé et configuré un connecteur pour votre organisation, accédez à [Bien démarrer avec le proxy d’application et l’installation du connecteur](application-proxy-enable.md), puis publiez votre application.

> [!TIP]
> Si vous testez le proxy d’application pour la première fois, choisissez une application qui est configurée pour l’authentification par mot de passe. Le proxy d’application prend en charge d’autres types d’authentification, mais les applications basées sur un mot de passe sont les plus faciles à exploiter rapidement. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Nouvelle application**.

  ![Ajouter une application d’entreprise](./media/application-proxy-publish-azure-portal/add-app.png)

3. Sélectionnez **Tout**, puis **Application locale**.  

  ![Ajout de votre propre application](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Fournissez les informations suivantes sur votre application :

   - **Nom** : nom de l’application qui apparaît dans le panneau d’accès et sur le portail Azure. 

   - **URL interne** : URL permettant d’accéder à l’application à partir de votre réseau privé. Vous pouvez spécifier un chemin spécifique sur le serveur principal à publier, alors que le reste du serveur n’est pas publié. De cette façon, vous pouvez publier des sites différents sur le même serveur en tant qu’applications distinctes et donner à chacun d’eux son propre nom et ses propres règles d’accès.

     > [!TIP]
     > Si vous publiez un chemin d’accès, vérifiez qu’il inclut l’ensemble des images, des scripts et des feuilles de style nécessaires pour votre application. Par exemple, si votre application se trouve à l’adresse https://yourapp/app et utilise des images situées à l’adresse https://yourapp/media, vous devrez publier l’adresse https://yourapp/ comme chemin d’accès. Cette URL interne n’est pas nécessairement la page d’accueil que vos utilisateurs voient. Pour plus d’informations, consultez [Définir une page d’accueil personnalisée pour les applications publiées](application-proxy-configure-custom-home-page.md).

   - **URL externe** : adresse que vos utilisateurs utilisent pour accéder à l’application en dehors de votre réseau. Si vous ne souhaitez pas utiliser le domaine de proxy d’application par défaut, lisez la documentation relative aux [domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md).
   - **Pré-authentification** : façon dont le service Proxy d’application vérifie les utilisateurs avant de leur donner accès à votre application. 

     - Azure Active Directory : le proxy d’application redirige les utilisateurs pour la connexion à Azure AD, qui authentifie leurs autorisations pour le répertoire et l’application. Nous vous recommandons de laisser cette option par défaut, afin que vous puissiez tirer parti des fonctionnalités de sécurité Azure AD, comme l’accès conditionnel et l’authentification multifacteur.
     - Direct : les utilisateurs n’ont pas besoin de s’authentifier auprès d’Azure Active Directory pour accéder à l’application. Vous pouvez toujours configurer les exigences d’authentification sur le serveur principal.
   - **Groupe de connecteurs** : les connecteurs gèrent l’accès à distance à votre application et les groupes de connecteurs vous aident à organiser des connecteurs et des applications par région, réseau ou objectif. Si aucun groupe de connecteurs n’est encore créé, votre application est assignée au groupe **Par défaut**.

>[!NOTE]
>Si votre application utilise des WebSockets pour se connecter, vérifiez que vous avez au moins la version 1.5.612.0 du connecteur, avec prise en charge de WebSocket, et que le groupe de connecteurs attribué utilise uniquement ces connecteurs.

   ![Configuration de votre application](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Si nécessaire, configurez des paramètres supplémentaires. Pour la plupart des applications, vous devez conserver ces paramètres dans leur état par défaut. 
   - **Expiration de l’application principale** : définissez cette valeur sur **Long** uniquement si l’authentification et la connexion de votre application sont lentes. 
   - **Utiliser un cookie HTTP-only** : définissez cette valeur sur **Oui** pour que les cookies de proxy d’application incluent un indicateur HTTPOnly dans l’en-tête de réponse HTTP.
   - **Traduire l’URL dans les en-têtes** : conservez la valeur **Oui**, sauf si votre application a demandé l’en-tête d’hôte d’origine dans la requête d’authentification.
   - **Translate URLs in Application Body** (Traduire les URL dans le corps de l’application) : conservez la valeur **Non**, sauf si vous avez codé en dur des liens HTML vers d’autres applications locales et si vous n’utilisez pas les domaines personnalisés. Pour plus d’informations, consultez [Rediriger les liens codés en dur pour les applications publiées avec le Proxy d’application Azure AD](application-proxy-configure-hard-coded-link-translation.md).
   
   ![Configuration de votre application](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Sélectionnez **Ajouter**.


## <a name="add-a-test-user"></a>Ajouter un utilisateur de test 

Pour vérifier que votre application a été correctement publiée, ajoutez un compte d’utilisateur test. Vérifiez que ce compte dispose déjà des autorisations d’accès à l’application à partir du réseau d’entreprise.

1. Dans le panneau Démarrage rapide, sélectionnez **Assign a user for testing (Attribuer un utilisateur à des fins de test)**.

  ![Attribuer un utilisateur à des fins de test](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Dans le panneau Utilisateurs et groupes, sélectionnez **Ajouter**.

  ![Ajouter un utilisateur ou groupe](./media/application-proxy-publish-azure-portal/add-user.png)

3. Dans le panneau Ajouter une attribution, sélectionnez **Utilisateurs et groupes**, puis le compte à ajouter. 
4. Sélectionnez **Attribuer**.

## <a name="test-your-published-app"></a>Tester votre application publiée

Dans votre navigateur, accédez à l’URL externe que vous avez configurée au cours de l’étape de publication. L’écran d’accueil doit s’afficher et vous devez être en mesure de vous connecter avec le compte de test que vous avez configuré.

![Tester votre application publiée](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Étapes suivantes
- [Télécharger des connecteurs](application-proxy-enable.md) et [créer des groupes de connecteurs](application-proxy-connector-groups.md) pour publier des applications sur des réseaux et emplacements distincts

- [Configurer l’authentification unique](application-proxy-configure-single-sign-on-password-vaulting.md) pour votre application récemment publiée
