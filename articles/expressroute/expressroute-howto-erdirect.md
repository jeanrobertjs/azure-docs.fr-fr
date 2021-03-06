---
title: Comment configurer Azure ExpressRoute Direct | Microsoft Docs
description: Cette page vous permet de configurer ExpressRoute Direct (préversion)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: e0009791263c45e0172abcb4836aaadde26f3ace
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887187"
---
# <a name="how-to-configure-expressroute-direct-preview"></a>Comment configurer ExpressRoute Direct (préversion)

ExpressRoute Direct vous offre la possibilité de vous connecter directement au réseau mondial Microsoft à partir d’emplacements d’homologation qui sont distribués stratégiquement dans le monde entier. Pour plus d’informations, consultez [À propos d’ExpressRoute Direct Connect](expressroute-erdirect-about.md).

> [!IMPORTANT]
> ExpressRoute Direct est actuellement disponible en préversion.
>
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="resources"></a>1. Créer la ressource

1. Connectez-vous à Azure et sélectionnez l’abonnement. La ressource ExpressRoute Direct et les circuits ExpressRoute doivent se trouver dans le même abonnement.

  ```powershell
  Connect-AzureRMAccount 

  Select-AzureRMSubscription -Subscription “<SubscriptionID or SubscriptionName>”
  ```
2. Répertoriez tous les emplacements où ExpressRoute Direct est pris en charge.
  
  ```powershell
  Get-AzureRmExpressRoutePortsLocation
  ```

  **Exemple de sortie**
  
  ```powershell
  Name                : Equinix-Ashburn-DC2
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
  ProvisioningState   : Succeeded
  Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-Dallas-DA3
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
  ProvisioningState   : Succeeded
  Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : []
  ```
3. Déterminer si un emplacement répertorié ci-dessus a de la bande passante disponible

  ```powershell
  Get-AzureRMExpressRoutePortsLocations -Name "Equinix-San-Jose-SV1"
  ```

  **Exemple de sortie**

  ```powershell
  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
  ```
4. Créer une ressource ExpressRoute Direct en fonction de l’emplacement choisi ci-dessus

  ExpressRoute Direct prend en charge l’encapsulation QinQ et Dot1Q. Si l’encapsulation QinQ est sélectionnée, chaque circuit ExpressRoute se voit affecter dynamiquement un S-Tag et est unique sur l’ensemble de la ressource ExpressRoute Direct. Chaque C-Tag sur le circuit doit être unique sur le circuit, mais pas sur ExpressRoute Direct.  

  Si l’encapsulation Dot1Q est sélectionnée, vous devez gérer l’unicité de C-Tag (VLAN) sur l’ensemble de la ressource ExpressRoute Direct.  

  > [!IMPORTANT]
  > ExpressRoute Direct ne peut être que d’un seul type d’encapsulation. L’encapsulation ne peut pas être modifiée après la création d’ExpressRoute Direct.
  > 
 
  ```powershell 
  $ERDirect = New-AzureRMExpressRoutePort -Name $Name -ResourceGroupName -$RGName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
  ```

  > [!NOTE]
  > L’attribut d’encapsulation peut également être défini sur Dot1Q. 
  >

  **Exemple de sortie :**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
  Circuits                   : []
  ```

## <a name="state"></a>2. Changer l’état Administrateur de liens

Ce processus doit être utilisé pour effectuer un test de la couche 1, en s’assurant que chaque connexion croisée est correctement reliée dans chaque routeur principal et secondaire.

1. Obtenez des détails sur ExpressRoute Direct.

  ```powershell
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  ```
2. Définissez le lien sur Activé. Répétez cette étape pour chaque lien à activer.

  Links[0] correspondent au port principal et Links[1] au port secondaire.

  ```powershell
  $ERDirect.Links[0].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  $ERDirect.Links[1].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  ```
  **Exemple de sortie :**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
  Circuits                   : []
  ```

Utilisez la même procédure avec `AdminState = “Disabled”` pour désactiver les ports.

## <a name="circuit"></a>3. Créer un circuit

Par défaut, vous pouvez créer 10 circuits dans l’abonnement où se trouve la ressource ExpressRoute Direct. Ce nombre peut être augmenté par le support. Vous êtes responsable du suivi de la bande passante approvisionnée et utilisée. La bande passante approvisionnée équivaut à la somme de la bande passante de tous les circuits sur la ressource ExpressRoute Direct et la bande passante utilisée équivaut à l’utilisation physique des interfaces physiques sous-jacentes.

D’autres bandes passantes de circuit peuvent être utilisées sur ExpressRoute Direct, uniquement pour prendre en charge les scénarios décrits ci-dessus. Les voici : 40 Gbps et 100 Gbps.

Des circuits Standard ou Premium peuvent être créés. Les circuits Standard sont inclus dans le coût, tandis que les circuits Premium ont un coût basé sur la bande passante sélectionnée. Les circuits peuvent uniquement être créés comme limités, car illimité n’est pas pris en charge sur ExpressRoute Direct.

Créez un circuit sur la ressource ExpressRoute Direct.

```powershell
New-AzureRmExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 1.0 | 2.0 | 5.0 | 10.0 | 40.0 | 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
```

D’autres bandes passantes incluent les suivantes : 1.0, 2.0, 5.0, 10.0 et 40.0

**Exemple de sortie :**

```powershell
Name                             : ExpressRoute-Direct-ckt
ResourceGroupName                : Contoso-Direct-rg
Location                         : westcentralus
Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
Etag                             : W/"<etagnumber>"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : null
ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
BandwidthInGbps                  : 10
Stag                             : 2
ServiceKey                       : <number>
Peerings                         : []
Authorizations                   : []
AllowClassicOperations           : False
GatewayManagerEtag     
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur ExpressRoute, consultez la [Vue d’ensemble](expressroute-erdirect-about.md).
