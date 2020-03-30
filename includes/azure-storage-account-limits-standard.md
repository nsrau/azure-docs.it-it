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
La tabella seguente descrive i limiti predefiniti per gli account di archiviazione abilitati per gli account di archiviazione abilitati per Data Lake Storage Gen2 di Azure.The following table describes default limits for Azure general-purpose v1, v2, Blob storage, block blob storage, and Data Lake Storage Gen2 enabled storage accounts. Il limite *in ingresso* si riferisce a tutti i dati inviati a un account di archiviazione. Il limite *in uscita* si riferisce a tutti i dati ricevuti da un account di archiviazione.

| Risorsa | Limite |
| --- | --- |
| Numero di account di archiviazione per area per sottoscrizione, inclusi account di archiviazione standard, premium e Data Lake Storage Gen2 abilitati. <sup>3 (COM del</sup> nome | 250 |
| Capacità massima dell'account di archiviazioneMaximum storage account capacity | 5 PiB <sup>1</sup>|
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazioneMaximum number of blob containers, blobs, file shares, tables, queues, entities, or messages per storage account | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Ingresso massimo 1 per account di archiviazione (Stati Uniti, Europa aree)Maximum ingress<sup>1</sup> per storage account (US, Europe regions) | 25 Gbps (25 Gbps) |
| Ingresso<sup>massimo 1</sup> per account di archiviazione (aree diverse da Stati Uniti ed Europa)Maximum ingress 1 per storage account (regions other than US and Europe) | 5 Gbps se RA-GRS/GRS è abilitato, 10 Gbps per LRS/<sup>2</sup> |
| Massimo uscita per gli account di archiviazione generici v2 e BLOB (tutte le aree)Maximum egress for general-purpose v2 and Blob storage accounts (all regions) | 50 Gbps |
| Massimo uscita per gli account di archiviazione v1 generici (aree USA)Maximum egress for general-purpose v1 storage accounts (US regions) | 20 Gbps se RA-GRS/GRS è abilitato, 30 Gbps per LRS /<sup>2</sup> |
| Massimo uscita per gli account di archiviazione v1 generici (aree non statunitensi)Maximum egress for general-purpose v1 storage accounts (non-US regions) | 10 Gbps se RA-GRS/GRS è abilitato, 15 Gbps per LRS /<sup>2</sup> |
| Numero massimo di regole di rete virtuale per account di archiviazioneMaximum number of virtual network rules per storage account | 200 |
| Numero massimo di regole degli indirizzi IP per account di archiviazioneMaximum number of IP address rules per storage account | 200 |

<sup>1</sup> Gli account standard di Archiviazione di Azure supportano limiti di capacità più elevati e limiti superiori per l'ingresso su richiesta.1 Azure Storage standard accounts support higher capacity limits and higher limits for ingress by request. Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se l'account di archiviazione ha l'accesso in lettura abilitato con archiviazione con ridondanza geografica (RA-GRS) o archiviazione con ridondanza geografica (RA-G-RS), le destinazioni in uscita per la posizione secondaria sono identiche a quelle della posizione primaria. [Le](https://docs.microsoft.com/azure/storage/common/storage-redundancy) opzioni di replica di Archiviazione di Azure includono:Azure Storage replication options include:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) è un set di funzionalità dedicate all'analisi dei Big Data, basate sull'archiviazione BLOB di Azure.3 Azure Data Lake Storage Gen2 is a set of capabilities dedicated to big data analytics, built on Azure Blob storage. Azure Storage and blob storage limitations apply to Data Lake Storage Gen2.

> [!NOTE]
> Microsoft consiglia di usare un account di archiviazione versione 2 generico per la maggior parte degli scenari. È possibile aggiornare facilmente una versione 1 generica o un account di archiviazione BLOB di Azure a un account v2 generico senza tempi di inattività e senza la necessità di copiare i dati. Per altre informazioni, vedere [Eseguire l'aggiornamento a un account di archiviazione v2 generico.](../articles/storage/common/storage-account-upgrade.md)

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi dei volumi, vedere Prezzi di Archiviazione di Azure.For information on [volume pricing,](https://azure.microsoft.com/pricing/details/storage/)see Azure Storage pricing .

Tutti gli account di archiviazione vengono eseguiti in una topologia di rete flat indipendentemente da quando sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
