---
title: Individuare l'hop successivo con Azure Network Watcher - Interfaccia della riga di comando di Azure 1.0 | Microsoft Docs
description: "Questo articolo descrive come individuare il tipo di hop successivo e l'indirizzo IP tramite la funzionalità Hop successivo dell'interfaccia della riga di comando di Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Individuare il tipo di hop successivo tramite la funzionalità Hop successivo di Azure Network Watcher usando l'interfaccia della riga di comando di Azure 1.0

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST di Azure](network-watcher-check-next-hop-rest.md)

Hop successivo è una funzionalità di Network Watcher che consente di recuperare il tipo di hop successivo e l'indirizzo IP in base a una macchina virtuale specificata. La funzionalità è utile per determinare se il traffico in uscita da una macchina virtuale attraversa gateway, Internet o reti virtuali per arrivare alla propria destinazione.

Questo articolo usa l'interfaccia della riga di comando di Azure 1.0 multipiattaforma, disponibile per Windows, Mac e Linux.

## <a name="before-you-begin"></a>Prima di iniziare

In questo scenario viene usata l'interfaccia della riga di comando di Azure per trovare il tipo di hop successivo e l'indirizzo IP.

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo usa la funzionalità Hop successivo di Network Watcher che rileva il tipo di hop successivo e l'indirizzo IP di una risorsa. Per altre informazioni sulla funzionalità di individuazione dell'hop successivo, consultare la [panoramica su Hop successivo](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Ottenere l'hop successivo

Per ottenere l'hop successivo viene usato il cmdlet `azure netowrk watcher next-hop`. Al cmdlet vengono passati il gruppo di risorse di Network Watcher, l'ID della macchina virtuale, l'indirizzo IP di origine e l'indirizzo IP di destinazione. Nell'esempio seguente, l'indirizzo IP di destinazione corrisponde a una macchina virtuale in un'altra rete virtuale. Tra le due reti virtuali esiste un gateway di rete virtuale. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Se la macchina virtuale è dotata di più schede NIC e su una qualsiasi delle schede NIC è abilitato l'inoltro dell'IP, è necessario specificare il parametro NIC (-i nic-id). In caso contrario, è facoltativo.

## <a name="review-results"></a>Esaminare i risultati

Al termine vengono forniti i risultati. Viene inoltre restituito l'indirizzo IP dell'hop successivo e il tipo di risorsa corrispondente.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

L'elenco seguente mostra i valori NextHopType attualmente disponibili:

**Tipo di hop successivo**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nessuna

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come verificare le impostazioni del gruppo di sicurezza di rete a livello di codice, consultare l'articolo su come [verificare i gruppi di sicurezza di rete con Network Watcher](network-watcher-nsg-auditing-powershell.md)
