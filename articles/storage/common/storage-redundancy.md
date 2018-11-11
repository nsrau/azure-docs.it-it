---
title: Replica dei dati in Archiviazione di Azure | Documentazione Microsoft
description: I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le opzioni di replica includono archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/08/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 7afbdaba46674b69aa601355e80160e7c72ff373
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240971"
---
# <a name="azure-storage-replication"></a>Replica di Archiviazione di Azure

I dati nell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne la durabilità e la disponibilità elevata. La replica di Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, ad esempio errori hardware temporanei, interruzioni della rete o dell'alimentazione, forti calamità naturali e così via. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area e persino tra aree.

Garantisce infine che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Altre informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure sono reperibili nel Contratto di servizio.

## <a name="choosing-a-replication-option"></a>Scelta di un'opzione di replica

Quando si crea un account di archiviazione, è possibile selezionare una delle opzioni di replica seguenti:

* [Archiviazione con ridondanza locale (LRS)](storage-redundancy-lrs.md)
* [Archiviazione con ridondanza della zona (ZRS).](storage-redundancy-zrs.md)
* [Archiviazione con ridondanza geografica (GRS)](storage-redundancy-grs.md)
* [Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).](storage-redundancy-grs.md#read-access-geo-redundant-storage)

La tabella seguente presenta una rapida panoramica dell'ambito di durabilità e disponibilità che ogni strategia di replica è in grado di offrire per un determinato tipo di evento (o un evento di impatto simile).

| Scenario                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | Archiviazione con ridondanza geografica                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Mancata disponibilità di un nodo in un data center                                                                 | Yes                             | Sì                              | Sì                                  | Yes                                  |
| Mancata disponibilità di un intero data center (di zona o non di zona)                                           | No                               | Yes                              | Sì                                  | Yes                                  |
| Interruzione a livello di area                                                                                     | No                               | No                                | Yes                                  | Yes                                  |
| Accesso in lettura ai dati (in un'area remota con replica geografica) in caso di mancata disponibilità a livello di area | No                               | No                                | No                                    | Yes                                  |
| Replica progettata per fornire una durabilità di \_\_ per gli oggetti nel corso di un determinato anno                                          | Almeno 99,999999999% (11 9) | Almeno 99,9999999999% (12 9) | Almeno 99,99999999999999% (16 9) | Almeno 99,99999999999999% (16 9) |
| Tipi di account di archiviazione supportati                                                                   | GPv2, GPv1, BLOB                | GPv2                             | GPv2, GPv1, BLOB                     | GPv2, GPv1, BLOB                     |
| Contratto di servizio relativo alla disponibilità per le richieste di lettura | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,99% (99,9% per livello di accesso sporadico) |
| Contratto di servizio relativo alla disponibilità per le richieste di scrittura | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) |

Per informazioni sui prezzi delle varie opzioni di ridondanza, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). 

Per informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure, vedere il [contratto di servizio di Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Archiviazione Premium supporta solo l'archiviazione con ridondanza locale. Per informazioni su Archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Modifica della strategia di replica
È possibile modificare la strategia di replica dell'account di archiviazione tramite il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o una delle numerose [librerie client di Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). La modifica del tipo di replica dell'account di archiviazione non comporta un tempo di inattività.

   > [!NOTE]
   > Attualmente non è possibile usare il portale o l'API per convertire l'account in archiviazione ZRS. Per convertire la replica dell'account in ZRS, vedere [Archiviazione con ridondanza della zona (ZRS).](storage-redundancy-zrs.md) per maggiori dettagli.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Vengono addebitati costi per la modifica della strategia di replica dell'account personale?
Dipende dal percorso di conversione. Dall'offerta di ridondanza più economica alla più costosa, sono disponibili le strategie di replica LRS, ZRS, GRS e RA-GRS. Se ad esempio si passa *da* LRS a un'altra strategia di replica, vengono addebitati costi aggiuntivi perché si esegue la conversione a un livello di ridondanza più sofisticato. Il passaggio *a* GRS o RA-GRS comporta un addebito per la larghezza di banda in uscita poiché i dati (nell'area primaria) vengono replicati nell'area secondaria remota. Questo addebito viene applicato una sola volta, durante la fase di configurazione iniziale. Dopo la copia dei dati non sono previsti altri costi di conversione. Verranno addebitati solo i costi per la replica di nuovi dati o di aggiornamenti ai dati esistenti. Per informazioni dettagliate sui costi addebitati per la larghezza di banda, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se si passa da GRS a LRS non sono previsti costi aggiuntivi, ma i dati replicati vengono eliminati dalla località secondaria.

## <a name="see-also"></a>Vedere anche 

- [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per l'Archiviazione di Azure](storage-redundancy-lrs.md)
- [Archiviazione con ridondanza della zona (ZRS): applicazioni di Archiviazione di Azure a disponibilità elevata](storage-redundancy-zrs.md)
- [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md)
- [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md)
- [Progettazione di applicazioni a disponibilità elevata con archiviazione RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Opzioni di ridondanza di Archiviazione di Microsoft Azure e archiviazione con ridondanza geografica e accesso in lettura ](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Paper SOSP - Archiviazione di Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
