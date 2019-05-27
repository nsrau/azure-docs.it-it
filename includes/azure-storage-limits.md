---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ee9b70827c19236287f81f66f4b9c6e1004a54fd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114526"
---
La tabella seguente descrive i limiti predefiniti per Azure per utilizzo generico v1, v2 e account di archiviazione Blob. Il *ingress* limite si riferisce a tutti i dati dalle richieste inviate a un account di archiviazione. Il *uscita* limite si riferisce a tutti i dati dalle risposte che vengono ricevute da un account di archiviazione.

| Resource | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account sia Standard che Premium | 250 |
| Capacità dell'account di archiviazione massimo | 2 PB di Stati Uniti ed Europa, 500 TB per tutte le altre aree, che include il Regno Unito |
| Numero massimo di contenitori blob, BLOB, condivisioni file, tabelle, code, entità o messaggi per ogni account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Massimo in ingresso<sup>1</sup> per ogni account di archiviazione (aree degli Stati Uniti) | 10 Gbps se RA-GRS/archiviazione con ridondanza geografica è abilitata, 20 Gbps per LRS/ZRS<sup>2</sup> |
| Massimo in ingresso<sup>1</sup> per ogni account di archiviazione (aree non degli Stati Uniti) | 5 Gbps se RA-GRS/archiviazione con ridondanza geografica è abilitata, 10 Gbps per LRS/ZRS<sup>2</sup> |
| Numero massimo in uscita per utilizzo generico v2 e account di archiviazione Blob (tutte le aree) | 50 Gbps |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico v1 (aree degli Stati Uniti) | 20 Gbps se RA-GRS/archiviazione con ridondanza geografica è abilitata, 30 Gbps per LRS/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico v1 (aree non degli Stati Uniti) | 10 Gbps se RA-GRS/archiviazione con ridondanza geografica è abilitata, 15 Gbps per LRS/ZRS<sup>2</sup> |

<sup>1</sup>gli account di archiviazione Standard di azure supportano limiti più elevati per l'ingresso dalla richiesta. Per richiedere un incremento dei limiti di archiviazione per il traffico in ingresso, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>Le opzioni di [replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: Archiviazione con ridondanza geografica. 
* **ZRS**: Archiviazione con ridondanza della zona.
* **LRS**: Archiviazione con ridondanza locale. 

> [!NOTE]
> È consigliabile usare un account di archiviazione per utilizzo generico v2 per la maggior parte degli scenari. È possibile aggiornare facilmente un utilizzo generico v1 o un account di archiviazione Blob di Azure a un account per utilizzo generico v2 senza tempi di inattività e senza la necessità di copiare i dati.
>
> Per altre informazioni sugli account di archiviazione di Azure, vedere [panoramica dell'account di archiviazione](../articles/storage/common/storage-account-overview.md). 

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Tutti gli account di archiviazione vengono eseguiti su una topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti in questo articolo, indipendentemente dal momento in cui sono stati creati. Per altre informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta).

