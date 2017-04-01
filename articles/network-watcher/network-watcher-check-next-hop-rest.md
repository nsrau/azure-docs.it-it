---
title: "Individuare l&quot;hop successivo con la funzionalità Hop successivo di Network Watcher di Azure - REST | Documentazione Microsoft"
description: "Questo articolo descrive come individuare il tipo di hop successivo e l&quot;indirizzo IP mediante la funzionalità Hop successivo usando l&quot;API REST di Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 04801ce75a767da2756165f51f49192f89887380
ms.lasthandoff: 03/31/2017

---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Individuare il tipo di hop successivo tramite la funzionalità Hop successivo di Network Watcher di Azure usando l'API REST di Azure

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [API REST di Azure](network-watcher-check-next-hop-rest.md)

Hop successivo è una funzionalità di Network Watcher che consente di recuperare il tipo di hop successivo e l'indirizzo IP in base a una macchina virtuale specificata. La funzionalità è utile per determinare se il traffico in uscita da una macchina virtuale attraversa gateway, Internet o reti virtuali per arrivare alla propria destinazione.

## <a name="before-you-begin"></a>Prima di iniziare

ARMclient viene usato per chiamare l'API REST con PowerShell. ARMClient è reperibile in Chocolatey in [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient)

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo usa la funzionalità Hop successivo di Network Watcher che rileva il tipo di hop successivo e l'indirizzo IP di una risorsa. Per altre informazioni sulla funzionalità di individuazione dell'hop successivo, consultare la [panoramica sulla funzionalità Hop successivo](network-watcher-next-hop-overview.md).

In questo scenario si apprenderà come:

* Recuperare l'hop successivo per una macchina virtuale.

## <a name="log-in-with-armclient"></a>Accedere con ARMClient

Accedere ad armclient con le credenziali di Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperare una macchina virtuale

Eseguire lo script seguente per restituire la macchina virtuale. Queste informazioni sono necessarie per eseguire l'hop successivo.

Il codice seguente richiede i valori per le variabili seguenti:

- **subscriptionId**: l'ID sottoscrizione da usare.
- **resourceGroupName**: il nome di un gruppo di risorse contenente le macchine virtuali.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

L'ID della macchina virtuale dell'output seguente viene usato nell'esempio seguente:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Ottenere l'hop successivo

Dopo aver creato l'intestazione di autorizzazione, è possibile recuperare l'hop successivo da una macchina virtuale. Perché l'esempio di codice funzioni devono essere sostituiti i valori seguenti.

> [!Important]
> Per le chiamate dell'API REST di Network Watcher, il nome del gruppo di risorse nell'URI della richiesta è il gruppo di risorse che contiene Network Watcher, non le risorse su cui eseguono le azioni di diagnostica.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> La funzionalità Hop successivo richiede che la risorsa VM sia allocata.

## <a name="results"></a>Risultati

Il seguente frammento di codice è un esempio dell'output ricevuto. I risultati contengono i valori seguenti:

* **nextHopType**: questo è uno dei seguenti valori: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway o None.
* **nextHopIpAddress**: l'indirizzo IP dell'hop successivo.
* **routeTableId**: il valore è un URI per la tabella di route associata alla route oppure, se non ci sono route definite dall'utente, viene restituito il valore *System Route*.

Di seguito sono riportati i risultati in formato json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver scoperto l'hop successivo per una macchina virtuale, è possibile visualizzare le informazioni sulla sicurezza delle risorse di rete visitando [Security View overview](network-watcher-security-group-view-overview.md) (Panoramica della visualizzazione della sicurezza)















