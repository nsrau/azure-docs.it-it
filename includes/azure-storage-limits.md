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
ms.openlocfilehash: 1dd11e22361e25721effe2ed919f175d9cb1b9e4
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249621"
---
La tabella seguente descrive i limiti predefiniti per Archiviazione di Azure. Il limite *in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. Il limite *in uscita* indica tutti i dati (risposte) ricevuti da un account di archiviazione.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account sia Standard che Premium | 250 |
| Capacità massima dell'account di archiviazione | 2 PB per Stati Uniti ed Europa, 500 TB per tutte le altre aree incluso il Regno Unito |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 20 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree non degli Stati Uniti) | 5 Gbps con archiviazione RA-GRS/GRS abilitata, 10 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v2 e BLOB (tutte le aree) | 50 Gbps |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS abilitata, 30 Gbps per LRS/ZRS <sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (aree non degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 15 Gbps per LRS/ZRS <sup>2</sup> |

<sup>1</sup> Gli account di archiviazione di Azure supportano limiti più elevati per il traffico in ingresso e per le operazioni di I/O al secondo in base alle richieste. Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>Le opzioni di [replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: Archiviazione con ridondanza geografica. 
* **ZRS**: Archiviazione con ridondanza della zona.
* **LRS**: Archiviazione con ridondanza locale. 

> [!NOTE]
> Per la maggior parte degli scenari è consigliabile usare un account di archiviazione per utilizzo generico v2. È possibile eseguire facilmente l'aggiornamento di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB a un account per utilizzo generico v2, senza tempi di inattività e senza la necessità copiare i dati.
>
> Per altre informazioni sugli account di Archiviazione di Azure, vedere [Panoramica dell'account di archiviazione di Azure](../articles/storage/common/storage-account-overview.md). 

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi in base al volume, vedere la pagina relativa ai [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

Tutti gli account di archiviazione vengono eseguiti su una topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti in questo articolo, indipendentemente dal momento in cui sono stati creati. Per altre informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta).

