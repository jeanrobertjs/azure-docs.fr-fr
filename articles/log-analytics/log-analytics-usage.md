---
title: Analyser l’utilisation des données dans Log Analytics | Microsoft Docs
description: Utilisez le tableau de bord répertoriant l’utilisation et les coûts estimés de Log Analytics pour déterminer la quantité de données envoyées à Log Analytics et identifier les éléments à l’origine d’augmentations inattendues.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: ad3deaad8c069cfb11bb0eb997d886807ecdb0f8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006496"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analyser l’utilisation des données dans Log Analytics

> [!NOTE]
> Cet article explique comment analyser l’utilisation des données dans Log Analytics.  Pour plus d’informations, consultez les articles suivants.
> - L’article [Manage cost by controlling data volume and retention in Log Analytics](log-analytics-manage-cost-storage.md) (Gérer les coûts en contrôlant le volume et la conservation des données dans Log Analytics) explique comment contrôler ses coûts en modifiant la durée de conservation des données.
> - L’article [Monitoring usage and estimated costs](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) (Surveillance de l’utilisation et estimation des coûts) explique comment visualiser l’utilisation et les coûts estimés avec plusieurs fonctions de surveillance Azure en fonction des différents modèles de tarification. Il explique également comment modifier votre modèle de tarification.

Log Analytics inclut des informations sur la quantité de données collectées, les sources qui envoient les données et les différents types de données envoyées.  Utilisez le tableau de bord **Utilisation de Log Analytics** pour examiner et analyser l’utilisation de données. Le tableau de bord affiche la quantité de données collectées par chaque solution et la quantité de données que vos ordinateurs envoient.

## <a name="understand-the-usage-dashboard"></a>Comprendre le tableau de bord Utilisation
Le tableau de bord **Utilisation de Log Analytics** affiche les informations suivantes :

- Volume de données
    - Volume de données dans le temps (en fonction de la portée temporelle actuelle)
    - Volume de données par solution
    - Données non associées à un ordinateur
- Ordinateurs
    - Ordinateurs envoyant des données
    - Ordinateurs sans données dans les dernières 24 heures
- Offres
    - Nœuds Insight & Analytics
    - Nœuds d’automatisation et de contrôle
    - Nœuds de sécurité  
- Performances
    - Temps nécessaire pour recueillir et indexer les données  
- Liste de requêtes

![Tableau de bord répertoriant les coûts et l’utilisation](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>
)

### <a name="to-work-with-usage-data"></a>Utilisation des données d’utilisation
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.<br><br> ![Portail Azure](media/log-analytics-usage/azure-portal-01.png)<br><br>  
3. Dans votre liste d’espaces de travail Log Analytics, sélectionnez un espace de travail.
4. Sélectionnez **Utilisation et estimation des coûts** dans la liste du volet gauche.
5. Dans le tableau de bord **Utilisation et estimation des coûts**, vous pouvez modifier l’intervalle de temps en sélectionnant **Heure : Dernières 24 heures**.<br><br> ![Intervalle de temps](./media/log-analytics-usage/usage-time-filter-01.png)<br><br>
6. Affichez les panneaux de catégorie d’utilisation qui montrent les domaines qui vous intéressent. Choisissez un panneau, puis cliquez dessus pour afficher plus de détails dans [Recherche de journal](log-analytics-queries.md).<br><br> ![Exemple de kpi d’utilisation des données](media/log-analytics-usage/data-volume-kpi-01.png)<br><br>
7. Dans le tableau de bord Recherche de journal, passez en revue les résultats renvoyés par la recherche.<br><br> ![exemple de recherche de journal d’utilisation](./media/log-analytics-usage/usage-log-search-01.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Créer une alerte lorsque la collection de données est plus volumineuse que prévu
Cette section décrit la création d’une alerte si :
- Le volume de données dépasse une quantité spécifiée.
- Le volume de données est censé dépasser une quantité spécifiée.

Les Alertes Azure prennent en charge les [alertes de journal](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) qui utilisent des requêtes de recherche. 

La requête suivante obtient un résultat quand plus de 100 Go de données sont collectés dans les dernières 24 heures :

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

La requête suivante utilise une formule simple pour prévoir le moment où plus de 100 Go de données seront envoyés en une journée : 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Pour alerter sur un volume de données différent, remplacez la valeur de 100 dans les requêtes par le nombre de Go pour lequel vous souhaitez créer une alerte.

Utilisez les étapes décrites dans [Création d’une alerte de journal](../monitoring-and-diagnostics/alert-metric.md) pour être averti lorsque la collection de données est plus volumineuse que prévu.

Lors de la création de l’alerte pour la première requête, lorsque plus de 100 Go de données sont collectés en 24 heures, définissez :  

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - La **logique d’alerte** est **basée sur**  le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *1440* minutes et **fréquence des alertes** toutes les *60* minutes comme les données d’utilisation ne se mettent à jour qu’une fois par heure.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** sur *Data volume greater than 100 GB in 24 hours* (Volume de données supérieur à 10 Go en 24 heures)
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé.

Lors de la création de l’alerte pour la seconde requête, lorsqu’il est prévu que plus de 100 Go de données seront collectés en 24 heures, définissez :

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - La **logique d’alerte** est **basée sur**  le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *180* minutes et **fréquence des alertes** toutes les *60* minutes comme les données d’utilisation ne se mettent à jour qu’une fois par heure.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** à *Data volume expected to greater than 100 GB in 24 hours* (Volume de données attendu supérieur à 10 Go en 24 heures)
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé.

Lorsque vous recevez une alerte, utilisez les étapes de la section suivante pour résoudre les problèmes à l’origine d’une utilisation plus importante que prévu.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Résolution des problèmes à l’origine d’une utilisation plus importante que prévu
Le tableau de bord Utilisation vous aide à identifier pourquoi l’utilisation (et donc les coûts) est plus importante que ce que vous attendez.

Une utilisation plus importante est due à l’un des éléments suivants, voire les deux :
- Plus de données que prévu sont envoyées à Log Analytics
- Plus de nœuds que prévu envoient des données à Log Analytics

### <a name="check-if-there-is-more-data-than-expected"></a>Vérifier s’il y a plus de données que prévu 
Deux sections clés de la page Utilisation vous permettent de déterminer ce qui est à l’origine du trop grand nombre de données collectées.

Le graphique *Volume de données dans le temps* représente le volume total de données envoyées et les ordinateurs envoyant le plus de données. Le graphique en haut vous permet de voir si votre utilisation des données globales augmente, reste stable ou diminue. La liste des ordinateurs montre les 10 ordinateurs envoyant le plus de données.

Le graphique *Volume de données par solution* représente le volume de données envoyé par chaque solution et les solutions envoyant le plus de données. Le graphique en haut montre affiche le volume total des données envoyées par chaque solution au fil du temps. Ces informations vous permettent de déterminer si une solution envoie plus de données, environ la même quantité de données, ou moins de données au fil du temps. La liste des solutions montre les 10 solutions envoyant le plus de données. 

Ces deux graphiques affichent toutes les données. Certaines données sont facturables, et les autres données sont libres. Pour vous concentrer uniquement sur les données facturables, modifiez la requête sur la page de recherche pour inclure `IsBillable=true`.  

![graphiques de volume de données](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Examinez le graphique *Volume de données dans le temps*. Pour voir les solutions et les types de données qui envoient le plus de données d’un ordinateur spécifique, cliquez sur le nom de l’ordinateur. Cliquez sur le nom du premier ordinateur dans la liste.

Dans la capture d’écran suivante, le type de données *Gestion des journaux/Performance* envoie le plus de données pour l’ordinateur.<br><br> ![volume de données pour un ordinateur](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Ensuite, revenez au tableau de bord *Utilisation* et examinez le graphique *Volume de données par solution*. Pour voir les ordinateurs envoyant le plus de données pour une solution, cliquez sur le nom de la solution dans la liste. Cliquez sur le nom de la première solution dans la liste. 

La capture d’écran suivante confirme que l’ordinateur *mycon* envoie le plus de données pour la solution de gestion des journaux.<br><br> ![volume de données pour une solution](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

Si nécessaire, effectuez des analyses supplémentaires pour identifier des volumes importants au sein d’une solution ou d’un type de données. Les exemples de requêtes incluent :

+ une solution de **sécurité**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ une solution de **gestion des journaux**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ le type de données **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ le type de données **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ le type de données **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ le type de données **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

Pour réduire le volume de journaux collectés, procédez comme suit :

| Source du volume de données important | Comment réduire le volume de données |
| -------------------------- | ------------------------- |
| Événements de sécurité            | Sélectionnez [les événements de sécurité courants ou minimaux](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Modifier la stratégie d’audit de sécurité pour collecter les événements nécessaires uniquement. Plus particulièrement, examinez la nécessité de collecter des événements pour : <br> - [plateforme de filtrage de l’audit](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [registre de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [système de fichiers de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [objet de noyau d’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [manipulation du descripteur de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - stockage amovible de l’audit |
| Compteurs de performances       | Modifiez la [configuration du compteur de performances](log-analytics-data-sources-performance-counters.md) de façon à : <br> - Réduire la fréquence de collecte <br> - Réduire le nombre de compteurs de performance |
| Journaux d’événements                 | Modifiez la [configuration du journal d’événements](log-analytics-data-sources-windows-events.md) de façon à : <br> - Réduire le nombre de journaux des événements collectés <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations*. |
| syslog                     | Modifiez la [configuration du syslog](log-analytics-data-sources-syslog.md) de façon à : <br> - Réduire le nombre d’installations collectées <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations* et *Débogage*. |
| AzureDiagnostics           | Modifiez la collection de journaux de ressources pour : <br> - Réduire le nombre de journaux d’envoi de ressources à Log Analytics <br> - Collecter uniquement les journaux nécessaires |
| Données de solution d’ordinateurs n’ayant pas besoin de la solution | Utilisez le [ciblage de solution](../monitoring/monitoring-solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Vérifier s’il y a plus de nœuds que prévu
Si vous utilisez le niveau tarifaire *Par nœud (Log Analytics)*, vous êtes facturé en fonction du nombre de nœuds et de solutions que vous utilisez. Vous pouvez voir le nombre de nœuds utilisés par offre dans la section *Offres* du tableau de bord d’utilisation.<br><br> ![tableau de bord utilisation](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Cliquez sur **Afficher tout...**  pour consulter la liste complète des ordinateurs envoyant des données pour l’offre sélectionnée.

Utilisez le [ciblage de solution](../monitoring/monitoring-solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement.

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Recherche de données à l’aide de recherches de journal](log-analytics-queries.md) pour apprendre à utiliser le langage de recherche. Vous pouvez utiliser des requêtes de recherche pour effectuer des analyses supplémentaires sur les données d’utilisation.
* Utilisez les étapes décrites dans [Création d’une alerte de journal](../monitoring-and-diagnostics/alert-metric.md) pour être averti lorsqu’un critère de recherche est rempli.
* Utilisez le [ciblage de solution](../monitoring/monitoring-solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement.
* Pour configurer une règle efficace de collecte d’événements de sécurité, passez en revue [Stratégie de filtrage de Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Modifier la [configuration du compteur de performances](log-analytics-data-sources-performance-counters.md).
* Pour modifier vos paramètres de collecte d’événements, consultez [Configuration du journal des événements](log-analytics-data-sources-windows-events.md).
* Pour modifier vos paramètres de collecte de messages syslog, consultez [Configuration syslog](log-analytics-data-sources-syslog.md).
