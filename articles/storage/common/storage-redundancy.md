---
title: Ridondanza dei dati in Archiviazione di Azure | Microsoft Docs
description: I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le opzioni di ridondanza includono archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7006e19616be51d79dc3e1319064d19024400bcc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789987"
---
# <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure

I dati nell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne la durabilità e la disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente.

Garantisce infine che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Altre informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure sono reperibili nel Contratto di servizio.

## <a name="choosing-a-redundancy-option"></a>Selezione di un'opzione di ridondanza

Quando si crea un account di archiviazione, è possibile selezionare una delle opzioni di ridondanza seguenti:

* [Archiviazione con ridondanza locale (LRS)](storage-redundancy-lrs.md)
* [Archiviazione con ridondanza della zona (ZRS).](storage-redundancy-zrs.md)
* [Archiviazione con ridondanza geografica (GRS)](storage-redundancy-grs.md)
* [Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).](storage-redundancy-grs.md#read-access-geo-redundant-storage)

La tabella seguente presenta una rapida panoramica dell'ambito di durabilità e disponibilità che ogni strategia di replica è in grado di offrire per un determinato tipo di evento (o un evento di impatto simile).

| Scenario                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | Archiviazione con ridondanza geografica                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Mancata disponibilità di un nodo in un data center                                                                 | Sì                             | Sì                              | Sì                                  | Sì                                  |
| Mancata disponibilità di un intero data center (di zona o non di zona)                                           | N.                              | Sì                              | Sì                                  | Sì                                  |
| Interruzione a livello di area                                                                                     | N.                              | N.                               | Sì                                  | Sì                                  |
| Accesso in lettura ai dati (in un'area remota con replica geografica) in caso di mancata disponibilità a livello di area | N.                              | N.                               | N.                                   | Sì                                  |
| Replica progettata per fornire una durabilità di \_\_ per gli oggetti nel corso di un determinato anno                                          | Almeno 99,999999999% (11 9) | Almeno 99,9999999999% (12 9) | Almeno 99,99999999999999% (16 9) | Almeno 99,99999999999999% (16 9) |
| Tipi di account di archiviazione supportati                                                                   | GPv2, GPv1, BLOB                | GPv2                             | GPv2, GPv1, BLOB                     | GPv2, GPv1, BLOB                     |
| Contratto di servizio relativo alla disponibilità per le richieste di lettura | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,99% (99,9% per livello di accesso sporadico) |
| Contratto di servizio relativo alla disponibilità per le richieste di scrittura | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) |

Per informazioni sui prezzi delle varie opzioni di ridondanza, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). 

Per informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure, vedere il [contratto di servizio di Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Archiviazione Premium supporta solo l'archiviazione con ridondanza locale.

## <a name="changing-replication-strategy"></a>Modifica della strategia di replica
È possibile modificare la strategia di replica dell'account di archiviazione usando il [portale di Azure](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), o uno del [client di Azure librerie](https://docs.microsoft.com/azure/index#pivot=sdkstools). La modifica del tipo di replica dell'account di archiviazione non comporta un tempo di inattività.

   > [!NOTE]
   > Attualmente non è possibile usare il portale o l'API per convertire l'account in archiviazione ZRS. Per convertire la replica dell'account in ZRS, vedere [Archiviazione con ridondanza della zona (ZRS).](storage-redundancy-zrs.md) per maggiori dettagli.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Vengono addebitati costi per la modifica della strategia di replica dell'account personale?
Dipende dal percorso di conversione. Dall'offerta di ridondanza più economica alla più costosa, sono disponibili le strategie di replica LRS, ZRS, GRS e RA-GRS. Se ad esempio si passa *da* LRS a un'altra strategia di replica, vengono addebitati costi aggiuntivi perché si esegue la conversione a un livello di ridondanza più sofisticato. Il passaggio *a* GRS o RA-GRS comporta un addebito per la larghezza di banda in uscita poiché i dati (nell'area primaria) vengono replicati nell'area secondaria remota. Questo addebito viene applicato una sola volta, durante la fase di configurazione iniziale. Dopo la copia dei dati non sono previsti altri costi di conversione. Verranno addebitati solo i costi per la replica di nuovi dati o di aggiornamenti ai dati esistenti. Per informazioni dettagliate sui costi addebitati per la larghezza di banda, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se si converte l'account di archiviazione da archiviazione con ridondanza geografica di archiviazione con ridondanza locale, non sono previsti costi aggiuntivi, ma i dati replicati vengono eliminati dalla località secondaria.

Se si converte l'account di archiviazione da RA-GRS in archiviazione con ridondanza geografica o archiviazione con ridondanza locale, tale account viene fatturato come RA-GRS per altri 30 giorni oltre la data in cui è stato convertito.

## <a name="see-also"></a>Vedere anche 

- [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per Archiviazione di Azure](storage-redundancy-lrs.md)
- [Archiviazione con ridondanza della zona (ZRS): applicazioni di Archiviazione di Azure a disponibilità elevata](storage-redundancy-zrs.md)
- [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md)
- [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md)
- [Progettazione di applicazioni a disponibilità elevata con archiviazione RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Archiviazione di Microsoft Azure la ridondanza dell'accesso in lettura e le opzioni di archiviazione con ridondanza geografica](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Paper SOSP - Archiviazione di Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
