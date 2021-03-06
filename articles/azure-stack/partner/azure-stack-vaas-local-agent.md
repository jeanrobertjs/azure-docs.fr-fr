---
title: Déployer l’agent local | Microsoft Docs
description: Déployez l’agent local pour le service Validation en tant que service pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 6a7d1f886bd3a8457b6cad2e9fea11820805e7c9
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651942"
---
# <a name="deploy-the-local-agent"></a>Déployer l’agent local

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Découvrez comment utiliser l’agent local Validation en tant que service (VaaS) pour vérifier votre matériel. Vous devez déployer l’agent local sur la solution Azure Stack en cours de validation avant d’exécuter les tests de validation.

> [!Note]  
> Vous devez vous assurer que la machine sur laquelle s’exécute l’agent local ne perd pas l’accès sortant à Internet. Cette machine doit être accessible uniquement aux utilisateurs que vous avez autorisé à utiliser le service VaaS pour le compte de votre abonné.

Pour déployer l’agent local :

1. Installez l’agent local.
2. Procédez à des contrôles d’intégrité
3. Exécutez l’agent local.

## <a name="download-and-start-the-local-agent"></a>Télécharger et démarrer l’agent local

Téléchargez l’agent sur une machine présentant la configuration requise dans votre centre de données qui ne fait pas partie intégrante du système Azure Stack, mais qui a accès à tous les points de terminaison Azure Stack.

### <a name="machine-prerequisites"></a>Configuration requise pour les machines

Vérifiez que votre machine répond aux critères suivants :

- Accès à tous les points de terminaison Azure Stack
- .NET 4.6 et PowerShell 5.0 installés
- 8 Go de RAM au minimum
- 8 processeurs Core au minimum
- Espace disque de 200 Go au minimum
- Connectivité réseau à Internet stable

Azure Stack est le système testé. La machine ne doit pas faire partie intégrante d’Azure Stack ni être hébergée dans le cloud Azure Stack.

### <a name="download-and-install-the-agent"></a>Téléchargement et installation de l’agent

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation de privilèges sur la machine que vous allez utiliser pour exécuter les tests.
2. Exécutez la commande ci-après pour télécharger l’agent local :

    ```PowerShell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Exécutez la commande ci-après pour installer les dépendances de l’agent local :

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Paramètres**

    | Paramètre | Description |
    | --- | --- |
    | aadServiceAdminUser | Utilisateur administrateur général pour votre locataire Azure AD. Par exemple, il peut s’agir de vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Mot de passe de l’utilisateur administrateur général. |
    | AadTenantId | ID de locataire Azure AD pour le compte Azure inscrit auprès du service Validation en tant que service. |
    | ExternalFqdn | Vous pouvez obtenir le nom de domaine complet à partir du fichier de configuration. Pour obtenir les instructions correspondantes, consultez l’article [Paramètres de flux de travail communs dans la validation en tant que service Azure Stack](azure-stack-vaas-parameters.md). |
    | Région | Région de votre locataire Azure AD. |

La commande télécharge une image (disque dur virtuel de système d’exploitation) du référentiel d’images public et la copie à partir d’un service Stockage Blob Azure dans votre stockage Azure Stack.

![Télécharger les composants requis](media/installingprereqs.png)

> [!Note]
> Si vous rencontrez un problème de lenteur du réseau lorsque vous téléchargez ces images, téléchargez ces dernières séparément dans un partage local et spécifiez le paramètre **-LocalPackagePath** *FileShareOrLocalPath*. Vous trouverez des conseils supplémentaires sur le téléchargement du référentiel d’images public dans la section [Gérer les problèmes de lenteur de la connexion réseau](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) de l’article [Résoudre les problèmes de validation en tant que service](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Vérifications avant de commencer les tests

Les tests exécutent des actions à distance. La machine qui exécute les tests doit avoir accès aux points de terminaison Azure Stack pour le bon déroulement des tests. Si vous utilisez l’agent VaaS local, utilisez la machine sur laquelle l’agent s’exécutera. Vous pouvez vérifier que votre machine a accès aux points de terminaison Azure Stack en effectuant les vérifications suivantes :

1. Vérifiez que l’URI de base est accessible. Ouvrez une invite de commande ou interpréteur de commandes bash, et exécutez la commande suivante en remplaçant `<EXTERNALFQDN>` avec le nom de domaine complet externe de votre environnement :

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Ouvrez un navigateur web et accédez à `https://adminportal.<EXTERNALFQDN>` afin de vérifier que le portail MAS est accessible.

3. Connectez-vous à l’aide des valeurs du mot de passe et du nom d’administrateur de service Azure AD fournies lors de la création de la passe de test.

4. Vérifiez l’état d’intégrité du système en exécutant la cmdlet PowerShell **Test-AzureStack** comme décrit dans l’article [Exécuter un test de validation pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test). Corrigez les problèmes signalés par les avertissements et les erreurs avant de lancer le moindre test.

## <a name="run-the-agent"></a>Exécuter l’agent

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation des privilèges.

2. Exécutez la commande suivante :

    ````PowerShell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Paramètres**  
    | Paramètre | Description |
    | --- | --- |
    | VaaSUserId | Identificateur d’utilisateur permettant de se connecter au portail VaaS (par exemple, UserName@Contoso.com). |
    | VaaSTenantId | ID de locataire Azure AD pour le compte Azure inscrit auprès du service Validation en tant que service. |

    > [!Note]  
    > Lorsque vous exécutez l’agent, le répertoire de travail actuel doit être l’emplacement de l’exécutable de l’hôte du moteur de tâches, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Si aucune erreur ne vous est signalée, l’agent local a réussi. L’exemple de texte ci-après s’affiche dans la fenêtre de console.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Agent démarré](media/startedagent.png)

Un agent est identifié de manière unique par son nom. Par défaut, il utilise le nom de domaine complet (FQDN) de la machine sur laquelle il a été démarré. Vous devez réduire la fenêtre pour éviter les sélections accidentelles dans cette dernière, car le basculement vers cette fenêtre interrompt toutes les autres actions.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de validation en tant que service](azure-stack-vaas-troubleshoot.md)
- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Concepts clés à propos de la validation en tant que service)
- [Quickstart: Use the Validation as a Service portal to schedule your first test](azure-stack-vaas-schedule-test-pass.md) (Démarrage rapide : utiliser le portail de validation en tant que service pour planifier votre premier test)