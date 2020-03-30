---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334806"
---
**Volume e conservazione della raccolta dei dati** 

| Livello | Limite al giorno | Conservazione dei dati | Comment |
|:---|:---|:---|:---|
| Piano tariffario Per GB corrente<br>(introdotto nell'aprile 2018) | Nessun limite | 30 - 730 giorni | La conservazione dei dati oltre i 31 giorni è disponibile per costi aggiuntivi. Altre informazioni sui prezzi di Monitoraggio di Azure. |
| Livelli gratuiti legacyLegacy Free tiers<br>(introdotto nell'aprile 2016) | 500 MB | 7 giorni | Quando l'area di lavoro raggiunge il limite di 500 MB al giorno, l'inserimento dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC. Si noti che i dati raccolti dal Centro sicurezza di Azure non sono inclusi in questo limite di 500 MB al giorno e continueranno a essere raccolti al di sopra di questo limite.  |
| Livello autonomo legacy per GB<br>(introdotto nell'aprile 2016) | Nessun limite | Da 30 a 730 giorni | La conservazione dei dati oltre i 31 giorni è disponibile per costi aggiuntivi. Altre informazioni sui prezzi di Monitoraggio di Azure. |
| Legacy Per Nodo (OMS)<br>(introdotto nell'aprile 2016) | Nessun limite | Da 30 a 730 giorni | La conservazione dei dati oltre i 31 giorni è disponibile per costi aggiuntivi. Altre informazioni sui prezzi di Monitoraggio di Azure. |
| Legacy Livello StandardLegacy Standard tier | Nessun limite | 30 giorni  | La conservazione non può essere regolata |
| Livello Premium legacy | Nessun limite | 365 giorni  | La conservazione non può essere regolata |

**Numero di aree di lavoro per sottoscrizione.**

| Piano tariffario    | Limite area di lavoro | Commenti
|:---|:---|:---|
| Livello gratuito  | 10 | Questo limite non può essere aumentato. |
| Tutti gli altri livelli | Nessun limite | Il numero di risorse all'interno di un gruppo di risorse e il numero di gruppi di risorse per sottoscrizione sono limitati. |

**Portale di Azure**

| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di record restituiti da una query di logMaximum records returned by a log query | 10,000 | Ridurre i risultati utilizzando l'ambito della query, l'intervallo di tempo e i filtri nella query. |


**API dell'agente di raccolta dati**

| Category | Limite | Commenti |
|:---|:---|:---|
| Dimensione massima per un singolo post | 30 MB | Suddividere volumi più grandi in più post. |
| Dimensione massima per i valori dei campi  | 32 KB | I campi con una lunghezza superiore a 32 KB vengono troncati. |

**API di ricerca**

| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di record restituiti in una singola query | 500.000 | |
| Dimensione massima dei dati restituiti | 64.000.000 byte (61 MiB)| |
| Tempo massimo di esecuzione delle query | 10 minuti | Per informazioni [dettagliate,](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) vedere Timeout.  |
| Frequenza massima richieste | 200 richieste per 30 secondi per indirizzo IP dell'utente o del client AAD | Per informazioni dettagliate, vedere [Limiti di tariffa.](https://dev.loganalytics.io/documentation/Using-the-API/Limits) |

**Limiti generali dell'area di lavoro**

| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di colonne in una tabella         | 500 | |
| Numero massimo di caratteri per il nome della colonna | 500 | |
| Esportazione dati | Attualmente non disponibile | Usare la funzione di Azure o l'app per la logica per aggregare ed esportare i dati. | 

**Frequenza del volume di inserimento datiData ingestion volume rate**


Monitoraggio di Azure è un servizio dati su larga scala che serve migliaia di clienti che inviano terabyte di dati ogni mese a un ritmo crescente. Il limite di frequenza del volume di inserimento predefinito per i dati inviati dalle risorse di Azure usando [le impostazioni](../articles/azure-monitor/platform/diagnostic-settings.md) di diagnostica è di circa **6 GB/min** per ogni area di lavoro. Si tratta di un valore approssimativo poiché le dimensioni effettive possono variare tra i tipi di dati a seconda della lunghezza del log e del relativo rapporto di compressione. Questo limite non si applica ai dati inviati dagli agenti o [dall'API dell'agente](../articles/azure-monitor/platform/data-collector-api.md)di raccolta dati.

Se si inviano dati a una frequenza più elevata a una singola area di lavoro, alcuni dati vengono eliminati e viene inviato un evento alla tabella *Operazione* nell'area di lavoro ogni 6 ore mentre la soglia continua a essere superata. Se il volume di inserimento continua a superare il limite di frequenza o si prevede di raggiungerlo a breve termine, è possibile richiedere un aumento all'area di lavoro aprendo una richiesta di supporto.
 
Per ricevere una notifica su un evento di questo tipo nell'area di lavoro, creare una regola di [avviso del log](../articles/azure-monitor/platform/alerts-log.md) utilizzando la query seguente con la logica di avviso basata sul numero di risultati grattugiare su zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>A seconda di quanto tempo si usa Log Analytics, potresti avere accesso ai piani tariffari legacy. Ulteriori informazioni sui [piani tariffari legacy](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)di Log Analytics . 