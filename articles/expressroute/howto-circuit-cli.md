---
title: 'Guida introduttiva: creare e modificare un circuito ExpressRoute: interfaccia della riga di comando di Azure'
description: Questa Guida introduttiva illustra come creare, effettuare il provisioning, verificare, aggiornare, eliminare e deprovisionare un circuito ExpressRoute usando l'interfaccia della riga di comando di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: eebb2693d3bc0f65059c6c3c377f1afb7ae7eccd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757606"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Guida introduttiva: creare e modificare un circuito ExpressRoute usando l'interfaccia della riga di comando

Questa Guida introduttiva descrive come creare un circuito ExpressRoute di Azure usando l'interfaccia della riga di comando (CLI). Questo articolo descrive anche come controllare lo stato, eseguire l'aggiornamento o effettuare l'eliminazione e il deprovisioning di un circuito.

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installare la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione

Per iniziare la configurazione, accedere al proprio account Azure. Se si usa il Cloud Shell "prova", l'accesso viene eseguito automaticamente. Per eseguire la connessione, usare gli esempi che seguono:

```azurecli-interactive
az login
```

Controllare le sottoscrizioni per l'account.

```azurecli-interactive
az account list
```

Selezionare la sottoscrizione per la quale si vuole creare un circuito ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Ottenere l'elenco dei provider, delle località e delle larghezze di banda supportate

Prima di creare un circuito ExpressRoute, è necessario avere l'elenco delle località, delle opzioni di larghezza di banda e dei provider di connettività supportati. Il comando CLI `az network express-route list-service-providers` restituisce queste informazioni, che verranno usate nei passaggi successivi:

```azurecli-interactive
az network express-route list-service-providers
```

La risposta restituita è simile all'esempio seguente:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Controllare la riposta per verificare se è presente il proprio provider di connettività. Prendere nota delle informazioni seguenti, che saranno necessarie per la creazione di un circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

È ora possibile creare un circuito ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Creare un circuito ExpressRoute

> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Eseguire l'operazione quando il provider di connettività è pronto a effettuare il provisioning del circuito.
>
>

Se non si ha già un gruppo di risorse, è necessario crearne uno prima di creare il circuito ExpressRoute. È possibile creare un gruppo di risorse eseguendo il comando seguente:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix nella Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni quando si effettua la richiesta.

Verificare di aver specificato il livello e la famiglia SKU corretti:

* Il livello SKU determina se un circuito ExpressRoute è [locale](expressroute-faqs.md#expressroute-local), standard o [Premium](expressroute-faqs.md#expressroute-premium). È possibile specificare *local*, * standard o *Premium*. Non è possibile modificare lo SKU da *Standard/Premium* a *local*.
* La famiglia SKU determina il tipo di fatturazione. È possibile specificare *MeteredData* per un piano dati a consumo e *UnlimitedData* per un piano dati senza limiti. È possibile modificare il tipo di fatturazione da *MeteredData* a *UnlimitedData*, ma non è possibile modificare il tipo da *UnlimitedData* a *MeteredData*. Un circuito *locale* è solo *UnlimitedData* .


Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

La risposta contiene la chiave di servizio.

### <a name="list-all-expressroute-circuits"></a>Elencare tutti i circuiti ExpressRoute

Per ottenere un elenco di tutti i circuiti ExpressRoute creati, eseguire il `az network express-route list` comando. È possibile recuperare queste informazioni in qualsiasi momento usando questo comando. Per ottenere un elenco di tutti i circuiti, effettuare la chiamata senza parametri.

```azurecli-interactive
az network express-route list
```

La chiave di servizio è indicata nel campo *ServiceKey* della risposta.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando con il parametro '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Inviare la chiave di servizio al provider di connettività per il provisioning

'ServiceProviderProvisioningState' offre informazioni sullo stato di provisioning corrente sul lato provider del servizio. Le informazioni di stato indicano lo stato sul lato Microsoft. Per altre informazioni, vedere [l'articolo sui flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Il circuito passa allo stato seguente quando il provider di connettività lo Abilita per l'utente:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Per usare il circuito ExpressRoute, lo stato deve essere il seguente:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Controllare periodicamente lo stato e la condizione della chiave del circuito

Verificando lo stato e lo stato della chiave del servizio si informa che il provider ha effettuato il provisioning del circuito. Dopo la configurazione del circuito, lo stato di *ServiceProviderProvisioningState* visualizzato è *Provisioned*, come illustrato nell'esempio seguente:

```azurecli-interactive
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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>Creare la configurazione di routing

Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](howto-routing-cli.md) per creare e modificare i peering del circuito.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito una VPN IP, come MPLS), il provider di connettività configura e gestisce il routing per conto dell'utente.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Collegare una rete virtuale a un circuito ExpressRoute

Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](howto-linkvnet-cli.md).

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modifica di un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività. È possibile apportare le modifiche seguenti senza tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute. La modifica dello SKU da *Standard/Premium* a *local* non è supportata.
* È possibile aumentare la larghezza di banda del circuito ExpressRoute a condizione che sulla porta sia disponibile capacità. Tuttavia, il downgrade della larghezza di banda di un circuito non è supportato.
* Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. Tuttavia, la modifica del piano di misurazione da dati senza limiti a dati a consumo non è supportata.
* È possibile abilitare e disabilitare l'opzione *Consenti operazioni classiche*.

Per altre informazioni su limiti e limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Per abilitare il componente aggiuntivo ExpressRoute Premium

È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il comando seguente:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Le funzionalità del componente aggiuntivo ExpressRoute Premium sono così abilitate nel circuito. La fatturazione della funzionalità del componente aggiuntivo Premium inizia non appena il comando viene eseguito correttamente.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Per disabilitare il componente aggiuntivo ExpressRoute Premium

> [!IMPORTANT]
> Questa operazione può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.
>
>

Prima di disabilitare il componente aggiuntivo ExpressRoute Premium, tenere conto dei criteri seguenti:

* Prima di effettuare il downgrade da Premium a Standard, è necessario assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* Tutte le reti virtuali in altre aree geopolitiche devono essere prima scollegate. Se non si rimuove il collegamento, la richiesta di aggiornamento avrà esito negativo e si continuerà a fatturare le tariffe Premium.
* La tabella di route deve includere meno di 4.000 route per il peering privato. Se le dimensioni della tabella di route sono maggiori di 4.000 route, la sessione BGP verrà eliminata. La sessione BGP non verrà riabilitata finché il numero di prefissi annunciati non sarà inferiore a 4.000.

È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando l'esempio seguente:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Per aggiornare la larghezza di banda del circuito ExpressRoute

Per le opzioni relative alla larghezza di banda supportate per il provider, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md). È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente.

> [!IMPORTANT]
> Se la capacità sulla porta esistente non è sufficiente, potrebbe essere necessario ricreare il circuito ExpressRoute. Il circuito non può essere aggiornato se in tale posizione non è disponibile capacità aggiuntiva.
>
> Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda richiede il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.
>

Una volta stabilite le dimensioni necessarie, usare il comando seguente per ridimensionare il circuito:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Il circuito verrà aggiornato sul lato Microsoft. Sarà poi necessario contattare il provider di connettività perché aggiorni le configurazioni corrispondenti in base a questa modifica. In seguito alla notifica, Microsoft inizia la fatturazione in base all'opzione relativa alla larghezza di banda aggiornata.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Per passare lo SKU dal piano a consumo al piano senza limiti

È possibile modificare lo SKU di un circuito ExpressRoute usando l'esempio seguente:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Per controllare l'accesso all'ambiente classico e all'ambiente Resource Manager

Vedere le istruzioni contenute in [Spostare i circuiti ExpressRoute dal modello di distribuzione classica a quello Resource Manager](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning di un circuito ExpressRoute

Per effettuare il deprovisioning e l'eliminazione di un circuito ExpressRoute, assicurarsi di comprendere i criteri seguenti:

* Tutte le reti virtuali devono essere scollegate dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi viene impostato su **senza provisioning**, è possibile eliminare il circuito. La fatturazione per il circuito verrà arrestata.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito ed eseguito il provisioning con il provider, continuare con il passaggio successivo per configurare il peering:

> [!div class="nextstepaction"]
> [Creare e modificare il routing per un circuito ExpressRoute](howto-routing-cli.md)
