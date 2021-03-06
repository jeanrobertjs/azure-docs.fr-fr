---
title: Suivre l’utilisation d’un laboratoire dans Azure Lab Services | Microsoft Docs
description: Dans ce didacticiel, vous (en tant que créateur et propriétaire d’un laboratoire) allez suivre l’utilisation de votre laboratoire.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 710d157dcf4c6d060e59bcfbb69455e2ddc91bdd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450128"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Didacticiel : suivre l’utilisation d’un labo dans Azure Lab Service
Ce didacticiel vous montre comment un créateur ou propriétaire de laboratoire peut suivre l’utilisation d’un laboratoire.

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Afficher les utilisateurs inscrits à votre laboratoire
> * Afficher l’utilisation des machines virtuelles dans le laboratoire
> * Gérer les machines virtuelles des étudiants 


## <a name="view-users-registered-with-the-lab"></a>Afficher les utilisateurs inscrits au laboratoire

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft.
3. Sur la page **Mes laboratoires**, sélectionnez le laboratoire pour lequel vous souhaitez suivre l’utilisation. 
4. Sélectionnez l’onglet **Utilisateurs**. Vous visualisez les étudiants inscrits à votre laboratoire. Sélectionnez le **lien d’inscription**, copiez-le, puis envoyez-le à tout nouvel étudiant qui ne s’est pas encore inscrit auprès de votre laboratoire. 

    ![Utilisateurs inscrits](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Afficher l’utilisation des machines virtuelles dans le laboratoire 

1. Dans le menu de gauche, sélectionnez **Machines virtuelles**. 
2. Confirmez que vous visualisez bien l’état des machines virtuelles ainsi que le nombre d’heures d’exécution de celles-ci. Le temps passé sur la machine virtuelle d’un étudiant n’est pas comptabilisé dans le temps d’utilisation indiqué dans la dernière colonne. 

    ![Utilisation de machines virtuelles](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gérer les machines virtuelles des étudiants 
Le fait de pointer la souris sur une ligne dans la liste des machines virtuelles vous permet de consulter les commandes pour effectuer les tâches suivantes : 

- Se connecter à une machine virtuelle
- Démarrer une machine virtuelle
- Arrêter une machine virtuelle
- Supprimer une machine virtuelle

![Commandes des machines virtuelles](../media/tutorial-track-usage/vm-controls.png) 



## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à rechercher les utilisateurs qui se sont inscrits à votre laboratoire, à suivre l’utilisation des machines virtuelles dans le laboratoire et à gérer ces machines dans le laboratoire.

Pour en savoir plus sur les laboratoires de salle de classe, consultez les rubriques de la section [Guides de procédures](how-to-manage-lab-accounts.md).
