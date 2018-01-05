---
title: Visualizzare le latenze relative alle aree di Azure da percorsi specifici | Documenti Microsoft
description: Informazioni su come visualizzare le latenze relative provider Internet per le aree di Azure da percorsi specifici.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Latenza relativa visualizzazione di aree di Azure da percorsi specifici

In questa esercitazione, imparare a usare Azure [Watcher di rete](network-watcher-monitoring-overview.md) utili per decidere quale area di Azure per distribuire l'applicazione o il servizio del servizio, in base all'utente demografico. Inoltre, è possibile utilizzare per consentire di valutare le connessioni ai provider di servizi di Azure.  
        
## <a name="create-a-network-watcher"></a>creare un controllo di rete

Se dispone già di un watcher di rete in almeno Azure [area](https://azure.microsoft.com/regions), è possibile ignorare le attività in questa sezione. Creare un gruppo di risorse per il controllo di rete. In questo esempio viene creato il gruppo di risorse nell'area Stati Uniti orientali, ma è possibile creare il gruppo di risorse in qualsiasi area di Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Creare un controllo di rete. È necessario disporre di un controllo di rete creato in almeno un'area di Azure. In questo esempio viene creato un controllo di rete nell'area orientale Microsoft Azure.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Confrontare le latenze di rete relativo a una singola regione di Azure da una posizione specifica

Valutare i provider di servizi o risolvere i problemi relativi a un utente di un problema di creazione report come "il sito è risultata lento," da una posizione specifica all'area di azure in cui viene distribuito un servizio. Ad esempio, il comando seguente restituisce le latenze medie provider del servizio Internet relative tra lo stato di Washington, Stati Uniti e l'area occidentale ci 2 Azure tra dicembre 13-15, 2017:

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
> L'area in cui che è specificato nel comando precedente non deve necessariamente coincidere con l'area specificata quando è stato recuperato il watcher di rete. Il comando precedente semplicemente è necessario specificare un controllo di rete esistente. Il watcher di rete può essere in qualsiasi area. Se si specificano valori per `-Country` e `-State`, devono essere validi. I valori tra maiuscole e minuscole. Sono disponibili dati per un numero limitato di paesi, stati e città. Eseguire i comandi in [visualizzare disponibili paesi, stati, città e provider](#view-available) per visualizzare un elenco di paesi disponibili, città e gli stati da utilizzare con il comando precedente. 

> [!WARNING]
> È necessario specificare una data successiva 14 novembre 2017 per `-StartTime` e `-EndTime`. Specificare una data prima 14 novembre 2017 non restituisce alcun dato. 

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

Nell'output restituito, il valore per **punteggio** è la latenza relativa in aree e i provider. Un punteggio pari a 1 è la peggiore latenza (massima), mentre 100 è la latenza più bassa. Le latenze relative vengono calcolata la media per il giorno. Nell'esempio precedente, mentre è ovvio che le latenze sono gli stessi entrambi i giorni e che è presente una piccola differenza tra la latenza dei due provider, è anche ovvio che le latenze per entrambi i provider sono basse sulla scala 1-100. Mentre è previsto, poiché lo stato di Washington, Stati Uniti è fisicamente vicino area occidentale ci 2 Azure, talvolta risultati non sono come previsto. Maggiore è l'intervallo di date specificato, maggiore è possibile latenza media del tempo.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Confrontare le latenze di rete relativo in aree geografiche di Azure da una posizione specifica

Se, invece di specificare le latenze relative tra una posizione specifica e una regione di Azure specifico utilizzando `-Location`, si desiderava determinare le latenze relative a tutte le aree di Azure da un percorso fisico specifico, è possibile farlo troppo. Ad esempio, il comando seguente consente di valutare quale area di azure per distribuire un servizio in se gli utenti primari sono utenti di Comcast che si trovano nello stato di Washington:

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
A differenza di quando si specifica un'unica posizione, se non specificare un percorso o specificare più percorsi, ad esempio "Occidentale US2", "Stati Uniti occidentali", è necessario specificare un provider di servizi Internet quando si esegue il comando. 

## <a name="view-available"></a>Visualizzazione disponibili paesi, stati, città e provider

Sono disponibili dati per le città, paesi, stati e provider di servizi Internet specifico. Per visualizzare un elenco di tutti i provider di servizi Internet disponibili, paesi, stati e città, che è possibile visualizzare i dati, immettere il comando seguente:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Dati sono disponibili solo per i paesi, stati e città restituita dal comando precedente. Il comando precedente è necessario specificare un controllo di rete esistente. Nell'esempio viene specificato il *NetworkWatcher_eastus* watcher di rete in un gruppo di risorse denominato *NetworkWatcherRG*, ma è possibile specificare qualsiasi watcher di rete esistente. Se non si dispone di un controllo di rete esistente, crearne uno, completare le attività di [creare un controllo di rete](#create-a-network-watcher). 

Dopo aver eseguito il comando precedente, è possibile filtrare l'output restituito specificando i valori validi per **paese**, **stato**, e **Città**, se necessario.  Per visualizzare l'elenco dei provider di servizi Internet disponibile in Seattle, Washington, Stati Uniti, ad esempio, immettere il comando seguente:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Il valore specificato per **paese** devono essere lettere maiuscole e minuscole. I valori specificati per **stato** e **Città** devono essere minuscole. I valori devono essere inclusi nell'output restituito dopo l'esecuzione del comando senza valori per **paese**, **stato**, e **Città**. Se è specificare il case non corretto o specificare un valore per **paese**, **stato**, o **Città** che non è presente nell'output restituito dopo l'esecuzione del comando senza valori per questi proprietà, l'output restituito è vuoto.
