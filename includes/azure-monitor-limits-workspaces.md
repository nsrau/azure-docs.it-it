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
ms.openlocfilehash: 0f55f3ce4856e7b6eccf665faaa1b1fa45b84b58
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72302318"
---
**Volume e conservazione della raccolta dati** 

| Livello | Limite al giorno | Conservazione dei dati | Comment |
|:---|:---|:---|:---|
| Piano tariffario per GB corrente<br>(introdotto il 2018 aprile) | Nessun limite | 30-730 giorni | La conservazione dei dati oltre i 31 giorni è disponibile per addebiti aggiuntivi. Scopri di più sui prezzi di monitoraggio di Azure. |
| Livelli gratuiti legacy<br>(introdotto il 2016 aprile) | 500 MB | 7 giorni | Quando l'area di lavoro raggiunge il limite di 500 MB al giorno, l'inserimento dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC. Si noti che i dati raccolti dal centro sicurezza di Azure non sono inclusi in questo limite di 500 MB al giorno e continueranno a essere raccolti oltre questo limite.  |
| Livello legacy autonomo per GB<br>(introdotto il 2016 aprile) | Nessun limite | da 30 a 730 giorni | La conservazione dei dati oltre i 31 giorni è disponibile per addebiti aggiuntivi. Scopri di più sui prezzi di monitoraggio di Azure. |
| Legacy per nodo (OMS)<br>(introdotto il 2016 aprile) | Nessun limite | da 30 a 730 giorni | La conservazione dei dati oltre i 31 giorni è disponibile per addebiti aggiuntivi. Scopri di più sui prezzi di monitoraggio di Azure. |
| Livello standard legacy | Nessun limite | 30 giorni  | Non è possibile modificare la conservazione |
| Livello Premium legacy | Nessun limite | 365 giorni  | Non è possibile modificare la conservazione |

**Numero di aree di lavoro per sottoscrizione.**

| Piano tariffario    | Limite area di lavoro | Commenti
|:---|:---|:---|
| Livello gratuito  | 10 | Non è possibile aumentare questo limite. |
| Tutti gli altri livelli | Nessun limite | Il numero di risorse all'interno di un gruppo di risorse e il numero di gruppi di risorse per sottoscrizione sono limitati. |

**Portale di Azure**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Numero massimo di record restituiti da una query di log | 10,000 | Ridurre i risultati utilizzando l'ambito della query, l'intervallo di tempo e i filtri nella query. |


**API di raccolta dati**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Dimensioni massime per un singolo post | 30 MB | Suddividere i volumi più grandi in più post. |
| Dimensioni massime per i valori dei campi  | 32 KB | I campi con una lunghezza superiore a 32 KB vengono troncati. |

**API di ricerca**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Numero massimo di record restituiti in una singola query | 500,000 | |
| Dimensioni massime dei dati restituiti | 64 milioni byte (~ 61 MiB)| |
| Tempo di esecuzione massimo query | 10 minuti | Per informazioni dettagliate, vedere [timeout](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Frequenza massima richieste | 200 richieste per 30 secondi per utente AAD o indirizzo IP client | Per informazioni dettagliate, vedere [limiti di velocità](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Limiti generali dell'area di lavoro**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Numero massimo di colonne in una tabella         | 500 | |
| Numero massimo di caratteri per il nome della colonna | 500 | |
| Aree alla capacità | Stati Uniti centro-occidentali | Attualmente non è possibile creare una nuova area di lavoro in questa area perché si trova al limite di capacità temporanea. Questo limite è pianificato per essere risolto entro la fine di novembre 2019. |
| Esportazione dati | Attualmente non disponibile | Usare funzioni di Azure o app per la logica per aggregare ed esportare dati. | 

**Velocità del volume di inserimento dati**


Monitoraggio di Azure è un servizio dati su larga scala che serve migliaia di clienti che inviano terabyte di dati ogni mese a un ritmo crescente. Il limite di velocità del volume di inserimento predefinito per i dati inviati dalle risorse di Azure con [le impostazioni di diagnostica](../articles/azure-monitor/platform/diagnostic-settings.md) è di circa **6 GB/min** per area di lavoro. Si tratta di un valore approssimativo, poiché la dimensione effettiva può variare tra i tipi di dati a seconda della lunghezza del log e del relativo rapporto di compressione. Questo limite non si applica ai dati inviati dagli agenti o dall' [API dell'agente di raccolta dati](../articles/azure-monitor/platform/data-collector-api.md).

Se si inviano dati a una velocità superiore a una singola area di lavoro, alcuni dati vengono eliminati e un evento viene inviato alla tabella delle *operazioni* nell'area di lavoro ogni 6 ore mentre la soglia continua a essere superata. Se il volume di inserimento continua a superare il limite di velocità o se si prevede di raggiungerlo presto, è possibile richiedere un aumento dell'area di lavoro aprendo una richiesta di supporto.
 
Per ricevere una notifica su tale evento nell'area di lavoro, creare una [regola di avviso di log](../articles/azure-monitor/platform/alerts-log.md) usando la query seguente con la logica di avviso base su numero di risultati maggiore di zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>A seconda di quanto tempo si utilizza Log Analytics, è possibile che si disponga dell'accesso ai piani tariffari legacy. Scopri di più sui [log Analytics piani tariffari legacy](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 