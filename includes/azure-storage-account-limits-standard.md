---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/20/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 72afa8ebea4cb9bf07cb3ce56583cbdf1849be9e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778167"
---
La tabella seguente descrive i limiti predefiniti per Azure per gli account di archiviazione di Azure per utilizzo generico V1, V2, archiviazione BLOB e archiviazione BLOB in blocchi. Il limite *in ingresso* indica tutti i dati inviati a un account di archiviazione. Il limite *in uscita* indica tutti i dati ricevuti da un account di archiviazione.

| Risorsa | Limite |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account di archiviazione Standard e Premium.| 250 |
| Capacità massima dell'account di archiviazione | 5 PiB <sup>1</sup>|
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima delle richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (per le aree Stati Uniti ed Europa) | 25 Gbps |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (per le aree diverse da Stati Uniti ed Europa) | 5 Gbps se è abilitata l'archiviazione RA-GRS/GRS, 10 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v2 e BLOB (per tutte le aree) | 50 Gbps |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (per le aree degli Stati Uniti) | 20 Gbps se è abilitata l'archiviazione RA-GRS/GRS, 30 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (per le aree diverse dagli Stati Uniti) | 10 Gbps se è abilitata l'archiviazione RA-GRS/GRS, 15 Gbps per LRS/ZRS<sup>2</sup> |
| Numero massimo di regole di rete virtuale per account di archiviazione | 200 |
| Numero massimo di regole di indirizzo IP per account di archiviazione | 200 |

<sup>1</sup> Gli account standard di Archiviazione di Azure supportano limiti di capacità superiori e limiti più elevati per il traffico in ingresso in base alle richieste. Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se per l'account di archiviazione è abilitata l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS), le destinazioni in uscita per la posizione secondaria sono identiche a quelle della posizione primaria. Le opzioni di [replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) è un set di funzionalità dedicate all'analisi dei Big Data e integrate in Archiviazione BLOB di Azure.

> [!NOTE]
> Per la maggior parte degli scenari è consigliabile usare un account di archiviazione per utilizzo generico v2. È possibile eseguire facilmente l'aggiornamento di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB di Azure a un account per utilizzo generico v2, senza tempi di inattività e senza la necessità copiare i dati. Per altre informazioni, [Eseguire l'aggiornamento alla versione 2 di un account di archiviazione per uso generico](../articles/storage/common/storage-account-upgrade.md).

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. È quindi possibile partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi in base al volume, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Tutti gli account di archiviazione vengono eseguiti in una topologia di rete flat indipendentemente da quando sono stati creati. Per altre informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
