---
title: Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni di Azure - Interfaccia della riga di comando di Azure 1.0 | Microsoft Docs
description: Questa pagina spiega come usare l'interfaccia della riga di comando di Azure 1.0 per risolvere i problemi in Network Watcher di Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 2acbc47970acf0eb2aa1aea8535d7157bc73cbb6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-10"></a>Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni tramite l'interfaccia della riga di comando 1.0 di Azure in Network Watcher di Azure

> [!div class="op_single_selector"]
> - [di Microsoft Azure](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher offre numerose funzionalità che consentono di comprendere le risorse di rete in Azure. Una di queste funzionalità è la risoluzione dei problemi riscontrati con le risorse. La funzionalità può essere chiamata dal portale, da PowerShell, dall'interfaccia della riga di comando o dall'API REST. Quando chiamata, Network Watcher controlla l'integrità di un gateway di rete virtuale o di una connessione e restituisce i risultati.

Questo articolo usa l'interfaccia della riga di comando di Azure 1.0 multipiattaforma, disponibile per Windows, Mac e Linux. 

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

Per un elenco dei tipi di gateway supportati, consultare i [tipi di gateway supportati](/network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Panoramica

La risoluzione dei problemi delle risorse consente di risolvere i problemi che si verificano con i gateway di rete virtuale e le connessioni. Quando viene inviata una richiesta di risoluzione dei problemi delle risorse, i log vengono sottoposti a query e controllati. Dopo aver completato l'ispezione, vengono restituiti i risultati. Le richieste di risoluzione dei problemi delle risorse sono richieste a esecuzione prolungata, che possono richiedere anche diversi minuti per restituire un risultato. I log risultati vengono archiviati in un contenitore in un account di archiviazione specificato.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperare una connessione e un gateway di rete virtuale

In questo esempio la risoluzione dei problemi delle risorse viene eseguita su una connessione. È anche possibile passare il comando a un gateway di rete virtuale. Il cmdlet seguente elenca le connessioni VPN in un gruppo di risorse.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

È anche possibile eseguire il comando per visualizzare le connessioni di una sottoscrizione.

```azurecli
azure network vpn-connection list -s subscription
```

Dopo aver ottenuto il nome della connessione, è possibile eseguire questo comando per ottenere il relativo ID della risorsa:

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Il comando per la risoluzione dei problemi delle risorse restituisce i dati sullo stato della risorsa e salva i log in un account di archiviazione, per la successiva revisione. In questo passaggio viene creato un account di archiviazione. È anche possibile usare un account di archiviazione già esistente.

1. Creare l'account di archiviazione

    ```azurecli
    azure storage account create -n storageAccountName -l location -g resourceGroupName
    ```

1. Ottenere le chiavi dell'account di archiviazione

    ```azurecli
    azure storage account keys list storageAccountName -g resourcegroupName
    ```

1. Creare il contenitore

    ```azurecli
    azure storage container create --account-name storageAccountName -g resourcegroupName --account-key {storageAccountKey} --container logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Eseguire il comando per la risoluzione dei problemi delle risorse di Network Watcher

Per risolvere i problemi relativi alle risorse eseguire il cmdlet `network watcher troubleshoot`. Al cmdlet viene passato il gruppo di risorse, il nome del servizio Network Watcher, l'ID della connessione, l'ID dell'account di archiviazione, e il percorso del BLOB nel quale archiviare i risultati ottenuti.

```azurecli
azure network watcher troubleshoot -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

Dopo aver eseguito il cmdlet, Network Watcher esamina la risorsa per verificarne l'integrità. Restituisce quindi i risultati alla shell e archivia i log dei risultati nell'account di archiviazione specificato.

## <a name="understanding-the-results"></a>Informazioni sui risultati

Il testo dell'azione offre indicazioni generiche su come risolvere il problema. Se è possibile eseguire un'azione per il problema, viene fornito un collegamento con altre informazioni. Nel caso in cui non siano presenti altre indicazioni, la risposta include l'URL per aprire una richiesta di assistenza.  Per altre informazioni sulle proprietà della risposta e ciò che vie è incluso, consultare la [panoramica sulla risoluzione dei problemi in Network Watcher](network-watcher-troubleshoot-overview.md).

Per istruzioni sul download di file dall'account di archiviazione di Azure, consultare [Introduzione all'archiviazione BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Un altro strumento che può essere usato è Storage Explorer. Altre informazioni su Storage Explorer sono reperibili facendo clic sul collegamento seguente: [Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Passaggi successivi

Se sono state modificate le impostazioni che arrestano la connettività VPN, vedere [Gestire gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per tenere traccia del gruppo di sicurezza di rete e delle regole di sicurezza in questione.
