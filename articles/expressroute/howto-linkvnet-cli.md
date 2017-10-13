---
title: Collegare una rete virtuale a un circuito ExpressRoute usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo documento offre una panoramica sulle procedure di collegamento delle reti virtuali ai circuiti ExpressRoute usando il modello di distribuzione Resource Manager e l'interfaccia della riga di comando.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: anzaman,cherylmc
ms.openlocfilehash: 0ea696e796ec3a943bc028f56da417978b728b82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Connettere una rete virtuale a un circuito ExpressRoute usando l'interfaccia della riga di comando

Questo articolo illustra come collegare reti virtuali a circuiti ExpressRoute di Azure usando l'interfaccia della riga di comando. Per creare un collegamento tramite l'interfaccia della riga di comando di Azure, è necessario creare le reti virtuali con il modello di distribuzione Resource Manager. Le reti virtuali possono essere nella stessa sottoscrizione o appartenere a un'altra sottoscrizione. Se si vuole usare un metodo diverso per connettere la rete virtuale a un circuito ExpressRoute, è possibile scegliere uno degli articoli seguenti:

> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-linkvnet-cli.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classico)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

* È necessaria la versione più recente dell'interfaccia della riga di comando. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
* Prima di procedere con la configurazione, è necessario verificare i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. 
  * Seguire le istruzioni per [creare un circuito ExpressRoute](howto-circuit-cli.md) e fare in modo che venga abilitato dal provider di connettività. 
  * Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](howto-routing-cli.md) per istruzioni relative al routing. 
  * Verificare che il peering privato di Azure sia configurato. Per abilitare la connettività end-to-end è necessario che il peering BGP tra la rete e Microsoft sia attivo.
  * Assicurarsi di disporre di una rete virtuale e di un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni per [configurare un gateway di rete virtuale per ExpressRoute](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Assicurarsi di usare `--gateway-type ExpressRoute`.

* È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuali devono essere nella stessa area geopolitica quando si usa un circuito ExpressRoute standard. 

* Abilitando il componente aggiuntivo ExpressRoute Premium, è possibile collegare una rete virtuale esterna all'area geopolitica del circuito ExpressRoute o connettere un numero maggiore di reti virtuali al circuito ExpressRoute. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Collegare una rete virtuale della stessa sottoscrizione a un circuito

È possibile connettere un gateway di rete virtuale a un circuito ExpressRoute usando l'esempio seguente. Prima di eseguire il comando, assicurarsi che il gateway di rete virtuale sia stato creato e sia pronto per il collegamento.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Collegare una rete virtuale di un'altra sottoscrizione a un circuito

È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ciascun reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma i reparti possono condividere un singolo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

> [!NOTE]
> I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.
> 
> 

![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Amministrazione: proprietari e utenti del circuito

Il "proprietario del circuito" è un utente esperto autorizzato della risorsa del circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli utenti del circuito. Gli "utenti del circuito" sono i proprietari dei gateway di rete virtuale che non sono nella stessa sottoscrizione del circuito ExpressRoute. Gli utenti del circuito possono riscattare le autorizzazioni, una per ogni rete virtuale.

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. Quando un'autorizzazione viene revocata, tutti i collegamenti vengono eliminati dalla sottoscrizione di cui è stato revocato l'accesso.

### <a name="circuit-owner-operations"></a>Operazioni del proprietario del circuito

**Per creare un'autorizzazione**

Il proprietario del circuito crea un'autorizzazione, che consente di creare una chiave di autorizzazione che può essere usata da un utente del circuito per connettere i propri gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

L'esempio seguente mostra come creare un'autorizzazione:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

La risposta contiene la chiave di autorizzazione e lo stato:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Per verificare le autorizzazioni**

Il proprietario del circuito può esaminare tutte le autorizzazioni rilasciate in un determinato circuito eseguendo questo esempio:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Per aggiungere le autorizzazioni**

Il proprietario del circuito può aggiungere autorizzazioni usando questo esempio:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Per eliminare le autorizzazioni**

Il proprietario del circuito può revocare o eliminare le autorizzazioni dell'utente eseguendo questo esempio:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operazioni dell'utente del circuito

L'utente del circuito deve richiedere l'ID peer e una chiave di autorizzazione al proprietario del circuito. La chiave di autorizzazione è un GUID.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Per riscattare un'autorizzazione di connessione**

Per riscattare un'autorizzazione di collegamento, l'utente del circuito può eseguire questo esempio:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Per rilasciare un'autorizzazione di connessione**

È possibile rilasciare un'autorizzazione eliminando il collegamento del circuito ExpressRoute alla rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).