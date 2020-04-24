---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334958"
---
La tabella seguente descrive i limiti predefiniti per Azure per utilizzo generico V1, V2, archiviazione BLOB, archiviazione BLOB in blocchi e account di archiviazione Data Lake Storage Gen2 abilitati. Il limite di *ingresso* si riferisce a tutti i dati inviati a un account di archiviazione. Il limite di *uscita* si riferisce a tutti i dati ricevuti da un account di archiviazione.

| Risorsa | Limite |
| --- | --- |
| Numero di account di archiviazione per area per sottoscrizione, inclusi gli account di archiviazione standard, Premium e Data Lake Storage Gen2 abilitati. <sup>3</sup> | 250 |
| Capacità massima dell'account di archiviazione | 5 PiB <sup>1</sup>|
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Ingresso massimo<sup>1</sup> per account di archiviazione (aree Stati Uniti, Europa) | 25 Gbps |
| Ingresso massimo<sup>1</sup> per account di archiviazione (aree diverse da Stati Uniti ed Europa) | 5 Gbps se RA-GRS/GRS è abilitata, 10 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione BLOB e V2 per utilizzo generico (tutte le aree) | 50 Gbps |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico V1 (aree degli Stati Uniti) | 20 Gbps se RA-GRS/GRS è abilitata, 30 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico V1 (aree non statunitensi) | 10 Gbps se RA-GRS/GRS è abilitato, 15 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo di regole di rete virtuale per account di archiviazione | 200 |
| Numero massimo di regole di indirizzi IP per account di archiviazione | 200 |

<sup>1</sup> gli account standard di archiviazione di Azure supportano limiti di capacità più elevati e limiti più elevati per il traffico in ingresso per richiesta. Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> se per l'account di archiviazione è abilitato l'accesso in lettura con archiviazione con ridondanza geografica (RA-GRS) o archiviazione con ridondanza della zona geografica (RA-GZRS), le destinazioni in uscita per la posizione secondaria sono identiche a quelle della posizione primaria. Le opzioni di [replica di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) è un set di funzionalità dedicate a Big Data Analytics, basate sull'archiviazione BLOB di Azure. Le limitazioni dell'archiviazione BLOB e di archiviazione di Azure si applicano a Data Lake Storage Gen2.

> [!NOTE]
> Microsoft consiglia di utilizzare un account di archiviazione per utilizzo generico V2 per la maggior parte degli scenari. È possibile aggiornare facilmente un account di archiviazione BLOB di Azure o di uso generico V1 a un account per utilizzo generico V2 senza tempi di inattività e senza la necessità di copiare i dati. Per altre informazioni, vedere [eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2](../articles/storage/common/storage-account-upgrade.md).

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi dei volumi, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Tutti gli account di archiviazione vengono eseguiti in una topologia di rete Flat indipendentemente dal momento in cui sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
