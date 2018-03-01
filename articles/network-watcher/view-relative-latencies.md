---
title: "Visualizzare le latenze relative da località specifiche alle aree di Azure | Microsoft Docs"
description: "Informazioni su come visualizzare le latenze relative attraverso i provider Internet da località specifiche alle aree di Azure."
services: network-watcher
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Visualizzare le latenze relative da località specifiche alle aree di Azure

Questa esercitazione descrive come usare il servizio [Network Watcher](network-watcher-monitoring-overview.md) di Azure per determinare più facilmente l'area di Azure in cui distribuire un'applicazione o un servizio, in base ai dati demografici degli utenti. Questo servizio è utile anche per valutare con più facilità le connessioni dei provider di servizi ad Azure.  
        
## <a name="create-a-network-watcher"></a>Creare un'istanza di Network Watcher

Se è già disponibile un'istanza di Network Watcher in almeno un'[area](https://azure.microsoft.com/regions) di Azure, è possibile ignorare le attività in questa sezione. Creare un gruppo di risorse per l'istanza di Network Watcher. In questo esempio il gruppo di risorse viene creato nell'area Stati Uniti orientali, ma è possibile crearlo in qualsiasi area di Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Creare un'istanza di Network Watcher. È necessario che sia disponibile un'istanza di Network Watcher in almeno un'area di Azure. In questo esempio viene creata un'istanza di Network Watcher nell'area Stati Uniti orientali di Azure.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Confrontare le latenze di rete relative da una località specifica a una singola area di Azure

Valutare i provider di servizi o risolvere i problemi segnalati dagli utenti, come ad esempio la lentezza del sito, da una località specifica all'area di Azure in cui viene distribuito un servizio. Ad esempio, il comando seguente restituisce le latenze medie relative dei provider di servizi Internet tra lo stato di Washington negli Stati Uniti e l'area Stati Uniti occidentali 2 di Azure fra il 13 e il 15 dicembre 2017:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> L'area specificata nel comando precedente non deve necessariamente coincidere con quella specificata al momento del recupero dell'istanza di Network Watcher. Per il comando precedente è sufficiente specificare un'istanza di Network Watcher esistente. L'istanza di Network Watcher può trovarsi in qualsiasi area. Se si specificano valori per `-Country` e `-State`, questi devono essere validi, con l'esatta distinzione tra maiuscole e minuscole. Sono disponibili dati per un numero limitato di paesi, stati e città. Eseguire i comandi descritti in [Visualizzare i paesi, gli stati, le città e i provider disponibili](#view-available) per visualizzare un elenco di paesi, città e stati disponibili da usare con il comando precedente. 

> [!WARNING]
> Per `-StartTime` e `-EndTime` è necessario specificare una data successiva al 14 novembre 2017, altrimenti non viene restituito alcun dato. 

Di seguito è riportato l'output del comando precedente:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

Nell'output restituito il valore di **Score** corrisponde alla latenza relativa attraverso le aree e i provider. Se il punteggio è 1, la latenza è massima (peggiore). Se invece è 100, la latenza è minima. Le latenze relative vengono calcolate in base a una media giornaliera. Nell'esempio precedente è evidente che le latenze sono le stesse in entrambi i giorni e vi è una piccola differenza tra la latenza dei due provider, ma è altrettanto evidente che entrambi i provider hanno latenze basse nella scala da 1 a 100. Se nel caso specifico questo comportamento è prevedibile, poiché lo stato di Washington negli Stati Uniti è fisicamente vicino all'area Stati Uniti occidentali 2 di Azure, talvolta non si ottengono risultati come previsto. Specificando un intervallo di date più ampio, è possibile ottenere un valore medio più preciso per la latenza nel tempo.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Confrontare le latenze di rete relative da una località specifica a tutte le aree di Azure

Anziché specificare le latenze relative tra una località e una determinata area di Azure tramite `-Location`, è possibile determinare le latenze relative da una località fisica specifica a tutte le aree di Azure. Il comando seguente, ad esempio, consente di valutare l'area di Azure in cui distribuire un servizio se gli utenti principali sono utenti di Comcast che si trovano nello stato di Washington:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
Diversamente dal caso in cui si specifica un'unica località, se non si definisce una località o se ne definiscono più di una, ad esempio "West US2", "West US", quando si esegue il comando è necessario specificare un provider di servizi Internet. 

## <a name="view-available"></a>Visualizzare i paesi, gli stati, le città e i provider disponibili

Sono disponibili dati per paesi, stati, città e provider di servizi Internet specifici. Per visualizzare un elenco di tutti i provider di servizi Internet, i paesi, gli stati e le città disponibili per cui è possibile visualizzare i dati, immettere il comando seguente:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

I dati sono disponibili solo per i paesi, gli stati e le città restituiti dal comando precedente. Per il comando precedente è necessario specificare un'istanza di Network Watcher esistente. Nell'esempio è specificata l'istanza di Network Watcher *NetworkWatcher_eastus* in un gruppo di risorse denominato *NetworkWatcherRG*, ma è possibile specificare qualsiasi istanza di Network Watcher esistente. Se non è disponibile un'istanza di Network Watcher esistente, crearne una eseguendo le attività descritte in [Creare un'istanza di Network Watcher](#create-a-network-watcher). 

Dopo aver eseguito il comando precedente, è possibile filtrare l'output restituito specificando valori validi per **Country**, **State** e **City**, se necessario.  Ad esempio, per visualizzare l'elenco dei provider di servizi Internet disponibili a Seattle nello stato di Washington negli Stati Uniti, immettere il comando seguente:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Per **Country** deve essere specificato un valore in lettere maiuscole e minuscole, mentre per **State** e **City** i valori devono essere in lettere minuscole. I valori devono essere inclusi nell'output restituito dopo l'esecuzione del comando senza valori per **Country**, **State** e **City**. Se l'uso di maiuscole e minuscole non è corretto o si specifica un valore per **Country**, **State** o **City** che non è presente nell'output restituito dopo l'esecuzione del comando senza valori per queste proprietà, l'output restituito è vuoto.
