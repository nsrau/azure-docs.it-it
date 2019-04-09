---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1cb353d22e39447d7aeb723b5506ac0209cf74d8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287103"
---
### <a name="premium-performance-block-blob-storage"></a>Archiviazione blob in blocchi prestazioni Premium

Un account di archiviazione premium prestazioni blocco blob è ottimizzato per le applicazioni che usano più piccole, in kilobyte intervallo, gli oggetti. È ideale per le applicazioni che richiedono una frequenza elevata delle transazioni o coerenti con l'archiviazione a bassa latenza. Archiviazione blob in blocchi prestazioni Premium è progettato per soddisfare le tue applicazioni. Se si prevede di distribuire le applicazioni che richiedono centinaia di migliaia di richieste al secondo o petabyte di capacità di archiviazione, contattare Microsoft inviando una richiesta di supporto nel [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage-preview"></a>Prestazioni Premium FileStorage (anteprima)

File Premium usano un account di archiviazione univoco **FileStorage (anteprima)**. Questo tipo di account è progettato per carichi di lavoro con numero elevato di IOPS, velocità effettiva elevata con bassa latenza coerente. Archiviazione Premium di file si adatta alle dimensioni della condivisione con provisioning.

|Area  |Destinazione  |
|---------|---------|
|Dimensioni massime     |5 TiB (anteprima pubblica), 100 TiB (anteprima pubblica limitata)     |
|Condivisioni   |Illimitato  |
|IOPS     |100.000 (anteprima pubblica limitata)    |
|Larghezza di banda|5 GB/s     |

 File premium condividono obiettivi di scalabilità, vedere la [file Premium scalare destinazioni](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) sezione.

### <a name="premium-performance-page-blob-storage"></a>Archiviazione blob di pagine Premium prestazioni

Prestazioni Premium, utilizzo generico v1 o v2 gli account di archiviazione sono gli obiettivi di scalabilità seguente:

| Capacità account totale                            | Larghezza di banda totale per un account di archiviazione con ridondanza locale                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacità disco: 35 TB <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso<sup>1</sup> e in uscita<sup>2</sup> |

<sup>1</sup> Tutti i dati (richieste) inviati a un account di archiviazione

<sup>2</sup> Tutti i dati (risposte) ricevuti da un account di archiviazione

Se si usano account di archiviazione di prestazioni premium per dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, si potrebbe voler eseguire la migrazione a managed disks. Se non si vuole eseguire la migrazione a Managed Disks, compilare l'applicazione per l'uso di più account di archiviazione. Quindi partizionare i dati tra tali account di archiviazione. Ad esempio, se si desidera collegare dischi da 51 TB tra più VM, distribuirli in due account di archiviazione. Il limite per un account di Archiviazione Premium singolo è 35 TB. Assicurarsi che un account di archiviazione premium singolo prestazioni non ha mai più di 35 TB di dischi con provisioning.