---
title: Gestire i log di flusso NSG-interfaccia della riga di comando
titleSuffix: Azure Network Watcher
description: Questa pagina illustra come gestire i log di flusso del gruppo di sicurezza di rete in Network Watcher di Azure con l'interfaccia della riga di comando di Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840962"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configurazione dei log di flusso del gruppo di sicurezza di rete con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

I log di flusso del gruppo di sicurezza di rete sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base a regole, scheda di rete a cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo), e se il traffico è consentito o meno.

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando (CLI) di Azure per Mac, Linux e Windows](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrare il provider Insights

Per il corretto funzionamento della registrazione dei flussi è necessario che il provider **Microsoft.Insights** sia registrato. Per verificare che il provider **Microsoft.Insights** sia registrato, eseguire lo script seguente.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Abilitare i log di flusso dei gruppi di sicurezza di rete

L'esempio seguente mostra il comando che consente di abilitare i log di flusso:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

L'account di archiviazione specificato non può avere regole di rete che limitano l'accesso alla rete solo ai servizi Microsoft o a specifiche reti virtuali. L'account di archiviazione può essere nella stessa sottoscrizione di Azure, o in una differente, rispetto al gruppo di sicurezza di rete per cui si abilita il log di flusso. Se si usano sottoscrizioni diverse, devono essere entrambe associate allo stesso tenant di Azure Active Directory. L'account usato per ogni sottoscrizione deve avere le [autorizzazioni necessarie](required-rbac-permissions.md). 

Se l'account di archiviazione è in un gruppo di risorse o in una sottoscrizione diversi da quelli del gruppo di sicurezza di rete, specificare l'ID completo dell'account di archiviazione anziché il nome. Se, ad esempio, l'account di archiviazione si trova in un gruppo di risorse denominato *RG-Storage*, anziché specificare *storageAccountName* nel comando precedente, specificare */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Disabilitare i log di flusso dei gruppi di sicurezza di rete

Usare l'esempio seguente per disabilitare i log di flusso:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Scaricare un log di flusso

Il percorso di archiviazione di un log di flusso viene definito al momento della creazione. Uno strumento utile per accedere ai log di flusso salvati in un account di archiviazione è Microsoft Azure Storage Explorer, disponibile qui: https://storageexplorer.com/

Se viene specificato un account di archiviazione, i log di flusso vengono salvati in un account di archiviazione nel percorso seguente:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Fasi successive

Informazioni su come [visualizzare i log di flusso con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Informazioni su come [visualizzare i log di flusso del gruppi di sicurezza di rete con strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
