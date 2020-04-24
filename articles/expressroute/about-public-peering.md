---
title: Creare e gestire il peering pubblico di Azure ExpressRoute
description: Informazioni e gestione del peering pubblico di Azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481134"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Creare e gestire il peering pubblico di ExpressRoute

> [!div class="op_single_selector"]
> * [Articolo-peering pubblico](about-public-peering.md)
> * [Video - Peering pubblico](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Articolo-peering Microsoft](expressroute-circuit-peerings.md#microsoftpeering)
>

Questo articolo consente di creare e gestire la configurazione del routing del peering pubblico per un circuito ExpressRoute. È anche possibile controllare lo stato, aggiornare o eliminare i peering e deprovisioning. Questo articolo si applica ai circuiti Gestione risorse creati prima che il peering pubblico fosse deprecato. Se è presente un circuito precedentemente esistente (creato prima del peering pubblico deprecato), è possibile gestire/configurare il peering pubblico usando [Azure PowerShell](#powershell), l'interfaccia della riga di comando di [Azure](#cli)e la [portale di Azure](#portal).

>[!NOTE]
>Il peering pubblico è deprecato. Non è possibile creare il peering pubblico nei nuovi circuiti ExpressRoute. Se si dispone di un nuovo circuito ExpressRoute, usare invece il [peering Microsoft](expressroute-circuit-peerings.md#microsoftpeering) per i servizi di Azure.
>

## <a name="connectivity"></a>Connettività

La connettività viene sempre attivata dalla rete WAN verso i servizi di Microsoft Azure. I servizi di Microsoft Azure non potranno attivare connessioni alla rete dell'utente tramite questo dominio di routing. Se il circuito ExpressRoute è abilitato per il peering pubblico di Azure, è possibile accedere agli [intervalli di indirizzi IP pubblici usati in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) tramite il circuito.

Una volta abilitato il peering pubblico, è possibile connettersi alla maggior parte dei servizi di Azure. Non è consentito scegliere in modo selettivo i servizi per cui vengono annunciate route.

* Servizi quali archiviazione di Azure, database SQL e siti Web sono offerti su indirizzi IP pubblici.
* Tramite il dominio di routing di peering pubblico, è possibile connettersi privatamente ai servizi ospitati su indirizzi IP pubblici, inclusi i VIP dei servizi cloud.
* È possibile connettere il dominio di peering pubblico alla rete perimetrale e connettersi a tutti i servizi di Azure sui rispettivi indirizzi IP pubblici dalla rete WAN senza doversi connettere a Internet.

## <a name="services"></a><a name="services"></a>Servizi

Questa sezione illustra i servizi disponibili tramite peering pubblico. Poiché il peering pubblico è deprecato, non esiste alcun piano per aggiungere servizi nuovi o aggiuntivi al peering pubblico. Se si usa il peering pubblico e il servizio che si vuole usare è supportato solo dal peering Microsoft, è necessario passare al peering Microsoft. Per un elenco dei servizi supportati, vedere [peering Microsoft](expressroute-faqs.md#microsoft-peering) .

**Supportato**

* Power BI
* È supportata la maggior parte dei servizi di Azure. Controllare direttamente con il servizio che si vuole usare per verificare il supporto.

**Non supportato:**
  * RETE CDN
  * Frontdoor di Azure
  * Server di autenticazione a più fattori (legacy)
  * Gestione traffico

Per convalidare la disponibilità per un servizio specifico, è possibile controllare la documentazione relativa a tale servizio per verificare se è stato pubblicato un intervallo riservato per il servizio. Quindi, è possibile cercare gli intervalli IP del servizio di destinazione e confrontarli con gli intervalli elencati negli [intervalli IP di Azure e nei tag del servizio-file XML del cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519). In alternativa, è possibile aprire un ticket di supporto per il servizio in questione per chiarimenti.

## <a name="peering-comparison"></a><a name="compare"></a>Confronto tra i peering

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Il peering pubblico di Azure ha 1 indirizzo IP NAT associato a ogni sessione BGP. Per più di 2 indirizzi IP NAT, passare al peering Microsoft. Il peering Microsoft consente di configurare le proprie allocazioni NAT, nonché di usare i filtri di route per gli annunci con prefisso selettivo. Per altre informazioni, vedere [passare al peering Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Filtri di Route personalizzati

È possibile definire filtri di route personalizzati nella propria rete per usare solo le route necessarie. Per informazioni sulla configurazione del routing, vedere la pagina relativa al [routing](expressroute-routing.md) .

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Passaggi Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Poiché il peering pubblico è deprecato, non è possibile configurare il peering pubblico in un nuovo circuito ExpressRoute.

1. Verificare che sia disponibile un circuito ExpressRoute di cui è stato effettuato il provisioning e che sia abilitato. Usare l'esempio seguente:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   La risposta restituita è simile all'esempio seguente:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Configurare il peering pubblico di Azure per il circuito. Prima di continuare, verificare di avere a disposizione le informazioni seguenti.

   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Facoltativo:
   * Un hash MD5, se si sceglie di usarne uno.

   Per configurare il peering pubblico di Azure per il circuito, applicare l'esempio seguente:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se si sceglie di usare un hash MD5, usare l'esempio seguente:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure

Per ottenere i dettagli di configurazione, usare il cmdlet seguente:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Per aggiornare la configurazione del peering pubblico di Azure

Per aggiornare qualsiasi parte della configurazione, usare l'esempio seguente. In questo esempio il valore dell'ID VLAN del circuito viene aggiornato da 200 a 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Per eliminare un peering pubblico di Azure

Per rimuovere la configurazione di peering, eseguire l'esempio seguente:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Procedura CLI di Azure


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Controllare che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia abilitato. Usare l'esempio seguente:

   ```azurecli-interactive
   az network express-route list
   ```

   La risposta restituita è simile all'esempio seguente:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Configurare il peering pubblico di Azure per il circuito. Prima di continuare, verificare di avere a disposizione le informazioni seguenti.

   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * **Facoltativo:** un hash MD5, se si sceglie di usarne uno.

   Per configurare il peering pubblico di Azure per il circuito, eseguire l'esempio seguente:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Se si sceglie di usare un hash MD5, usare l'esempio seguente:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure

Per ottenere i dettagli di configurazione, usare l'esempio seguente:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

L'output è simile all'esempio seguente:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Per aggiornare la configurazione del peering pubblico di Azure

Per aggiornare qualsiasi parte della configurazione, usare l'esempio seguente. In questo esempio il valore dell'ID VLAN del circuito viene aggiornato da 200 a 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Per eliminare un peering pubblico di Azure

Per rimuovere la configurazione di peering, eseguire l'esempio seguente:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Procedure del portale di Azure

Per configurare il peering, usare i passaggi di PowerShell o dell'interfaccia della riga di comando contenuti in questo articolo. Per gestire un peering, è possibile usare le sezioni riportate di seguito. Per riferimento, questi passaggi sono simili alla gestione di un [peering Microsoft nel portale](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure

Visualizzare le proprietà del peering pubblico di Azure selezionando il peering nel portale.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Per aggiornare la configurazione del peering pubblico di Azure

Selezionare la riga per il peering, quindi modificare le proprietà del peering.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Per eliminare un peering pubblico di Azure

Rimuovere la configurazione del peering selezionando l'icona Elimina.

## <a name="next-steps"></a>Passaggi successivi

Passaggio successivo: [collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Per ulteriori informazioni sui flussi di lavoro ExpressRoute, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)
* Per ulteriori informazioni sull’uso delle reti virtuali, vedere [Panoramica sulla rete virtuale](../virtual-network/virtual-networks-overview.md).