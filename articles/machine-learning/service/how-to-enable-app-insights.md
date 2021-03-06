---
title: Activer Application Insights pour le service Azure Machine Learning en production
description: Découvrez comment configurer Application Insights pour le service Azure Machine Learning en vue d’un déploiement dans Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: fa425a5ecd8cf8f4c7b3516534b4c4f0f4257850
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085340"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Superviser vos modèles Azure Machine Learning en production avec Application Insights

Dans cet article, vous allez apprendre à configurer Application Insights pour votre service Azure Machine Learning. Application Insights vous permet de superviser :
* Les taux de demande, les temps de réponse et les taux d’échec
* Les taux de dépendance, les temps de réponse et les taux d’échec
* Les exceptions.

[En savoir plus sur Application Insights](../../application-insights/app-insights-overview.md) 

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Un espace de travail Azure Machine Learning, un répertoire local contenant vos scripts, et le SDK Azure Machine Learning pour Python. Pour savoir comment obtenir ces prérequis, consultez [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).
* Un modèle Machine Learning entraîné à déployer sur Azure Kubernetes Service (AKS). Si vous n’en avez pas, consultez le tutoriel [Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).
* Un [cluster AKS](how-to-deploy-to-aks.md).

## <a name="enable-and-disable-in-the-portal"></a>Activer et désactiver dans le portail

Vous pouvez activer et désactiver Application Insights dans le portail Azure.

### <a name="enable"></a>Activer

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.

1. Sous l’onglet **Déploiements**, sélectionnez le service dans lequel vous souhaitez activer Application Insights.

   [![Liste des services sous l’onglet Déploiements](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Sélectionnez **Modifier**.

   [![Bouton Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Dans **Paramètres avancés**, cochez la case **Activer les diagnostics AppInsights**.

   [![Case à cocher pour activer les diagnostics](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications. 

### <a name="disable"></a>Désactiver
1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.
1. Sélectionnez **Déploiements**, sélectionnez le service, puis sélectionnez **Modifier**.

   [![Bouton Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Dans **Paramètres avancés**, décochez la case **Activer les diagnostics AppInsights**. 

   [![Case à décocher pour désactiver les diagnostics](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications. 

## <a name="enable-and-disable-from-the-sdk"></a>Activer et désactiver à partir du SDK

### <a name="update-a-deployed-service"></a>Mettre à jour un service déployé
1. Recherchez le service dans votre espace de travail. La valeur de `ws` correspond au nom de votre espace de travail.

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Mettez à jour votre service et activez Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Journaliser des traces personnalisées dans votre service
Si vous voulez journaliser des traces personnalisées, suivez le [processus de déploiement standard pour AKS](how-to-deploy-to-aks.md). Ensuite :

1. Mettez à jour le fichier de scoring en ajoutant des instructions print.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Mettez à jour la configuration AKS.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Générez l’image et déployez-la.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Désactiver le suivi dans Python

Pour désactiver Application Insights, utilisez le code suivant :

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Évaluer les données
Les données de votre service sont stockées dans votre compte Application Insights, dans le même groupe de ressources que votre service Azure Machine Learning.
Pour l’afficher :
1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com), puis accédez à votre ressource Application Insights. 
2. L’onglet **Vue d’ensemble** présente l’ensemble des métriques de base de votre service.

   [![Vue d’ensemble](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Pour explorer vos traces personnalisées, sélectionnez **Analytique**.
4. Dans la section du schéma, sélectionnez **Traces**. Ensuite, sélectionnez **Exécuter** pour exécuter votre requête. Les données doivent s’afficher dans un tableau et doivent correspondre aux appels personnalisés de votre fichier de scoring. 

   [![Traces personnalisées](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Pour plus d’informations sur l’utilisation d’Application Insights, consultez [Présentation d’Application Insights](../../application-insights/app-insights-overview.md).
    

## <a name="example-notebook"></a>Exemple de bloc-notes

Le notebook [00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) illustre les concepts présentés dans cet article.  Consultez ce bloc-notes :
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez aussi collecter des données sur vos modèles en production. Lisez l’article [Collecter des données pour des modèles en production](how-to-enable-data-collection.md). 
