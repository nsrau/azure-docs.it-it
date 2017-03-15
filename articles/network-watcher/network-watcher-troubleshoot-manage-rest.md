---
title: Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni tramite Network Watcher di Azure - REST | Documentazione Microsoft
description: Questa pagina illustra come risolvere i problemi relativi al gateway di rete virtuale e alle connessioni con Network Watcher di Azure tramite REST
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: f71c760c4e565ae31eb82d3ff5ce865299f81a4c
ms.lasthandoff: 02/23/2017


---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni tramite Network Watcher di Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher offre numerose funzionalità che consentono di comprendere le risorse di rete in Azure. Una di queste funzionalità è la risoluzione dei problemi riscontrati con le risorse.  La funzionalità può essere chiamata da PowerShell, dall'interfaccia della riga di comando o dall'API REST. Quando chiamata, Network Watcher controlla l'integrità di un gateway di rete virtuale o di una connessione e restituisce i risultati.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

Questo articolo illustra le diverse attività di gestione attualmente disponibili per la risoluzione dei problemi relativi alle risorse.

- [**Risolvere i problemi relativi a un gateway di rete virtuale**](#troubleshoot-a-virtual-network-gateway)
- [**Risolvere i problemi relativi a una connessione**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Prima di iniziare

ARMclient viene usato per chiamare l'API REST con PowerShell. ARMClient è reperibile in Chocolatey in [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient)

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="overview"></a>Panoramica

La risoluzione dei problemi relativi a Network Watcher consente di risolvere i problemi che si verificano con i gateway di rete virtuale e le connessioni. Quando viene inviata una richiesta di risoluzione dei problemi delle risorse, i log vengono sottoposti a query e controllati. Dopo aver completato l'ispezione, vengono restituiti i risultati. Le richieste di risoluzione dei problemi delle API sono richieste a esecuzione prolungata, che possono richiedere anche diversi minuti per restituire un risultato. I log vengono archiviati in un contenitore di un account di archiviazione.


## <a name="log-in-with-armclient"></a>Accedere con ARMClient

```PowerShell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Risolvere i problemi relativi a un gateway di rete virtuale


### <a name="post-the-troubleshoot-request"></a>PUBBLICARE la richiesta di risoluzione dei problemi

Nell'esempio seguente viene eseguita una query sullo stato di un gateway di rete virtuale.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 "
```

Poiché questa operazione ha una lunga esecuzione, l'URI per le query dell'operazione e l'URI per il risultato vengono restituiti nell'intestazione della risposta come illustrato nella risposta seguente:

**Valori importanti**

* **Azure-AsyncOperation**: questa proprietà contiene l'URI per eseguire le query sull'operazione di risoluzione dei problemi asincrona
* **Location**: questa proprietà contiene l'URI in cui si trovano i risultati una volta completata l'operazione

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Eseguire una query all'operazione asincrona per il completamento

Usare l'URI delle operazioni per eseguire query sullo stato di avanzamento dell'operazione, come illustrato nell'esempio seguente:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30"
```

Mentre l'operazione è in corso, la risposta mostra **InProgress** come illustrato nell'esempio seguente:

```json
{
  "status": "InProgress"
}
```

Al termine dell'operazione, lo stato diventa **Succeeded**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Recuperare i risultati

Quando lo stato restituito è **Succeeded**, chiamare un metodo GET sull'URI operationResult per recuperare i risultati.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30"
```

Le risposte seguenti sono esempi di una tipica risposta ridotta, restituita quando si eseguono query sui risultati della risoluzione dei problemi relativi a un gateway. Vedere [Informazioni sui risultati](#understanding-the-results) per chiarimenti sul significato delle proprietà nella risposta.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Risoluzione dei problemi relativi alle connessioni

Nell'esempio seguente viene eseguita una query sullo stato di una connessione.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 "
```

> [!NOTE]
> L'operazione di risoluzione dei problemi non può essere eseguita in parallelo su una connessione e sui gateway corrispondenti. L'operazione deve essere completata prima di eseguire il gateway sulla risorsa precedente.

Poiché questa transazione ha una lunga esecuzione, nell'intestazione della risposta l'URI per le query dell'operazione e l'URI per il risultato vengono restituiti come illustrato nella risposta seguente:

**Valori importanti**

* **Azure-AsyncOperation**: questa proprietà contiene l'URI per eseguire le query sull'operazione di risoluzione dei problemi asincrona
* **Location**: questa proprietà contiene l'URI in cui si trovano i risultati una volta completata l'operazione

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Eseguire una query all'operazione asincrona per il completamento

Usare l'URI delle operazioni per eseguire query sullo stato di avanzamento dell'operazione, come illustrato nell'esempio seguente:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Mentre l'operazione è in corso, la risposta mostra **InProgress** come illustrato nell'esempio seguente:

```json
{
  "status": "InProgress"
}
```

Al termine dell'operazione, lo stato diventa **Succeeded**.

```json
{
  "status": "Succeeded"
}
```

Le risposte seguenti sono esempi di una tipica risposta restituita quando si eseguono query sui risultati della risoluzione dei problemi relativi a una connessione.

### <a name="retrieve-the-results"></a>Recuperare i risultati

Quando lo stato restituito è **Succeeded**, chiamare un metodo GET sull'URI operationResult per recuperare i risultati.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Le risposte seguenti sono esempi di una tipica risposta restituita quando si eseguono query sui risultati della risoluzione dei problemi relativi a una connessione.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Informazioni sui risultati

Il testo dell'azione offre indicazioni generiche su come risolvere il problema. Se è possibile eseguire un'azione per il problema, viene fornito un collegamento con altre informazioni. Nel caso in cui non siano presenti altre indicazioni, la risposta include l'URL per aprire una richiesta di assistenza.  Per altre informazioni sulle proprietà della risposta e ciò che vie è incluso, consultare la [panoramica sulla risoluzione dei problemi in Network Watcher](network-watcher-troubleshoot-overview.md).

Per istruzioni sul download di file dall'account di archiviazione di Azure, consultare [Introduzione all'archiviazione BLOB di Azure con .NET](../storage/storage-dotnet-how-to-use-blobs.md). Un altro strumento che può essere usato è Storage Explorer. Altre informazioni su Storage Explorer sono reperibili facendo clic sul collegamento seguente: [Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Passaggi successivi

Se sono state modificate le impostazioni che arrestano la connettività VPN, vedere [Gestire gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per tenere traccia del gruppo di sicurezza di rete e delle regole di sicurezza in questione.

