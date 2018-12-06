---
title: Configurare Copertura globale di Azure ExpressRoute con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali e abilitare il servizio Copertura globale.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: cherylmc
ms.openlocfilehash: 9d41ab26876d464187466f566bbfafc4861c799d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333261"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Configurare Copertura globale di Azure ExpressRoute con l'interfaccia della riga di comando di Azure (anteprima)
Questo articolo illustra come configurare Copertura globale di ExpressRoute con l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Prima di iniziare
> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Prima di avviare la configurazione, è necessario soddisfare i requisiti seguenti.

* Installare la versione più recente dell'interfaccia della riga di comando di Azure. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).
* Avere familiarità con i [flussi di lavoro](expressroute-workflows.md) di provisioning dei circuiti ExpressRoute.
* Assicurarsi che i circuiti ExpressRoute siano in stato Provisioning eseguito.
* Assicurarsi che il peering privato di Azure sia configurato nei circuiti ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Accedere all'account Azure
Per avviare la configurazione, è necessario accedere all'account Azure. Il comando apre il browser predefinito e richiede le credenziali di accesso per l'account Azure.  

```azurecli
az login
```

Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

```azurecli
az account list
```

Specificare la sottoscrizione da usare.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificare i circuiti ExpressRoute per la configurazione
È possibile abilitare Copertura globale di ExpressRoute tra due circuiti ExpressRoute qualsiasi, purché si trovino nelle aree supportate e vengano creati in località di peering diverse. Se la sottoscrizione è proprietaria di entrambi i circuiti, è possibile scegliere uno dei due circuiti per l'esecuzione della configurazione nelle sezioni seguenti. Se i due circuiti si trovano in diverse sottoscrizioni di Azure, sarà necessaria l'autorizzazione da una sottoscrizione di Azure e si dovrà passare la chiave di autorizzazione quando si esegue il comando di configurazione nell'altra sottoscrizione di Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Abilitare la connettività tra le reti locali

Eseguire il comando dell'interfaccia della riga di comando seguente per connettere due circuiti ExpressRoute.

> [!NOTE]
> *peer-circuit* deve essere l'ID risorsa completo, ad esempio
> ```
> */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}*
> ```
> 

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

> [!IMPORTANT]
> *-AddressPrefix* deve essere una subnet /29 IPv4, ad esempio "10.0.0.0/29". Si useranno gli indirizzi IP in questa subnet per stabilire la connettività tra i due circuiti ExpressRoute. Evitare di usare indirizzi inclusi in questa subnet nelle reti virtuali di Azure o nelle reti locali.
> 

L'output dell'interfaccia della riga di comando è simile al seguente.

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono inclusi nella stessa sottoscrizione di Azure, sarà necessaria l'autorizzazione. Nella configurazione seguente l'autorizzazione viene generata nella sottoscrizione del circuito 2 e la chiave di autorizzazione viene passata al circuito 1.

Generare una chiave di autorizzazione. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

L'output dell'interfaccia della riga di comando è simile al seguente.

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

Annotare l'ID risorsa del circuito 2, insieme alla chiave di autorizzazione.

Eseguire il comando seguente sul circuito 1. Passare l'ID risorsa del circuito 2 e la chiave di autorizzazione 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Ottenere e verificare la configurazione

Eseguire questo comando per verificare la configurazione nel circuito in cui è stata eseguita, ad esempio circuito 1 nell'esempio precedente.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Nell'output dell'interfaccia della riga di comando comparirà *CircuitConnectionStatus*. Questo indicherà se è stata stabilita la connettività tra i due circuiti, con valore "Connected", o meno, con valore "Disconnected". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Disabilitare la connettività tra le reti locali

Per disabilitare la connettività, eseguire i comandi sul circuito in cui è stata eseguita la configurazione, ad esempio circuito 1 nell'esempio precedente.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

È possibile eseguire il comando Show CLI per verificare lo stato. 

Al termine dell'operazione precedente, non sarà più presente connettività tra le reti locali tramite i circuiti ExpressRoute. 


## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Copertura globale di ExpressRoute](expressroute-global-reach.md)
* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)


