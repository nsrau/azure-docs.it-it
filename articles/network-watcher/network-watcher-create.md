---
title: Creare un&quot;istanza di Azure Network Watcher | Microsoft Docs
description: Questa pagina illustra la procedura per la creazione di un&quot;istanza di Network Watcher tramite il portale di Azure e l&quot;API REST
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 7d8eb5972d35eac6cb55fc393090cfcc21ec341c
ms.lasthandoff: 03/31/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Creare un'istanza di Azure Network Watcher

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Il monitoraggio a livello di scenario permette di diagnosticare i problemi in una visualizzazione completa a livello di rete. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

> [!NOTE]
> Dato che Network Watcher supporta attualmente solo l'interfaccia della riga di comando 1.0, le istruzioni fornite per creare una nuova istanza di Network Watcher sono quelle per l'interfaccia della riga di comando 1.0.

## <a name="create-a-network-watcher-in-the-portal"></a>Creare un'istanza di Network Watcher nel portale

Passare ad **Altri servizi** > **Rete** > **Network Watcher**. È possibile selezionare tutte le sottoscrizioni per cui si vuole abilitare Network Watcher. Questa azione crea un'istanza di Network Watcher in ogni area in cui è disponibile.

![Creare un'istanza di Network Watcher][1]

## <a name="create-a-network-watcher-with-powershell"></a>Creare un'istanza di Network Watcher con PowerShell

Per creare un'istanza di Network Watcher, eseguire questo esempio:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Creare un'istanza di Network Watcher con l'API REST

ARMclient viene usato per chiamare l'API REST con PowerShell. ARMClient è reperibile in Chocolatey in [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) (ARMClient in Chocolatey)

### <a name="log-in-with-armclient"></a>Accedere con ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Creare l'istanza di Network Watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'istanza di Network Watcher, è possibile approfondire le funzionalità disponibili, indicate di seguito:

* [Topologia](network-watcher-topology-overview.md)
* [Acquisizione pacchetti](network-watcher-packet-capture-overview.md)
* [Verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)
* [Hop successivo](network-watcher-next-hop-overview.md)
* [Visualizzazione di un gruppo di sicurezza](network-watcher-security-group-view-overview.md)
* [Registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)
* [Risoluzione dei problemi del gateway di rete virtuale](network-watcher-troubleshoot-overview.md)

Dopo aver creato un'istanza di Network Watcher, è possibile configurare l'acquisizione pacchetti in base a quanto indicato nell'articolo relativo alla [creazione dell'acquisizione pacchetti attivata da avvisi](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-create/figure1.png












