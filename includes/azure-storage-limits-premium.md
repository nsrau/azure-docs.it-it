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
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372714"
---
### <a name="premium-performance-block-blob-storage"></a>Archiviazione blob in blocchi prestazioni Premium

Un account di archiviazione premium prestazioni blocco blob è ottimizzato per le applicazioni che usano più piccole, in kilobyte intervallo, gli oggetti. È ideale per le applicazioni che richiedono l'archiviazione a bassa latenza coerente o frequenze di transazione molto elevato. Archiviazione blob in blocchi prestazioni Premium è progettato per soddisfare le tue applicazioni. Se si prevede di distribuire le applicazioni che richiedono centinaia di migliaia di richieste al secondo o petabyte di capacità di archiviazione, contattare Microsoft inviando una richiesta di supporto nel [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Archiviazione blob di pagine Premium prestazioni

Prestazioni Premium, account di archiviazione per utilizzo generico v1 o v2 hanno gli obiettivi di scalabilità seguente:

| Capacità account totale                            | Larghezza di banda totale per un account di archiviazione con ridondanza locale                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacità disco: 35 TB <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso<sup>1</sup> e in uscita<sup>2</sup> |

<sup>1</sup> Tutti i dati (richieste) inviati a un account di archiviazione

<sup>2</sup> Tutti i dati (risposte) ricevuti da un account di archiviazione

Se si usano account di archiviazione di prestazioni premium per dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, si potrebbe voler eseguire la migrazione a managed disks. Se non si vuole eseguire la migrazione a Managed Disks, compilare l'applicazione per l'uso di più account di archiviazione. Quindi partizionare i dati tra tali account di archiviazione. Ad esempio, se si desidera collegare dischi da 51 TB tra più VM, distribuirli in due account di archiviazione. Il limite per un account di Archiviazione Premium singolo è 35 TB. Assicurarsi che un account di archiviazione premium singolo prestazioni non ha mai più di 35 TB di dischi con provisioning.