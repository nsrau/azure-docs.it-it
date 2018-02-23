---
title: Gestire i log di flusso del gruppo di sicurezza di rete con Azure Network Watcher - Interfaccia della riga di comando di Azure 1.0 | Microsoft Docs
description: Questa pagina illustra come gestire i log di flusso del gruppo di sicurezza di rete in Azure Network Watcher con l'interfaccia della riga di comando di Azure 1.0
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1cdef3454a326fbdbdef5ab51ac93cc8a558f34d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli-10"></a>Configurazione dei log di flusso del gruppo di sicurezza di rete con l'interfaccia della riga di comando di Azure 1.0

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

I log di flusso del gruppo di sicurezza di rete sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base a regole, scheda di rete a cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo), e se il traffico è consentito o meno.

Questo articolo usa l'interfaccia della riga di comando di Azure 1.0 multipiattaforma, disponibile per Windows, Mac e Linux. Network Watcher usa attualmente l'interfaccia della riga di comando di Azure 1.0 per il supporto dell'interfaccia della riga di comando.

## <a name="register-insights-provider"></a>Registrare il provider Insights

Per il corretto funzionamento della registrazione dei flussi è necessario che il provider **Microsoft.Insights** sia registrato. Per verificare che il provider **Microsoft.Insights** sia registrato, eseguire lo script seguente.

```azurecli
azure provider register --namespace Microsoft.Insights --subscription <subscriptionid>
```

## <a name="enable-network-security-group-flow-logs"></a>Abilitare i log di flusso dei gruppi di sicurezza di rete

L'esempio seguente mostra il comando che consente di abilitare i log di flusso:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e true
```

## <a name="disable-network-security-group-flow-logs"></a>Disabilitare i log di flusso dei gruppi di sicurezza di rete

Usare l'esempio seguente per disabilitare i log di flusso:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e false
```

## <a name="download-a-flow-log"></a>Scaricare un log di flusso

Il percorso di archiviazione di un log di flusso viene definito al momento della creazione. Uno strumento utile per accedere ai log di flusso salvati in un account di archiviazione è Esplora archivi di Microsoft Azure, disponibile qui: http://storageexplorer.com/

Se viene specificato un account di archiviazione, i file di acquisizione di pacchetti vengono salvati in un account di archiviazione nel percorso seguente:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Per informazioni sulla struttura del log, leggere la [panoramica sul log di flusso del gruppo di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [visualizzare i log di flusso con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Informazioni su come [visualizzare i log di flusso del gruppi di sicurezza di rete con strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
