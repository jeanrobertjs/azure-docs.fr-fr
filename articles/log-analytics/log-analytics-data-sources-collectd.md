---
title: Collecter les données à partir de CollectD dans OMS Log Analytics | Microsoft Docs
description: CollectD est un démon Linux open source qui collecte périodiquement les données des applications et des informations de niveau système.  Cet article fournit des informations sur la collecte de données à partir de CollectD dans Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a1f28103f8faabae166f09185db3f3e1fee7a5ab
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404594"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Collecter des données à partir de CollectD sur les agents Linux dans Log Analytics
[CollectD](https://collectd.org/) est un démon Linux open source qui collecte périodiquement des mesures de performances à partir d’applications et d’informations de niveau système. Les applications peuvent être, par exemple, la machine virtuelle Java (JVM), le serveur MySQL et Nginx. Cet article fournit des informations sur la collecte des données de performances à partir de CollectD dans Log Analytics.

Vous trouverez la liste complète des plug-ins disponibles dans le [tableau de plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Vue d’ensemble de CollectD](media/log-analytics-data-sources-collectd/overview.png)

La configuration CollectD suivante est incluse dans l’agent Log Analytics pour Linux pour acheminer des données CollectD vers l’agent Log Analytics pour Linux.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

En outre, si vous utilisez une version de CollectD antérieure à 5.5, utilisez plutôt la configuration suivante.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

La configuration CollectD utilise le plug-in par défaut `write_http` pour envoyer des données de mesure de performances sur le port 26000 à l’agent Log Analytics pour Linux. 

> [!NOTE]
> Ce port peut être configuré sur un port personnalisé, si nécessaire.

L’agent Log Analytics pour Linux écoute également les mesures CollectD sur le port 26000 et les convertit ensuite en mesures de schéma Log Analytics. La configuration de l’agent Log Analytics pour Linux est la suivante : `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Versions prises en charge
- Log Analytics prend actuellement en charge la version 4.8 et les versions ultérieures de CollectD.
- L’agent Log Analytics pour Linux v1.1.0-217 ou version ultérieure est requis pour la collecte des mesures CollectD.


## <a name="configuration"></a>Configuration
Les étapes de base pour configurer la collecte des données CollectD dans Log Analytics sont les suivantes.

1. Configurez CollectD pour qu’il envoie des données à l’agent Log Analytics pour Linux à l’aide du plug-in write_http.  
2. Configurez l’agent Log Analytics pour Linux afin qu’il écoute les données CollectD sur le port approprié.
3. Redémarrez CollectD et l’agent Log Analytics pour Linux.

### <a name="configure-collectd-to-forward-data"></a>Configuration de CollectD pour le transfert des données 

1. Pour acheminer les données CollectD vers l’agent Log Analytics pour Linux, ajoutez `oms.conf` au répertoire de configuration de CollectD. La destination de ce fichier dépend de la distribution Linux de votre machine.

    Si votre répertoire de configuration CollectD se trouve dans /etc/collectd.d/ :

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Si votre répertoire de configuration CollectD se trouve dans /etc/collectd/collectd.conf.d/ :

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Pour les versions de CollectD antérieures à 5.5, vous devrez modifier les balises dans `oms.conf`, comme indiqué ci-dessus.
    >

2. Copiez collectd.conf vers le répertoire de configuration omsagent de l’espace de travail souhaité.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Redémarrez CollectD et l’agent Log Analytics pour Linux à l’aide des commandes suivantes.

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Mesures CollectD pour la conversion de schéma Log Analytics
Pour conserver un modèle cohérent entre les mesures d’infrastructure déjà collectées par l’agent Log Analytics pour Linux et les nouvelles mesures collectées par CollectD, le mappage de schéma suivant est utilisé :

| Champ Mesure CollectD | Champ Log Analytics |
|:--|:--|
| host | Ordinateur |
| plugin | Aucun |
| plugin_instance | Nom de l’instance<br>If **plugin_instance** is *null* then InstanceName="*_Total*" |
| Type | ObjectName |
| type_instance | CounterName<br>If **type_instance** is *null* then CounterName=**blank** |
| dsnames[] | CounterName |
| dstypes | Aucun |
| values[] | CounterValue |

## <a name="next-steps"></a>Étapes suivantes
* Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions. 
* Utilisez les [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les données des enregistrements syslog dans des champs individuels.

