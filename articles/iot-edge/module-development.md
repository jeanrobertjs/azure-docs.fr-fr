---
title: Développer des modules pour Azure IoT Edge | Microsoft Docs
description: Découvrez comment créer des modules personnalisés pour Azure IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d4253942ea5cd998bfd3806978e108413949f886
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741426"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Comprendre les conditions requises et les outils de développement de modules IoT Edge

Cet article explique quelles fonctionnalités sont disponibles lors de l’écriture d’applications qui s’exécutent en tant que module IoT Edge, et comment tirer parti de ces fonctionnalités.

## <a name="iot-edge-runtime-environment"></a>Environnement d’exécution IoT Edge
Le runtime IoT Edge fournit l’infrastructure nécessaire pour intégrer les fonctionnalités de plusieurs modules IoT Edge et pour les déployer sur des appareils IoT Edge. À un niveau global, tout programme peut être empaqueté en tant que module IoT Edge. Toutefois, pour tirer pleinement parti des fonctionnalités de communication et de gestion d’IoT Edge, un programme qui s’exécute dans un module peut se connecter au hub IoT Edge local, intégré dans le runtime IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Utilisation du hub IoT Edge
Le hub IoT Edge fournit deux fonctionnalités principales : proxy d’IoT Hub et communications locales.

### <a name="iot-hub-primitives"></a>Primitives IoT Hub
IoT Hub considère une instance de module comme un appareil, dans le sens où :

* Elle a un jumeau de module, qui est distinct et isolé du [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md) et des autres jumeaux de module de cet appareil.
* Elle peut envoyer des [messages appareil-à-cloud](../iot-hub/iot-hub-devguide-messaging.md).
* Elle peut recevoir des [méthodes directes](../iot-hub/iot-hub-devguide-direct-methods.md) ciblant spécifiquement son identité.

Actuellement, un module ne peut pas recevoir de messages cloud-à-appareil, ni utiliser la fonctionnalité de chargement de fichier.

Quand vous écrivez un module, vous pouvez utiliser [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) pour vous connecter au hub IoT Edge et utiliser la fonctionnalité ci-dessus comme vous le feriez lors de l’utilisation d’IoT Hub avec une application d’appareil (la seule différence étant que, à partir du back-end de votre application, vous devez faire référence à l’identité du module plutôt qu’à l’identité de l’appareil).

Pour obtenir un exemple d’application de module qui envoie des messages appareil-à-cloud et utilise le jumeau de module, consultez [Développer et déployer un module IoT Edge sur un appareil simulé](tutorial-csharp-module.md).

### <a name="device-to-cloud-messages"></a>Messages appareil-à-cloud
Pour permettre le traitement complexe des messages appareil-à-cloud, le hub IoT Edge fournit un routage déclaratif des messages entre les modules, et entre les modules et IoT Hub. Ce routage déclaratif permet aux modules d’intercepter et de traiter les messages envoyés par d’autres modules, et de les propager dans des pipelines complexes. L’article [Composition des modules](module-composition.md) explique comment composer des modules dans des pipelines complexes à l’aide d’itinéraires.

Un module IoT Edge, contrairement à une application d’appareil IoT Hub normale, peut recevoir des messages appareil-à-cloud qui sont traités en proxy par son hub IoT Edge local, afin de les traiter.

Le hub IoT Edge propage les messages vers votre module en fonction des routes déclaratives décrites dans l’article [Composition des modules](module-composition.md). Quand vous développez un module IoT Edge, vous pouvez recevoir ces messages en définissant des gestionnaires de messages.

Afin de simplifier la création d’itinéraires, IoT Edge ajoute le concept de points de terminaison *d’entrée* et *de sortie* de module. Un module peut recevoir tous les messages appareil-à-cloud qui lui sont envoyés sans spécifier d’entrée, et peut envoyer des messages appareil-à-cloud sans spécifier de sortie.
L’utilisation d’entrées et de sorties explicites rend toutefois les règles de routage plus simples à comprendre. Pour plus d’informations sur les règles de routage et les points de terminaison d’entrée et de sortie pour les modules, consultez [Composition des modules](module-composition.md).

Pour finir, les messages appareil-à-cloud gérés par le hub Edge sont marqués avec les propriétés système suivantes :

| Propriété | Description |
| -------- | ----------- |
| $connectionDeviceId | ID d’appareil du client ayant envoyé le message. |
| $connectionModuleId | ID de module du module ayant envoyé le message. |
| $inputName | Entrée ayant reçu ce message. Peut être vide. |
| $outputName | Sortie utilisée pour envoyer le message. Peut être vide. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connexion au hub IoT Edge à partir d’un module
La connexion au hub IoT Edge local à partir d’un module implique deux étapes : utiliser la chaîne de connexion fournie par le runtime IoT Edge au démarrage de votre module, et vérifier que votre application accepte le certificat présenté par le hub IoT Edge sur cet appareil.

La chaîne de connexion à utiliser est injectée par le runtime IoT Edge dans la variable d’environnement `EdgeHubConnectionString`. Du coup, n’importe quel programme souhaitant l’utiliser peut y accéder.

De la même façon, le certificat à utiliser pour valider la connexion au hub IoT Edge est injecté par le runtime IoT Edge dans un fichier dont le chemin est disponible dans la variable d’environnement `EdgeModuleCACertificateFile`.

## <a name="next-steps"></a>Étapes suivantes

Après avoir développé un module, découvrez comment [déployer et superviser des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).

