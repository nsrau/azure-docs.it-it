---
title: 'Azure ExpressRoute: configurare il peering: interfaccia della riga di comando'
description: Questo articolo descrive come creare ed eseguire il provisioning del peering privato, pubblico e Microsoft per un circuito ExpressRoute. Questo articolo mostra anche come controllare lo stato e aggiornare o eliminare i peering per un circuito.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/24/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: c20b0741f12ee2cab40d8f5b53c51ad537801bf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393174"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Creare e modificare il peering per un circuito ExpressRoute usando l'interfaccia della riga di comando

Questo articolo consente di creare e gestire la configurazione/peering del routing per un circuito ExpressRoute nel modello di distribuzione Resource Manager tramite l'interfaccia della riga di comando. La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute. Se si vuole usare un metodo diverso per eseguire operazioni nel circuito, selezionare l'articolo appropriato nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-routing-cli.md)
> * [Peering pubblico](about-public-peering.md)
> * [Video - Peering privato](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Peering Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-routing-classic.md) (PowerShell (classico))
> 

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

* Prima di iniziare, installare la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Prima di procedere con la configurazione, assicurarsi di avere letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti di routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](howto-circuit-cli.md) e fare in modo che il circuito sia abilitato dal provider di connettività prima di procedere. Per poter eseguire i comandi descritti in questo articolo deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.

Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (in genere una VPN IP, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.

È possibile configurare il peering privato e il peering Microsoft per un circuito ExpressRoute (il peering pubblico di Azure è deprecato per i nuovi circuiti). I peering possono essere configurati in qualsiasi ordine. assicurandosi, tuttavia, di completare la configurazione di un peering per volta. Per altre informazioni sul routing dei domini e il peering , vedere [Domini di routing ExpressRoute](expressroute-circuit-peerings.md). Per informazioni sul peering pubblico, vedere il [peering pubblico ExpressRoute](about-public-peering.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Peering Microsoft

Questa sezione consente di creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute.

> [!IMPORTANT]
> Per i circuiti ExpressRoute configurati prima del 1 agosto 2017 tutti i prefissi di servizio saranno annunciati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito. Per altre informazioni, vedere [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurare un filtro di route per il peering Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft

1. Installare la versione più recente dell'interfaccia della riga di comando di Azure. Usare la versione più recente dell'interfaccia della riga di comando di Azure. Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

   ```azurecli
   az login
   ```

   Selezionare la sottoscrizione per la quale si vuole creare un circuito ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Creare un circuito ExpressRoute. Seguire le istruzioni per creare un [circuito ExpressRoute](howto-circuit-cli.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering Microsoft per l'utente. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, proseguire la configurazione seguendo questa procedura. 

3. Controllare che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia abilitato. Usare l'esempio seguente:

   ```azurecli
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

4. Configurare il peering Microsoft per il circuito. Prima di procedere, verificare quanto segue:

   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Advertised prefixes: è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Se si intende inviare un set di prefissi, è possibile creare un elenco delimitato da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
   * **Facoltativo -** ASN cliente: se si annunciano prefissi non registrati nel numero AS di peering, è possibile specificare il numero AS in cui sono registrati.
   * Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
   * **Facoltativo:** un hash MD5, se si sceglie di usarne uno.

   Per configurare il peering Microsoft per il circuito, eseguire l'esempio seguente:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Per visualizzare i dettagli del peering Microsoft

Per ottenere i dettagli di configurazione, usare l'esempio seguente:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft verifica se all'utente sono assegnati "Prefissi pubblici annunciati" e "ASN peer" (o "ASN cliente") nel registro di sistema di routing Internet. Se si ricevono prefissi pubblici da un'altra entità e l'assegnazione non viene registrata con il registro di sistema di routing, la convalida automatica non verrà completata e sarà richiesta la convalida manuale. Se la convalida automatica ha esito negativo, verrà visualizzato ' AdvertisedPublicPrefixesState ' come ' Validation needed ' nell'output del comando precedente. 
> 
> Se viene visualizzato il messaggio "Convalida necessaria", raccogliere i documenti che mostrano i prefissi pubblici assegnati all'organizzazione dall'entità elencata come proprietaria dei prefissi nel registro di sistema di routing e inviarli per la convalida manuale aprendo un ticket di supporto come illustrato di seguito. 
> 
>

L'output è simile all'esempio seguente:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
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

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Per aggiornare la configurazione del peering Microsoft

È possibile aggiornare qualsiasi parte della configurazione. Nell'esempio seguente i prefissi annunciati del circuito vengono aggiornati da 123.1.0.0/24 a 124.1.0.0/24:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Per aggiungere le impostazioni del peering Microsoft IPv6 a una configurazione IPv4 esistente

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Per eliminare il peering Microsoft

Per rimuovere la configurazione di peering, eseguire l'esempio seguente:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Peering privato di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure

1. Installare la versione più recente dell'interfaccia della riga di comando di Azure. È necessario usare la versione più recente dell'interfaccia della riga di comando di Azure. * Verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.

   ```azurecli
   az login
   ```

   Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Creare un circuito ExpressRoute. Seguire le istruzioni per creare un [circuito ExpressRoute](howto-circuit-cli.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure per l'utente. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, proseguire la configurazione seguendo questa procedura.

3. Controllare che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia abilitato. Usare l'esempio seguente:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
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

4. Configurare il peering privato di Azure per il circuito. Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:

   * Una subnet /30 per il collegamento primario. La subnet non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Una subnet /30 per il collegamento secondario. La subnet non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Assicurarsi di non usare il numero 65515.
   * **Facoltativo:** un hash MD5, se si sceglie di usarne uno.

   Per configurare il peering privato di Azure per il circuito, usare l'esempio seguente:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Se si sceglie di usare un hash MD5, usare l'esempio seguente:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Per visualizzare i dettagli relativi al peering privato di Azure

Per ottenere i dettagli di configurazione, usare l'esempio seguente:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

L'output è simile all'esempio seguente:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
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

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Per aggiornare la configurazione del peering privato di Azure

Per aggiornare qualsiasi parte della configurazione, usare l'esempio seguente. In questo esempio il valore dell'ID VLAN del circuito viene aggiornato da 100 a 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Per eliminare un peering privato di Azure

Per rimuovere la configurazione di peering, eseguire l'esempio seguente:

> [!WARNING]
> Prima di eseguire questo esempio, verificare che tutte le reti virtuali e le connessioni di Copertura globale di ExpressRoute siano state rimosse. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Passaggi successivi

Successivamente, [collegare una rete virtuale a un circuito ExpressRoute](howto-linkvnet-cli.md).

* Per ulteriori informazioni sui flussi di lavoro ExpressRoute, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)
* Per ulteriori informazioni sull’uso delle reti virtuali, vedere [Panoramica sulla rete virtuale](../virtual-network/virtual-networks-overview.md).
