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
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305296"
---
**Conservazione e volume di raccolta dati** 

| Livello | Limitare al giorno | Conservazione dei dati | Commento |
|:---|:---|:---|:---|
| Piano tariffario corrente Per GB<br>(introdotta aprile 2018) | Nessun limite | 30 - 730 giorni | Conservazione dei dati oltre i 31 giorni è disponibile per gli addebiti aggiuntivi. Altre informazioni sui prezzi di monitoraggio di Azure. |
| Livelli gratuiti legacy<br>(introdotta aprile 2016) | 500 MB | 7 giorni | Quando l'area di lavoro raggiunge i 500 MB al limite di giorni, l'inserimento di dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC. Si noti che i dati raccolti dal Centro sicurezza di Azure non sono incluso in questo 500 MB al limite di giorni e continueranno a essere raccolti superano questo limite.  |
| Livello di autonoma per ogni GB legacy<br>(introdotta aprile 2016) | Nessun limite | 30 a 730 giorni | Conservazione dei dati oltre i 31 giorni è disponibile per gli addebiti aggiuntivi. Altre informazioni sui prezzi di monitoraggio di Azure. |
| Legacy Per nodo (OMS)<br>(introdotta aprile 2016) | Nessun limite | 30 a 730 giorni | Conservazione dei dati oltre i 31 giorni è disponibile per gli addebiti aggiuntivi. Altre informazioni sui prezzi di monitoraggio di Azure. |
| Livello Standard legacy | Nessun limite | 30 giorni  | Conservazione non può essere modificata. |
| Livello Premium legacy | Nessun limite | 365 giorni  | Conservazione non può essere modificata. |

**Numero di aree di lavoro per ogni sottoscrizione.**

| Piano tariffario    | Limite dell'area di lavoro | Commenti
|:---|:---|:---|
| Livello gratuito  | 10 | Non è possibile aumentare questo limite. |
| Tutti gli altri livelli | Nessun limite | Si è limitata dal numero di risorse all'interno di un gruppo di risorse e il numero di gruppi di risorse per ogni sottoscrizione. |

**Portale di Azure**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Numero massimo di record restituito da una query di log | 10,000 | Ridurre i risultati con ambito query, l'intervallo di tempo e i filtri nella query. |


**API di raccolta dati**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Dimensioni massime per un singolo post | 30 MB | Dividere i volumi più grandi in più post. |
| Dimensioni massime per i valori dei campi  | 32 KB | I campi con una lunghezza superiore a 32 KB vengono troncati. |

**API di ricerca**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Numero massimo di record restituito per i dati non aggregate | 5\.000 | |
| Numero massimo di record per i dati aggregati | 500,000 | I dati aggregati sono una ricerca che include il `summarize` comando. |
| Dimensione massima dei dati restituiti | 64.000.000 di byte (~ 61 MiB)| |
| Tempo di esecuzione massimo per le query | 10 minuti | Visualizzare [timeout](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) per informazioni dettagliate.  |
| Frequenza massima di richieste | 200 richieste per ogni 30 secondi per ogni indirizzo IP client o utente AAD | Visualizzare [limiti di velocità](https://dev.loganalytics.io/documentation/Using-the-API/Limits) per informazioni dettagliate. |

**Limiti dell'area di lavoro generale**

| Category | Limiti | Commenti |
|:---|:---|:---|
| Numero massimo di colonne in una tabella         | 500 | |
| Numero massimo di caratteri per nome di colonna | 500 | |
| Aree in base alla capacità | Stati Uniti centro-occidentali | È attualmente possibile creare una nuova area di lavoro in questa area perché è al limite di capacità temporanea. Questo limite è pianificato per essere risolti dalla fine del mese di settembre 2019. |
| Esportazione dei dati | Attualmente non disponibile | Usare funzioni di Azure o App per la logica di aggregazione ed esportare i dati. | 

>[!NOTE]
>A seconda di quanto tempo è stato usato Log Analitica, è necessario l'accesso per i livelli di prezzi precedente. Altre informazioni sulle [legacy di Log Analitica piani tariffari](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 