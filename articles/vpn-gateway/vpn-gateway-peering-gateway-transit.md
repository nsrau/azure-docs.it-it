---
title: 'Configurare il transito nel gateway VPN per il peering di rete virtuale: Azure Resource Manager | Microsoft Docs'
description: Configurare il transito nel gateway VPN per il peering di rete virtuale.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: d5e62bf1838c8f07068208019d28d7273c28bd63
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492346"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurare il transito nel gateway VPN per il peering di rete virtuale

Questo articolo aiuta a configurare il transito nel gateway per il peering di rete virtuale. Il [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md) connette facilmente due reti virtuali di Azure, unendole in un'unica rete per scopi di connettività. Il [transito nel gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) è una proprietà del peering che consente a una rete virtuale di utilizzare il gateway VPN nella rete virtuale con peering per la connettività cross-premise o da rete virtuale a rete virtuale. Il diagramma seguente mostra come funziona il transito nel gateway con il peering di rete virtuale.

![gateway-transit](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Nel diagramma il transito nel gateway consente alle reti virtuali con peering di usare il gateway VPN di Azure nella rete Hub-RM. La connettività disponibile nel gateway VPN, incluse le connessioni S2S, P2S e da rete virtuale a rete virtuale, si applica a tutte e tre le reti virtuali. L'opzione di transito è disponibile per il peering tra gli stessi modelli di distribuzione o modelli diversi. Il vincolo è che il gateway VPN può trovarsi solo nella rete virtuale che usa il modello di distribuzione Resource Manager, come illustrato nel diagramma.

Nell'architettura di rete hub-spoke il transito nel gateway consente alle reti virtuali spoke di condividere il gateway VPN nell'hub invece di distribuire gateway VPN in ogni rete virtuale spoke. Le route alle reti virtuali connesse al gateway o alle reti locali si propagheranno alle tabelle di routing per le reti virtuali con peering che usano il transito nel gateway. È possibile disabilitare la propagazione automatica della route dal gateway VPN. Creare una tabella di routing con l'opzione "**Disabilita propagazione route BGP**" e associare la tabella di routing alle subnet per impedire la distribuzione di route alle subnet. Per altre informazioni, vedere [Tabella di routing di una rete virtuale](../virtual-network/manage-route-table.md).

In questo documento sono descritti due scenari:

1. Entrambe le reti virtuali usano il modello di distribuzione di Resource Manager
2. La rete virtuale spoke è classica e la rete virtuale hub con gateway è in Resource Manager

## <a name="requirements"></a>Requisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

L'esempio riportato in questo documento richiede la creazione delle risorse seguenti:

1. Rete virtuale Hub-RM con un gateway VPN
2. Rete virtuale Spoke-RM
3. Rete virtuale Spoke-Classic con il modello di distribuzione classica
4. L'account in uso richiede i ruoli e l'autorizzazione necessari. Vedere la sezione [Autorizzazioni](#permissions) di questo articolo per informazioni dettagliate.

Per istruzioni, fare riferimento ai documenti seguenti:

1. [Creare un gateway VPN in una rete virtuale](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Creare un peering di rete virtuale con lo stesso modello di distribuzione](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Creare un peering di rete virtuale con modelli di distribuzione diversi](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>Autorizzazioni

Gli account usati per creare un peering di rete virtuale devono disporre del ruolo o delle autorizzazioni necessari. Nell'esempio seguente se si esegue il peering di due reti virtuali denominate Hub-RM e Spoke-Classic l'account deve avere i ruoli o le autorizzazioni seguenti per ogni rete virtuale:
    
|Rete virtuale|Modello di distribuzione|Ruolo|Autorizzazioni|
|---|---|---|---|
|Hub-RM|Gestione risorse|[Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classico|[Collaboratore reti virtuali classiche](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|Spoke-Classic|Gestione risorse|[Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classico|[Collaboratore reti virtuali classiche](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Altre informazioni sui [ruoli predefiniti](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (solo Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Peering da Resource Manager a Resource Manager con transito nel gateway

Seguire le istruzioni per creare o aggiornare i peering di rete virtuale in modo da abilitare il transito nel gateway.

1. Creare o aggiornare il peering di rete virtuale da Spoke-RM a Hub RM dal portale di Azure. Passare alla risorsa di rete virtuale Spoke-RM, fare clic su "Peer", quindi su "Aggiungi":
    - Impostare l'opzione "Resource Manager"
    - Selezionare la rete virtuale Hub-RM nella sottoscrizione corrispondente
    - Assicurarsi che l'opzione "Consenti accesso alla rete virtuale" sia abilitata
    - Impostare l'opzione "**Use remote gateways**" (Usa gateway remoti)
    - Fare clic su "OK"

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Se il peering è già stato creato, passare alla risorsa di peering, quindi abilitare l'opzione "**Use remote gateways**" in modo simile a quanto mostrato nello screenshot al passaggio (1)

3. Creare o aggiornare il peering di rete virtuale da Hub-RM a Spoke RM dal portale di Azure. Passare alla risorsa di rete virtuale Hub-RM, fare clic su "Peer", quindi su "Aggiungi":
    - Impostare l'opzione "Resource Manager"
    - Assicurarsi che l'opzione "Consenti accesso alla rete virtuale" sia abilitata
    - Selezionare la rete virtuale Spoke-RM nella sottoscrizione corrispondente
    - Impostare l'opzione "**Consenti transito gateway**"
    - Fare clic su "OK"

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Se il peering è già stato creato, passare alla risorsa di peering, quindi abilitare l'opzione "**Consenti transito gateway**" in modo simile a quanto mostrato nello screenshot al passaggio (3)

5. Verificare che lo stato del peering sia "**Connesso**" in entrambe le reti virtuali

### <a name="powershell-sample"></a>Esempio PowerShell

È inoltre possibile usare PowerShell per creare o aggiornare il peering con l'esempio precedente. Sostituire le variabili con i nomi delle reti virtuali e dei gruppi di risorse.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Peering da modello di distribuzione classica a Resource Manager con transito nel gateway

I passaggi sono simili a quelli descritti nell'esempio su Resource Manager, ad eccezione del fatto le operazioni vengono applicate solo alla rete virtuale Hub RM.

1. Creare o aggiornare il peering di rete virtuale da Hub-RM a Spoke RM dal portale di Azure. Passare alla risorsa di rete virtuale Hub-RM, fare clic su "Peer", quindi su "Aggiungi":
   - Impostare l'opzione "Classica" per il modello di distribuzione di rete virtuale
   - Selezionare la rete virtuale Spoke-Classic nella sottoscrizione corrispondente
   - Assicurarsi che l'opzione "Consenti accesso alla rete virtuale" sia abilitata
   - Impostare l'opzione "**Consenti transito gateway**"
   - Fare clic su "OK"

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Se il peering è già stato creato, passare alla risorsa di peering, quindi abilitare l'opzione "**Consenti transito gateway**" in modo simile a quanto mostrato nello screenshot al passaggio (1)

3. Non ci sono operazioni nella rete virtuale Spoke-Classic

4. Verificare che lo stato del peering sia "**Connesso**" nella rete virtuale Hub-RM

Dopo che lo stato risulta "Connesso", le reti virtuali spoke possono iniziare a usare la connettività da rete virtuale a rete virtuale o cross-premise attraverso il gateway VPN nella rete virtuale hub.

### <a name="powershell-sample"></a>Esempio PowerShell

È inoltre possibile usare PowerShell per creare o aggiornare il peering con l'esempio precedente. Sostituire le variabili e l'ID sottoscrizione con i valori dei gruppi di risorse e della rete virtuale e con la sottoscrizione. È necessario creare solo il peering di rete virtuale nella rete virtuale hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Passaggi successivi

* Acquisire altre informazioni su [comportamenti e vincoli del peering di rete virtuale](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) e [impostazioni del peering di rete virtuale](../virtual-network/virtual-network-manage-peering.md#create-a-peering) prima di creare un peering di rete virtuale per l'uso in produzione.
* Acquisire informazioni su come [creare una topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con peering di rete virtuale e transito nel gateway.
