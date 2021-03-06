---
title: Tutoriel - Créer et gérer des budgets Azure | Microsoft Docs
description: Ce tutoriel vous aide à planifier et à prendre en compte les coûts de services Azure que vous consommez.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/01/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 50bd22559c3695ac4161932652eb191084e2b46e
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017352"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutoriel : Créer et gérer des budgets Azure

Les budgets dans Cost Management vous aident à planifier et à suivre la comptabilité de l’organisation. Avec les budgets, vous pouvez prendre en compte les services Azure que vous consommez ou auxquels vous vous abonnez pendant une période spécifique. Ils vous permettent d’informer les autres utilisateurs de leurs dépenses pour gérer les coûts de manière proactive, ainsi que pour superviser la progression des dépenses. Vous pouvez ainsi voir comment les dépenses progressent. En cas de dépassement des seuils budgétaires que vous avez créés, seules des notifications sont déclenchées. Aucune de vos ressources n’est affectée et votre consommation n’est pas arrêtée. Vous pouvez utiliser des budgets pour comparer et suivre les dépenses lors de l’analyse des coûts.

Les budgets sont automatiquement réinitialisés à la fin d’une période (tous les mois, tous les trimestres ou une fois par an) pour le même montant de budget quand vous sélectionnez une date d’expiration ultérieure. Étant donné qu’ils sont réinitialisés avec le même montant de budget, vous devez créer des budgets distincts quand les montants budgétisés diffèrent pour des périodes ultérieures.

Les exemples de ce tutoriel montrent comment créer et modifier un budget pour un abonnement Azure Contrat Entreprise (EA).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Modifier un budget

## <a name="prerequisites"></a>Prérequis

Les budgets sont disponibles pour tous les clients Azure EA. Vous devez disposer d’un accès en lecture à un abonnement Azure EA pour créer et gérer des budgets. Les comptes de facturation EA ne sont pas pris en charge par les budgets.

Les budgets sont créés individuellement au niveau des abonnements ou des groupes de ressources. Les autorisations Azure suivantes sont prises en charge par abonnement aux budgets par utilisateur et par groupe :

- Propriétaire : peut créer, modifier ou supprimer des budgets pour un abonnement.
- Contributeur : peut créer, modifier ou supprimer ses propres budgets. Peut modifier le montant des budgets créés par d’autres utilisateurs.
- Lecteur : peut afficher les budgets pour lesquels ils disposent des autorisations adéquates.

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Créez un budget dans le portail Azure

Vous pouvez créer un budget d’abonnement Azure pour un mois, un trimestre ou un an. Votre navigation dans le portail Azure détermine si vous créez un budget pour un abonnement ou pour un groupe de ressources.

Dans le portail Azure, accédez à **Gestion des coûts + facturation** &gt; **Abonnements** &gt; Sélectionner un abonnement &gt; **Budgets**. Dans cet exemple, le budget que vous créez est pour l’abonnement que vous avez sélectionné.

Une fois des budgets créés, ils affichent une vue simple de vos dépenses actuelles par rapport à ces budgets.

Cliquez sur **Add**.

![Budgets Cost Management](./media/tutorial-acm-create-budgets/budgets01.png)

Dans la fenêtre **Créer un budget**, entrez un nom de budget et un montant de budget. Choisissez ensuite une période mensuelle, trimestrielle ou annuelle. Ensuite, sélectionnez une date de fin. Les budgets nécessitent au moins un seuil de coût (% du budget) et une adresse e-mail correspondante. Si vous le souhaitez, vous pouvez inclure jusqu’à cinq seuils et cinq adresses e-mail dans un seul budget. Lorsqu’un seuil de budget est atteint, des notifications par e-mail sont normalement reçues en moins de huit heures.

Voici un exemple de création de budget mensuel de 4 500 $. Une alerte par e-mail est générée quand 90 % du budget est atteint.

![Exemple de budget mensuel](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quand vous créez un budget trimestriel, il fonctionne de la même façon qu’un budget mensuel. La différence est que le montant du budget pour le trimestre est divisé de manière équitable entre les trois mois du trimestre. Comme vous pouvez l’imaginer, un montant d’un budget annuel est divisé de manière équitable entre les 12 mois de l’année civile.

Les dépenses actuelles par rapport aux budgets sont mises à jour chaque fois que Cost Management reçoit des données de facturation mises à jour. En général, tous les jours.

![Dépenses actuelles par rapport aux budgets](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Après avoir créé un budget, il est indiqué dans l’analyse des coûts. L’affichage de votre budget par rapport à la tendance de vos dépenses est l’une des premières étapes quand vous commencez à [analyser vos coûts et vos dépenses](quick-acm-cost-analysis.md).

![Budget indiqué dans l’analyse des coûts](./media/tutorial-acm-create-budgets/cost-analysis.png)

Dans l’exemple précédent, vous avez créé un budget pour un abonnement. Toutefois, vous pouvez également créer un budget pour un groupe de ressources. Si vous voulez créer un budget pour un groupe de ressources, accédez à **Gestion des coûts + facturation** &gt; **Abonnements** &gt; sélectionnez un abonnement > **Groupes de ressource** > sélectionnez un groupe de ressources > **Budgets** > puis **Ajouter** un budget.

## <a name="edit-a-budget"></a>Modifier un budget

Selon le niveau d’accès dont vous disposez, vous pouvez modifier un budget pour changer ses propriétés. Dans l’exemple suivant, certaines des propriétés sont en lecture seule, car l’utilisateur dispose uniquement de l’autorisation Contributeur sur l’abonnement. Actuellement, la **Date d’expiration** est désactivée et ne peut pas être modifiée une fois définie.

![Modifier un budget - Autorisation Contributeur](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Modifier un budget

Passez au tutoriel suivant pour créer une exportation récurrente de vos données de gestion des coûts.

> [!div class="nextstepaction"]
> [Créer et gérer des données exportées](tutorial-export-acm-data.md)
