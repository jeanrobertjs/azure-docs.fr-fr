---
title: Guide de l’administrateur Atlassian Jira/Confluence - Azure Active Directory| Microsoft Docs
description: Guide de l’administrateur pour utiliser Atlassian Jira et Confluence avec Azure Active Directory (Azure AD)...
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: jeedes
ms.openlocfilehash: 49516523abdd927c3ae60235fcd74473689c6856
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020615"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guide de l’administrateur Atlassian Jira et Confluence pour Azure Active Directory

## <a name="overview"></a>Vue d’ensemble

Le plug-in d’authentification unique (SSO) Azure Active Directory (Azure AD) permet aux clients Microsoft Azure AD d’utiliser leur compte professionnel ou scolaire pour se connecter aux produits serveur Atlassian Jira et Confluence. Il implémente l’authentification unique basée sur SAML 2.0.

## <a name="how-it-works"></a>Fonctionnement

Quand les utilisateurs souhaitent se connecter à l’application Atlassian Jira ou Confluence, un bouton **Login with Azure AD (Se connecter avec Azure AD)** apparaît sur la page de connexion. Lorsqu’ils cliquent sur ce bouton, ils doivent se connecter en utilisant la page de connexion de l’organisation Azure AD (c’est-à-dire leur compte professionnel ou scolaire).

Une fois authentifiés, ils sont normalement en mesure de se connecter à l’application. S’ils sont déjà authentifiés avec l’identifiant et le mot de passe de leur compte professionnel ou scolaire, ils se connectent directement à l’application. 

La connexion est valable à la fois pour Jira et Confluence. Si les utilisateurs sont connectés à l’application Jira et que Confluence est également ouverte dans la même fenêtre de navigateur, ils ne sont pas obligés de fournir leurs informations d’identification pour l’autre application. 

Les utilisateurs peuvent également accéder au produit de la société Atlassian via Mes applications, sous leur compte professionnel ou scolaire. Ils doivent être connectés et aucune information d’identification ne leur est demandée.

> [!NOTE]
> Ce plug-in n’intervient pas dans l’attribution d’utilisateurs.

## <a name="audience"></a>Audience

Les administrateurs de Jira et Confluence peuvent utiliser le plug-in pour activer l’authentification unique à l’aide d’Azure AD.

## <a name="assumptions"></a>Hypothèses

* Les instances Jira et Confluence sont compatibles avec le protocole HTTPS.
* Les utilisateurs sont déjà créés dans Jira ou Confluence.
* Les rôles ont déjà été attribués aux utilisateurs dans Jira ou Confluence.
* Les administrateurs ont accès aux informations nécessaires pour configurer le plug-in.
* Jira ou Confluence sont également disponibles à l’extérieur du réseau d’entreprise.
* Le plug-in fonctionne uniquement avec la version locale de Jira et Confluence.

## <a name="prerequisites"></a>Prérequis

Avant d’installer le plug-in, tenez compte des informations suivantes :

* Jira et Confluence sont installés sur une version Windows 64 bits.
* Les versions de Jira et Confluence sont compatibles avec le protocole HTTPS.
* Jira et Confluence sont disponibles sur Internet.
* Les informations d’identification administrateur sont en place pour Jira et Confluence.
* Les informations d’identification administrateur sont en place pour Azure AD.
* WebSudo est désactivé dans Jira et Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versions prises en charge de Jira et Confluence

Le plug-in prend en charge les versions suivantes de Jira et Confluence :

* Jira Core et Software : 6.0 à 7.8
* Jira Service Desk : 3.0 à 3.2
* Confluence : 5.0 à 5.10

## <a name="installation"></a>Installation

Pour installer le plug-in, procédez comme suit :

1. Connectez-vous à votre instance de Jira ou Confluence en tant qu’administrateur.

2. Accédez à la console d’administration de Jira ou Confluence, puis sélectionnez **Modules complémentaires**.

3. Depuis la place de marché Atlassian, recherchez **Microsoft SAML SSO Plugin**.

   La version appropriée du plug-in s’affiche dans les résultats de la recherche.

4. Sélectionnez le plug-in, puis poursuivez avec l’installation du Gestionnaire de plug-in universel (UPM).

Une fois installé, le plug-in s’affiche dans la section **User Installed Add-ons (Modules complémentaires installés par l’utilisateur)** de **Manage add-ons (Gérer les modules complémentaires)**.

## <a name="plug-in-configuration"></a>Configuration du plug-in

Avant de commencer à utiliser le plug-in, vous devez le configurer. Sélectionnez le plug-in, cliquez sur le bouton **Configurer**, puis indiquez les détails de la configuration.

L’image suivante montre l’écran de configuration dans Jira et Confluence :

![Écran de configuration du plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **URL des métadonnées** : URL permettant d’obtenir les métadonnées de fédération à partir d’Azure AD.

*   **Identificateurs** : URL utilisée par Azure AD pour valider la source de la requête. Elle correspond à l’élément **Identificateur** dans Azure AD. Le plug-in déduit automatiquement cette URL sous la forme https://*<domaine: port>*/.

*   **URL de réponse** : URL de réponse dans votre fournisseur d’identité (IdP) qui initie la connexion SAML. Elle correspond à l’élément **URL de réponse** dans Azure AD. Le plug-in déduit automatiquement cette URL sous la forme https://*<domaine: port>*/plugins/servlet/saml/auth.

*   **URL de connexion** : URL de connexion dans votre fournisseur d’identité (IdP) qui initie la connexion SAML. Elle correspond à l’élément **Connexion** dans Azure AD. Le plug-in déduit automatiquement cette URL sous la forme https://*<domaine: port>*/plugins/servlet/saml/auth.

*   **IdP Entity ID (ID entité IdP)** : identifiant d’entité utilisé par votre fournisseur d’identité. Cette case est renseignée quand l’URL des métadonnées est résolue.

*   **URL de connexion** : URL de connexion fournie par votre fournisseur d’identité. Cette case est renseignée à partir d’Azure AD quand l’URL des métadonnées est résolue.

*   **URL de déconnexion** : URL de déconnexion fournie par votre fournisseur d’identité. Cette case est renseignée à partir d’Azure AD quand l’URL des métadonnées est résolue.

*   **Certificat X.509** : certificat X.509 de votre fournisseur d’identité. Cette case est renseignée à partir d’Azure AD quand l’URL des métadonnées est résolue.

*   **Nom de bouton de connexion** : nom du bouton de connexion que votre organisation souhaite que les utilisateurs visualisent sur la page de connexion.

*   **Emplacements des ID utilisateur SAML** : emplacement auquel l’ID d’utilisateur Jira ou Confluence est attendu dans la réponse SAML. Il peut apparaître sous la forme **NameID** ou dans un nom d’attribut personnalisé.

*   **Nom d’attribut** : nom de l’attribut sur lequel l’ID utilisateur est attendu.

*   **Enable Home Realm Discovery (Activer la découverte du domaine d’accueil)** : sélection à effectuer si l’entreprise utilise une connexion basée sur Active Directory Federation Services (AD FS).

*   **Nom de domaine** : nom de domaine si la connexion est basée sur AD FS.

*   **Enable Single Signout (Activer la déconnexion unique)** : sélection à effectuer si vous souhaitez vous déconnecter d’Azure AD lorsqu’un utilisateur se déconnecte de Jira ou Confluence.

## <a name="troubleshooting"></a>Résolution de problèmes

* **Vous obtenez plusieurs erreurs de certificat** : connectez-vous à Azure AD et supprimez les différents certificats disponibles au niveau de l’application. Vérifiez qu’il n’en reste qu’un.

* **Un certificat est sur le point d’expirer dans Azure AD** : les modules complémentaires prennent en charge la substitution automatique du certificat. Quand un certificat est sur le point d’expirer, un nouveau certificat doit être marqué comme actif, et les certificats inutilisés doivent être supprimés. Quand un utilisateur tente de se connecter à Jira dans ce scénario, le plug-in extrait le nouveau certificat et l’enregistre.

* **Comment désactiver WebSudo (désactiver la session administrateur sécurisée)** :

  * Pour Jira, les sessions administrateur sécurisées (c’est-à-dire la confirmation du mot de passe avant d’accéder aux fonctions d’administration) sont activées par défaut. Si vous voulez supprimer cette possibilité dans votre instance Jira, indiquez la ligne suivante dans votre fichier jira-config.properties : `ira.websudo.is.disabled = true`

  * Pour Confluence, suivez les étapes indiquées sur le [site de support technique de Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Les champs censés être renseignés par l’URL des métadonnées ne le sont pas** :

  * Vérifiez si l’URL est correcte. Vérifiez si vous avez mappé le locataire et l’ID d’application qui conviennent.

  * Accédez à l’URL dans un navigateur et vérifiez si vous recevez le fichier XML des métadonnées de fédération.

* **Une erreur interne du serveur s’est produite** : consultez les journaux dans le répertoire des journaux de l’installation. Si vous obtenez cette erreur quand l’utilisateur essaie de se connecter à l’aide de l’authentification unique Azure AD, vous pouvez partager les journaux avec l’équipe de support technique.

* **Une erreur du type « Identifiant d’utilisateur introuvable » apparaît lorsque l’utilisateur tente de se connecter** : créez l’ID d’utilisateur dans Jira ou Confluence.

* **Une erreur du type « Application introuvable » apparaît dans Azure AD** : voyez si l’URL appropriée est bien mappée à l’application dans Azure AD.

* **Vous avez besoin d’aide** : contactez [l’équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Elle répond dans un délai de 24 à 48 heures.

  Vous pouvez également créer un ticket de support auprès de Microsoft par le biais du portail Azure.

## <a name="plug-in-faq"></a>FAQ sur les plug-ins

Reportez-vous aux FAQ ci-dessous si vous avez une requête concernant ce plug-in.

### <a name="what-does-the-plug-in-do"></a>Que fait le plug-in ?

Le plug-in fournit une fonctionnalité d’authentification unique (SSO) pour les logiciels locaux Jira (notamment Jira Core, Jira Software et Jira Service Desk) et Confluence d’Atlassian. Le plug-in fonctionne avec Azure Active Directory (Azure AD) comme fournisseur d’identité (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Avec quels produits Atlassian le plug-in fonctionne-t-il ?

Le plug-in fonctionne avec les versions locales de Jira et Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Le plug-in fonctionne-t-il sur les versions cloud ?

Non. Le plug-in prend uniquement en charge les versions locales de Jira et Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Quelles versions de Jira et Confluence le plug-in prend-il en charge ?

Le plug-in prend en charge les versions suivantes :

* Jira Core et Software : 6.0 à 7.8
* Jira Service Desk : 3.0 à 3.2
* Confluence : 5.0 à 5.10

### <a name="is-the-plug-in-free-or-paid"></a>Le plug-in est-il gratuit ou payant ?

Il s’agit d’un module complémentaire gratuit.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Ai-je besoin de redémarrer Jira ou Confluence après avoir déployé le plug-in ?

Un redémarrage n’est pas nécessaire. Vous pouvez commencer à utiliser le plug-in immédiatement.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Comment obtenir du support pour le plug-in ?

Vous pouvez contacter [l’équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) pour tout support nécessaire pour ce plug-in. Elle répond dans un délai de 24 à 48 heures.

Vous pouvez également créer un ticket de support auprès de Microsoft par le biais du portail Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Le plug-in fonctionne-t-il avec une installation de Jira et Confluence sur Mac ou Ubuntu ?

Nous avons testé le plug-in uniquement sur des installations de Jira et Confluence sur des serveurs Windows 64 bits.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Le plug-in fonctionne-t-il avec des IdP autres qu’Azure AD ?

Non. Il fonctionne uniquement avec Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Avec quelle version de SAML le plug-in fonctionne-t-il ?

Il fonctionne avec SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Le plug-in procède-t-il à l’approvisionnement des utilisateurs ?

Non. Le plug-in fournit uniquement l’authentification unique basée sur SAML 2.0. L’utilisateur doit être provisionné dans l’application avant la connexion avec authentification unique.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Le plug-in prend-il en charge les versions cluster de Jira et Confluence ?

Non. Le plug-in fonctionne avec les versions locales de Jira et Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Le plug-in fonctionne-t-il avec les versions HTTP de Jira et Confluence ?

Non. Le plug-in fonctionne uniquement avec les installations HTTPS.