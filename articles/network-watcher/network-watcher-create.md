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
ms.sourcegitcommit: 064c8f7c312aaac94f20224e99b9e29c641e0349
ms.openlocfilehash: 7099081e7294c64c1b9076b58a2c87f4f7ca7e44
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Creare un'istanza di Azure Network Watcher

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Il monitoraggio a livello di scenario permette di diagnosticare i problemi in una visualizzazione completa a livello di rete. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

## <a name="register-the-preview-capability"></a>Registrare la funzionalità in anteprima

Network Watcher è attualmente in anteprima. Per usare le funzionalità di Network Watcher, la funzionalità deve essere registrata. A tale scopo, eseguire questo esempio di PowerShell:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
```

Per verificare se la registrazione è riuscita, eseguire questo esempio di Powershell:

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

Se la funzionalità è stata registrata correttamente, l'output deve corrispondere a quanto segue:

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

####<a name="instructions-for-cli"></a>Istruzioni per l'interfaccia della riga di comando

Per eseguire la registrazione:

```CLI
azure provider register Microsoft.Network
azure feature register  Microsoft.Network AllowNetworkWatcher
```
Per verificare se la registrazione è riuscita, eseguire questo comando dell'interfaccia della riga di comando:

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

Se la funzionalità è stata registrata correttamente, l'output deve corrispondere a quanto segue:
```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

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

Dopo aver creato un'istanza di Network Watcher, è possibile configurare l'acquisizione pacchetti in base a quanto indicato nell'articolo relativo alla [creazione dell'acquisizione pacchetti attivata da avvisi](network-watcher-alert-triggered-packet-capture.md).

[1]: ./media/network-watcher-create/figure1.png












