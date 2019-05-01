---
title: 'Configurare Copertura globale di ExpressRoute: Interfaccia della riga di comando di Azure | Microsoft Docs'
description: Questo articolo descrive come collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali e abilitare il servizio Copertura globale.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 89ada41c5f3c9cf1ca7a2ac707363f57080c361d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869972"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configurare ExpressRoute globale raggiungere tramite la CLI di Azure

Questo articolo illustra come configurare Copertura globale di Azure ExpressRoute usando l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).
 
Prima di iniziare la configurazione, è necessario soddisfare i requisiti seguenti:

* Installare la versione più recente dell'interfaccia della riga di comando di Azure. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).
* Avere familiarità con i [flussi di lavoro](expressroute-workflows.md) di provisioning dei circuiti ExpressRoute.
* Assicurarsi che i circuiti ExpressRoute siano in stato Provisioning eseguito.
* Assicurarsi che il peering privato di Azure sia configurato nei circuiti ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Per iniziare la configurazione, accedere al proprio account Azure. Il comando seguente apre il browser predefinito e chiede di specificare le credenziali di accesso per l'account Azure:  

```azurecli
az login
```

Se si hanno più sottoscrizioni di Azure, verificare le sottoscrizioni per l'account:

```azurecli
az account list
```

Specificare la sottoscrizione da usare:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificare i circuiti ExpressRoute per la configurazione

È possibile abilitare ExpressRoute globale raggiungere tra qualsiasi due circuiti ExpressRoute, purché che si trovano in paesi e regioni supportati e sono stati creati in diverse località di peering. Se la sottoscrizione include entrambi i circuiti, è possibile scegliere uno dei due per eseguire la configurazione, come spiegato più avanti in questo articolo. Se i due circuiti si trovano in sottoscrizioni di Azure diverse, è necessario avere l'autorizzazione da una sottoscrizione di Azure e passare la relativa chiave di autorizzazione quando si esegue il comando di configurazione nell'altra sottoscrizione di Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Abilitare la connettività tra le reti locali

Quando si esegue il comando per abilitare la connettività, tenere presenti i requisiti seguenti per i valori dei parametri:

* *peer-circuit* deve essere l'ID risorsa completo. Ad esempio: 

  > /subscriptions/{id_sottoscrizione}/resourceGroups/{gruppo_risorse}/providers/Microsoft.Network/expressRouteCircuits/{nome_circuito}

* *address-prefix* deve essere una subnet "/29" IPv4, ad esempio "10.0.0.0/29". Gli indirizzi IP di questa subnet vengono usati per stabilire la connettività tra i due circuiti ExpressRoute. Evitare di usare indirizzi inclusi in questa subnet nelle reti virtuali di Azure o nelle reti locali.

Eseguire il comando dell'interfaccia della riga di comando seguente per connettere due circuiti ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

L'output dell'interfaccia della riga di comando è simile al seguente:

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

Al termine di questa operazione, si avrà connettività tra le reti locali su entrambi i lati attraverso i due circuiti ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Abilitare la connettività tra circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono inclusi nella stessa sottoscrizione di Azure, è necessaria l'autorizzazione. Nella configurazione seguente si genera l'autorizzazione nella sottoscrizione del circuito 2 e si passa la chiave di autorizzazione al circuito 1.

1. Generare una chiave di autorizzazione:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   L'output dell'interfaccia della riga di comando è simile al seguente:

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

1. Prendere nota dell'ID risorsa e della chiave di autorizzazione per il circuito 2.

1. Eseguire il comando seguente sul circuito 1, passando l'ID risorsa e la chiave di autorizzazione al circuito 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Al termine di questa operazione, si avrà connettività tra le reti locali su entrambi i lati attraverso i due circuiti ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Ottenere e verificare la configurazione

Usare il comando seguente per verificare la configurazione nel circuito in cui è stata eseguita (il circuito 1 nell'esempio precedente):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Nell'output dell'interfaccia della riga di comando comparirà *CircuitConnectionStatus*. Questa proprietà indica se la connettività tra i due circuiti è stata stabilita ("Connected") o meno ("Disconnected"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Disabilitare la connettività tra le reti locali

Per disabilitare la connettività, usare il comando seguente sul circuito in cui è stata eseguita la configurazione (il circuito 1 nell'esempio precedente).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Usare il comando ```show``` per verificare lo stato.

Al termine di questa operazione, non si avrà più connettività tra le reti locali attraverso i circuiti ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Copertura globale di ExpressRoute](expressroute-global-reach.md)
* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
