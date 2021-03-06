---
title: Webhooks Azure Container Registry
description: Découvrez comment utiliser des webhooks pour déclencher des événements quand certaines actions sont exécutées dans l’un des dépôts de votre registre.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/20/2017
ms.author: danlep
ms.openlocfilehash: 350ae16aa66276e7e64c5c35718dca74a70f499e
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854086"
---
# <a name="using-azure-container-registry-webhooks"></a>Utilisation de webhooks Azure Container Registry

Un registre Azure Container Registry stocke et gère des images conteneurs Docker privé, de la même manière qu’un Hub Docker stocke des images de Docker public. Vous pouvez utiliser des webhooks pour déclencher des événements quand certaines actions sont exécutées dans l’un des dépôts de votre registre. Les webhooks peuvent répondre à des événements au niveau du registre ou être réduits à une balise de dépôt spécifique.

Pour plus d’informations sur les requêtes de webhook, consultez la [référence de schéma du webhook Azure Container Registry](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Prérequis

* Azure Container Registry : créez un Registre de conteneur dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md).
* Interface CLI Docker : pour configurer votre ordinateur local comme hôte Docker et accéder aux commandes de l’interface CLI Docker, installez le [moteur Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Créer un portail Azure webhook

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Accédez au registre de conteneurs dans lequel vous souhaitez créer un webhook.
1. Sous **SERVICES**, sélectionnez **Webhooks**.
1. Dans la barre d’outils de webhook, sélectionnez **Ajouter**.
1. Remplissez le formulaire *Créer un webhook* avec les informations suivantes :

| Valeur | Description |
|---|---|
| NOM | Nom que vous voulez donner au webhook. Il peut contenir uniquement des caractères minuscules et des chiffres, et doit comporter de 5 à 50 caractères. |
| URI de service | URI auquel le webhook doit envoyer des notifications POST. |
| En-têtes personnalisés | En-têtes que vous souhaitez passer avec la demande POST. Ils doivent être au format « clé : valeur ». |
| Actions de déclencheur | Actions qui déclenchent le webhook. Actuellement, les webhooks peuvent être déclenchés par des actions d’envoi et/ou de suppression appliquées à une image. |
| Statut | État du webhook après sa création. Il est activé par défaut. |
| Scope | Étendue à laquelle le webhook fonctionne. Par défaut, l’étendue couvre tous les événements dans le registre. Vous pouvez la spécifier pour un dépôt ou une balise en utilisant le format « dépôt:balise ». |

Exemple de formulaire webhook :

![Interface utilisateur de création de webhook ACR dans le portail Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Créer un webhook avec Azure CLI

Pour créer un webhook à l’aide d’Azure CLI, utilisez la commande [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Tester un webhook

### <a name="azure-portal"></a>Portail Azure

Avant d’utiliser le webhook sur des actions d’envoi ou de suppression appliquées à une image conteneur, vous pouvez le tester à l’aide du bouton **Ping**. La commande Ping envoie une demande POST générique au point de terminaison spécifié et enregistre la réponse. Cette fonctionnalité peut vous aider à vérifier que vous avez correctement configuré le webhook.

1. Sélectionnez le webhook à tester.
2. Dans la barre d’outils supérieure, sélectionnez **Ping**.
3. Vérifiez la réponse du point de terminaison dans la colonne **État HTTP**.

![Interface utilisateur de création de webhook ACR dans le portail Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Pour tester un webhook d’ACR avec Azure CLI, utilisez la commande [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Pour afficher les résultats, utilisez la commande [az acr webhook list-events](/cli/azure/acr/webhook#list-events).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Supprimer un webhook

### <a name="azure-portal"></a>Portail Azure

Vous pouvez supprimer chaque webhook en le sélectionnant, puis en appuyant sur le bouton **Supprimer** dans le portail Azure.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="webhook-schema-reference"></a>Référence de schéma de webhook

Pour plus d’informations sur le format et les propriétés des charges utiles d’événement JSON émises par Azure Container Registry, consultez la référence de schéma de webhook :

[Référence de schéma de webhook Azure Container Registry](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Événements Event Grid

Outre les événements de webhook du registre natif abordés dans cet article, Azure Container Registry peut émettre des événements vers Event Grid :

[Démarrage rapide : Envoyer des événements de registre de conteneurs à Event Grid](container-registry-event-grid-quickstart.md)