---
title: Déployer Azure Blockchain Workbench
description: Comment déployer Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 3ee169e4139f5fc9cd4208c53c4997d50521fed7
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241228"
---
# <a name="deploy-azure-blockchain-workbench"></a>Déployer Azure Blockchain Workbench

Azure Blockchain Workbench est déployé à l’aide d’un modèle de solution dans la Place de marché Microsoft Azure. Ce modèle simplifie le déploiement des composants nécessaires à la création d’applications blockchain. Une fois déployé, Blockchain Workbench permet d’accéder aux applications clientes afin de créer et gérer les utilisateurs et les applications blockchain.

Pour plus d’informations sur les composants Blockchain Workbench, consultez [Architecture Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Préparation du déploiement

Blockchain Workbench vous permet de déployer un registre blockchain, ainsi qu’un ensemble de services Azure pertinents plus souvent utilisés pour générer une application basée sur les blockchains. Le déploiement de Blockchain Workbench entraîne l’approvisionnement des services Azure suivants au sein d’un groupe de ressources dans votre abonnement Azure.

* 1 rubrique Event Grid
* 1 espace de noms Service Bus
* 1 Application Insights
* 1 SQL Database (Standard S0)
* 2 App Services (Standard)
* 2 coffres Azure Key Vault
* 2 comptes Stockage Azure (LRS Standard)
* 2 groupes de machines virtuelles identiques (pour les nœuds Validateur et Worker)
* 2 réseaux virtuels (y compris un équilibreur de charge, un groupe de sécurité réseau et une adresse IP publique pour chaque réseau virtuel)
* Facultatif : Azure Monitor

Voici un exemple de déploiement créé dans le groupe de ressources **myblockchain**.

![Exemple de déploiement](media/deploy/example-deployment.png)

Le coût associé à Blockchain Workbench est un agrégat du coût des services Azure sous-jacents. Les informations de tarification pour les services Azure peuvent être calculées à l’aide de la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench requiert plusieurs conditions préalables avant le déploiement. Ces conditions préalables incluent les inscriptions de configuration et d’application Azure AD.

### <a name="blockchain-workbench-api-app-registration"></a>Inscription d’application API Blockchain Workbench

Le déploiement de Blockchain Workbench nécessite l’inscription d’une application Azure AD. Vous avez besoin d’un locataire Azure Active Directory (Azure AD) pour inscrire l’application. Vous pouvez utiliser un locataire existant ou en créer un. Si vous utilisez un locataire Azure AD existant, vous avez besoin d’autorisations suffisantes pour inscrire les applications et accorder les autorisations API Graph au sein d’un locataire Azure AD. Si vous n’avez pas d’autorisations suffisantes dans un locataire Azure AD existant, créez un locataire. 

> [!IMPORTANT]
> La solution Workbench n’a pas besoin d’être déployée dans le même locataire que celui que vous utilisez pour inscrire une application Azure AD. Elle doit être déployée dans un locataire pour lequel vous disposez d’autorisations suffisantes pour déployer des ressources. Pour plus d’informations sur les locataires Azure AD, consultez [Obtention d’un client Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) et [Intégration d’applications dans Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez votre compte en haut à droite, puis basculez vers le locataire Azure AD souhaité. Le locataire doit être locataire de l’administrateur de l’abonnement où Workbench est déployé et vous devez disposer d’autorisations suffisantes pour inscrire les applications.
3. Sélectionnez le service **Azure Active Directory** dans le volet de navigation gauche. Sélectionnez **Inscription d’applications** > **Nouvelle inscription d’application**.

    ![Inscription d'application](media/deploy/app-registration.png)

4. Fournissez un **nom** et une **URL de connexion** pour l’application. Vous pouvez utiliser des valeurs d’espace réservé dans la mesure où les valeurs sont modifiées pendant le déploiement. 

    ![Créer une inscription d’application](media/deploy/app-registration-create.png)

    |Paramètre  | Valeur  |
    |---------|---------|
    |NOM | `Blockchain API` |
    |Type d’application |Application web / API|
    |URL de connexion | `https://blockchainapi` |

5. Sélectionnez **Créer** pour inscrire l’application Azure AD.

### <a name="modify-application-manifest"></a>Modifier le manifeste de l’application

Ensuite, vous devez modifier le manifeste de l’application pour utiliser des rôles d’application dans Azure AD afin de spécifier les administrateurs Blockchain Workbench.  Pour plus d’informations sur les manifestes de l’application, consultez [Manifeste de l’application Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Pour l’application que vous avez inscrite, sélectionnez **Manifeste** dans le volet de détails de l’application inscrite.
2. Générez un GUID. Vous pouvez générer un GUID à l’aide de la commande PowerShell [guid] :: NewGuid () ou de la cmdlet New-GUID. Une autre option consiste à utiliser un site web générateur de GUID.
3. Vous vous apprêtez à mettre à jour la section **appRoles** du manifeste. Dans le volet d’édition du manifeste, sélectionnez **Modifier** et remplacer `"appRoles": []` par le JSON fourni. Remplacez la valeur du champ **id** par le GUID que vous avez généré. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Modifier le manifeste](media/deploy/edit-manifest.png)

    > [!IMPORTANT]
    > La valeur **Administrateur** est nécessaire pour identifier les administrateurs Blockchain Workbench.

4.  Cliquez sur **Enregistrer** pour enregistrer les modifications apportées au manifeste de l’application.

### <a name="add-graph-api-required-permissions"></a>Ajouter les autorisations requises pour l’API Graph

L’application API doit demander l’autorisation de l’utilisateur pour accéder au répertoire. Définissez l’autorisation requise suivante pour l’application API :

1. Dans l’inscription d’application API Blockchain, sélectionnez **Paramètres > Autorisations requises > Sélectionner une API > Microsoft Graph**.

    ![Sélectionnez une API](media/deploy/client-app-select-api.png)

    Cliquez sur **Sélectionner**.

2. Dans **Activer l’accès** sous **Autorisations de l’application**, choisissez **Lire les profils complets de tous les utilisateurs**.

    ![Activer l’accès](media/deploy/client-app-read-perms.png)

    Cliquez sur **Sélectionner**, puis sur **Terminé**.

3. Dans **Autorisations requises**, sélectionnez **Accorder des autorisations**, puis sélectionnez **Oui** pour la demande de vérification.

   ![Accorder des autorisations](media/deploy/client-app-grant-permissions.png)

   L’octroi d’autorisation permet à Blockchain Workbench d’accéder aux utilisateurs dans le répertoire. L’autorisation de lecture est requise pour la recherche et l’ajout de membres dans Blockchain Workbench.

### <a name="add-graph-api-key-to-application"></a>Ajouter la clé de l’API Graph à l’application

Blockchain Workbench utilise Azure AD comme système principal de gestion d’identité pour les utilisateurs qui interagissent avec les applications blockchain. Pour que Blockchain Workbench puisse accéder à Azure AD et récupérer les informations utilisateur, comme les noms et les adresses e-mail, vous devez ajouter une clé d’accès. Blockchain Workbench utilise la clé pour l’authentification auprès d’Azure AD.

1. Pour l’application que vous avez inscrite, sélectionnez **Paramètres** dans le volet de détails de l’application inscrite.
2. Sélectionnez **Clés**.
3. Ajoutez une nouvelle clé en spécifiant une clé **description** et en choisissant la durée d’**expiration**. 

    ![Créer une clé](media/deploy/app-key-create.png)

    |Paramètre  | Valeur  |
    |---------|---------|
    | Description | `Service` |
    | Expires | Choisissez une durée d’expiration |

4. Sélectionnez **Enregistrer**. 
5. Copiez la valeur de la clé et stockez-la pour une utilisation ultérieure. Vous en avez besoin pour le déploiement.

    > [!IMPORTANT]
    >  Si vous n’enregistrez pas la clé pour le déploiement, vous devrez générer une nouvelle clé. Vous ne pourrez pas récupérer la valeur de clé plus tard sur le portail.

### <a name="get-application-id"></a>Obtenir l’ID de l’application

L’ID de l’application et les informations du locataire sont requis pour le déploiement. Rassemblez et stockez les informations pour les utiliser pendant le déploiement.

1. Pour l’application que vous avez inscrite, sélectionnez **Paramètres** > **Propriétés**.
2.  Dans le volet **Propriétés**, copiez et stockez les valeurs suivantes pour une utilisation ultérieure lors du déploiement.

    ![Propriétés de l’application API](media/deploy/app-properties.png)

    | Paramètre à stocker  | Utiliser pendant le déploiement |
    |------------------|-------------------|
    | ID de l'application | Configuration Azure Active Directory > ID de l’application |

### <a name="get-tenant-domain-name"></a>Obtenir un nom de domaine de locataire

Rassemblez et stockez le nom de domaine du locataire Active Directory où les applications sont inscrites. 

Sélectionnez le service **Azure Active Directory** dans le volet de navigation gauche. Sélectionnez **Noms de domaine personnalisés**. Copiez et stockez le nom de domaine.

![Nom de domaine](media/deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Déployer Blockchain Workbench

Une fois que les étapes préalables requises ont été exécutées, vous êtes prêt à déployer Blockchain Workbench. Les sections suivantes expliquent comment déployer l’infrastructure.

1.  Connectez-vous au [Portail Azure](https://portal.azure.com).
2.  Sélectionnez votre compte en haut à droite, puis basculez vers le locataire Azure AD où vous voulez déployer Azure Blockchain Workbench.
3.  Dans le volet de gauche, sélectionnez **Créer une ressource**. Recherchez `Azure Blockchain Workbench` dans la barre de recherche **Rechercher dans la Place de marché**. 

    ![Barre de recherche de la Place de marché](media/deploy/marketplace-search-bar.png)

4.  Sélectionnez **Azure Blockchain Workbench**.

    ![Résultats de recherche de la Place de marché](media/deploy/marketplace-search-results.png)

4.  Sélectionnez **Créer**.
5.  Renseignez les paramètres de base.

    ![Créer Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Paramètre | Description  |
    |---------|--------------|
    | Préfixe de ressource | Identificateur unique court pour votre déploiement. Cette valeur est utilisée comme base pour les noms des ressources. |
    | Nom d’utilisateur de la machine virtuelle | Le nom d’utilisateur sert en tant qu’administrateur pour toutes les machines virtuelles. |
    | Type d'authentification | Indiquez si vous souhaitez utiliser un mot de passe ou une clé pour la connexion aux machines virtuelles. |
    | Mot de passe | Le mot de passe est utilisé pour la connexion aux machines virtuelles. |
    | SSH | Utilisez une clé publique RSA au format ligne unique et commençant par **ssh-rsa** ou utilisez le format PEM multiligne. Vous pouvez générer des clés SSH à l’aide de `ssh-keygen` sur Linux et OS X, ou à l’aide de PuTTYGen sur Windows. Pour plus d’informations sur les clés SSH, consultez [Comment utiliser des clés SSH avec Windows sur Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Mot de passe de base de données / Confirmer le mot de passe de base de données | Spécifiez le mot de passe à utiliser pour accéder à la base de données créée dans le cadre du déploiement. |
    | Région du déploiement | Spécifiez où déployer les ressources Blockchain Workbench. Pour une disponibilité optimale, cette région doit correspondre au paramètre **Emplacement**. |
    | Abonnement | Spécifiez l’abonnement Azure que vous souhaitez utiliser pour votre déploiement. |
    | Groupes de ressources | Créez un groupe de ressources en sélectionnant **Créer** et donnez un nom unique au groupe de ressources. |
    | Lieu | Spécifiez la région où vous souhaitez déployer l’infrastructure. |

6.  Sélectionnez **OK** à la fin de la section de configuration du paramètre de base.

7.  Terminez la **configuration d’Azure Active Directory**.

    ![Configuration Azure AD](media/deploy/blockchain-workbench-settings-aad.png)

    | Paramètre | Description  |
    |---------|--------------|
    | Nom de domaine | Utilisez le locataire Azure AD collecté dans la section Configuration requise sous [Obtenir un nom de domaine de locataire](#get-tenant-domain-name). |
    | ID de l'application | Utilisez l’ID d’application issu de l’inscription d’application cliente Blockchain collectée dans la section Configuration requise sous [Obtenir l’ID de l’application](#get-application-id). |
    | Clé de l’application | Utilisez la clé de l’application issue de l’inscription d’application cliente Blockchain collectée dans la section Configuration requise sous [Ajouter la clé de l’API Graph à l’application](#add-graph-api-key-to-application). |


8.  Cliquez sur **OK** à la fin de la section de configuration des paramètres Azure AD.

9.  Dans **Paramètres réseau et niveau de performance**, choisissez si vous souhaitez créer un réseau blockchain ou utiliser un réseau blockchain de preuve d’autorité existant.

    Pour **Créer** :

    L’option *Créer* permet de créer un ensemble de nœuds Ethereum Proof-of Authority (PoA) dans l’abonnement d’un seul membre. 

    ![Paramètres réseau et niveau de performance](media/deploy/blockchain-workbench-settings-network-new.png)

    | Paramètre | Description  |
    |---------|--------------|
    | Nombre de nœuds de blockchain | Choisissez le nombre de nœuds de validation Ethereum PoA à déployer dans votre réseau. |
    | Performances de stockage | Choisissez les performances de stockage de machine virtuelle préférées pour votre réseau blockchain. |
    | Taille de la machine virtuelle | Choisissez la taille de machine virtuelle préférée pour votre réseau blockchain. |

    Pour **Utiliser l’existant** :

    L’option *Utiliser l’existant* vous permet de spécifier un réseau blockchain Ethereum Proof-of-Authority (PoA). Les points de terminaison doivent répondre aux exigences suivantes.

    * Le point de terminaison doit être un réseau blockchain Ethereum Proof-of-Authority (PoA).
    * Le point de terminaison doit être accessible publiquement sur le réseau.
    * Le réseau blockchain PoA doit être configuré de sorte que le prix du gaz soit défini sur la valeur zéro (remarque : les comptes Blockchain Workbench ne sont pas financés. Si des fonds sont requis, les transactions échoueront.).

    ![Paramètres réseau et niveau de performance](media/deploy/blockchain-workbench-settings-network-existing.png)

    | Paramètre | Description  |
    |---------|--------------|
    | Point de terminaison Ethereum RPC | Indiquez le point de terminaison RPC d’un réseau blockchain PoA existant. Le point de terminaison commence par http:// et se termine par un numéro de port. Par exemple, `http://contoso-chain.onmicrosoft.com:8545` |
    | Performances de stockage | Choisissez les performances de stockage de machine virtuelle préférées pour votre réseau blockchain. |
    | Taille de la machine virtuelle | Choisissez la taille de machine virtuelle préférée pour votre réseau blockchain. |

10. Sélectionnez **OK** pour valider les paramètres réseau et le niveau de performance.

11. Terminez la définition des paramètres **Azure Monitor**.

    ![Azure Monitor](media/deploy/blockchain-workbench-settings-oms.png)

    | Paramètre | Description  |
    |---------|--------------|
    | Surveillance | Indiquer si vous souhaitez autoriser Azure Monitor à surveiller votre réseau blockchain |
    | Se connecter à une instance Log Analytics existante | Indiquez si vous souhaitez utiliser une instance Log Analytics existante ou en créer une. Si vous utilisez une instance existante, entrez votre ID d’espace de travail et votre clé primaire. |

12. Cliquez sur **OK** à la fin de la section Azure Monitor.

13. Passez en revue le récapitulatif pour vérifier que vos paramètres sont corrects.

    ![Résumé](media/deploy/blockchain-workbench-summary.png)

14. Sélectionnez **Créer** pour accepter les conditions et déployer votre Azure Blockchain Workbench.

Le déploiement peut prendre jusqu’à 90 minutes. Vous pouvez utiliser le portail Azure pour surveiller la progression. Dans le groupe de ressources nouvellement créé, sélectionnez **Déploiements > Vue d’ensemble** pour connaître l’état des artefacts déployés.

## <a name="blockchain-workbench-web-url"></a>URL web Blockchain Workbench

Une fois le déploiement de Blockchain Workbench terminé, un nouveau groupe de ressources contient vos ressources Blockchain Workbench. Les services Blockchain Workbench sont accessibles par le biais d’une URL web. Les étapes suivantes vous montrent comment récupérer l’URL web de l’infrastructure déployée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**
3. Choisissez le nom du groupe de ressources spécifié lors du déploiement de Blockchain Workbench.
4. Cliquez sur l’en-tête de colonne **TYPE** pour trier la liste par ordre alphabétique et par type.
5. Deux ressources présentent le type **App Service**. Sélectionnez la ressource de type **App Service** *sans* le suffixe « -api ».

    ![Liste App Service](media/deploy/resource-group-list.png)

6.  Dans la section **Bases** App Service, copiez la valeur **URL** qui représente l’URL web sur votre Blockchain Workbench déployé.

    ![Base App Service](media/deploy/app-service.png)

Pour associer un nom de domaine personnalisé avec Blockchain Workbench, voir [Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Configuration de l’URL de réponse

Une fois Azure Blockchain Workbench déployé, l’étape suivante consiste à s’assurer que l’application cliente Azure Active Directory (Azure AD) est inscrite sur la bonne **URL de réponse** de l’URL web du Blockchain Workbench déployé.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Vérifiez que vous êtes dans le locataire sur lequel vous avez inscrit l’application cliente Azure AD.
3. Sélectionnez le service **Azure Active Directory** dans le volet de navigation gauche. Sélectionnez **Inscriptions d’applications**.
4. Sélectionnez l’application cliente Azure AD que vous avez enregistrée dans la section Configuration requise.
5. Sélectionnez **Paramètres > URL de réponse**.
6. Spécifiez l’URL web principale du déploiement Azure Blockchain Workbench que vous avez récupérée dans la section **Get the Azure Blockchain Workbench Web URL (Obtenir l’URL Web Azure Blockchain Workbench)**. L’URL de réponse est préfixée par `https://`. Par exemple, `https://myblockchain2-7v75.azurewebsites.net`

    ![URL de réponse](media/deploy/configure-reply-url.png)

7. Sélectionnez **Enregistrer** pour mettre à jour l’inscription du client.

## <a name="remove-a-deployment"></a>Supprimer un déploiement

Lorsqu’un déploiement n’est plus nécessaire, vous pouvez le supprimer en supprimant le groupe de ressources Blockchain Workbench.

1. Dans le portail Azure, accédez à **Groupe de ressources** dans le volet de navigation de gauche et sélectionnez le groupe de ressources que vous souhaitez supprimer. 
2. Sélectionnez **Supprimer le groupe de ressources**. Validez la suppression en entrant le nom du groupe de ressources, puis sélectionnez **Supprimer**.

    ![Supprimer un groupe de ressources](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article sur les procédures, vous avez déployé Azure Blockchain Workbench. Pour apprendre à créer une application blockchain, passez à l’article suivant.

> [!div class="nextstepaction"]
> [Créer une application blockchain dans Azure Blockchain Workbench](create-app.md)
