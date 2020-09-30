---
title: 'Azure ExpressRoute: configurare ExpressRoute Direct'
description: Informazioni su come usare Azure PowerShell per configurare Azure ExpressRoute Direct per connettersi direttamente alla rete globale di Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: a450c4057b4639206fd1db4b7f44d27c69441f7f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569853"
---
# <a name="how-to-configure-expressroute-direct"></a>Come configurare ExpressRoute Direct

ExpressRoute Direct offre la possibilità di connettersi direttamente alla rete globale di Microsoft attraverso le località di peering distribuite in modo strategico in tutto il mondo. Per altre informazioni, vedere [Informazioni su ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Creare la risorsa

1. Accedere ad Azure e selezionare la sottoscrizione. La risorsa di ExpressRoute Direct e circuiti di ExpressRoute devono essere nella stessa sottoscrizione.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Registrare nuovamente la sottoscrizione a Microsoft. Network per accedere alle API expressrouteportslocation e expressrouteport.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Elencare tutte le località in cui è supportato ExpressRoute Direct.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Output di esempio**
  
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
4. Determinare se una località indicato sopra ha larghezza di banda disponibile

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Output di esempio**

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
5. Creare una risorsa di ExpressRoute Direct in base alla località selezionata in precedenza

   ExpressRoute Direct supporta l'incapsulamento QinQ e Dot1Q. Se si seleziona QinQ, a ogni circuito di ExpressRoute viene assegnato dinamicamente un S-Tag e il circuito è univoco per tutta la risorsa di ExpressRoute Direct. Ogni C-Tag nel circuito deve essere univoco nel circuito, ma non in tutto ExpressRoute Direct.  

   Se si seleziona l'incapsulamento Dot1Q, è necessario gestire l'univocità del C-Tag (VLAN) nell'intera risorsa di ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct può essere un solo tipo di incapsulamento. L'incapsulamento non può essere modificato dopo la creazione di ExpressRoute Direct.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > L'attributo dell'incapsulamento può anche essere impostato su Dot1Q. 
   >

   **Output di esempio**:

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

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Genera la lettera di autorizzazione (LOA)

Fare riferimento alla risorsa ExpressRoute Direct creata di recente, immettere un nome cliente per scrivere la LOA in e (facoltativamente) definire un percorso file per archiviare il documento. Se non viene fatto riferimento a un percorso di file, il documento verrà scaricato nella directory corrente.

  ```powershell 
   New-AzExpressRoutePortLOA -ExpressRoutePort $ERDirect -CustomerName TestCustomerName -Destination "C:\Users\SampleUser\Downloads" 
   ```
 **Output di esempio**

   ```powershell
   Written Letter of Authorization To: C:\Users\SampleUser\Downloads\LOA.pdf
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>Modificare lo stato di amministratore di collegamenti
   
Utilizzare questo processo per condurre un test di livello 1, verificando che ogni Cross Connection sia correttamente trasferito a ciascun router per le porte primaria e secondaria.
1. Ottenere i dettagli di ExpressRoute Direct.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Impostare il collegamento su abilitato. Ripetere questo passaggio per impostare ogni collegamento su abilitato.

   Links[0] è la porta primaria e Links[1] è la porta secondaria.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Output di esempio**:

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

   Utilizzare la stessa procedura con `AdminState = "Disabled"` per rifiutare le porte.

## <a name="create-a-circuit"></a><a name="circuit"></a>Creare un circuito

Per impostazione predefinita, è possibile creare dieci circuiti nella sottoscrizione in cui si trova la risorsa di ExpressRoute Direct. Questo limite può essere aumentato dal supporto tecnico. Si è responsabili del monitoraggio della larghezza di banda con provisioning e di quella utilizzata. La larghezza di banda con provisioning è la somma della larghezza di banda di tutti i circuiti della risorsa ExpressRoute Direct e la larghezza di banda utilizzata è l'utilizzo fisico delle interfacce fisiche sottostanti.

Sono disponibili altre larghezze di banda del circuito che possono essere usate in ExpressRoute Direct per supportare solo gli scenari descritti in precedenza. Queste larghezze di banda sono 40 Gbps e 100 Gbps.

**SkuTier** può essere locale, standard o Premium.

**SkuFamily** può essere MeteredData. Illimitata non è supportata in ExpressRoute Direct.

Creare un circuito nella risorsa di ExpressRoute Direct.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Altre larghezze di banda sono 5.0, 10.0 e 40.0

  **Output di esempio**:

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

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute Direct, vedere la [Panoramica](expressroute-erdirect-about.md).
