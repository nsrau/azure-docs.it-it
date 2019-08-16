---
title: Ridondanza dei dati in Archiviazione di Azure | Microsoft Docs
description: I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le opzioni di ridondanza includono archiviazione con ridondanza locale (con ridondanza locale), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), archiviazione con ridondanza geografica e con ridondanza geografica e accesso in lettura archiviazione (RA-GZRS) (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 17d1bd95067c15bd67f80f3713f0e497bff8a68d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516117"
---
# <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure

I dati nell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne la durabilità e la disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente.

Garantisce infine che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Altre informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure sono reperibili nel Contratto di servizio.

Archiviazione di Azure verifica regolarmente l'integrità dei dati archiviati usando i controlli di ridondanza ciclici (CRC). Se viene rilevato un danneggiamento dei dati, viene ripristinato utilizzando dati ridondanti. Archiviazione di Azure calcola inoltre i checksum su tutto il traffico di rete per rilevare il danneggiamento dei pacchetti di dati durante l'archiviazione o il recupero dei dati.

## <a name="choosing-a-redundancy-option"></a>Selezione di un'opzione di ridondanza

Quando si crea un account di archiviazione, è possibile selezionare una delle opzioni di ridondanza seguenti:

* [Archiviazione con ridondanza locale (LRS)](storage-redundancy-lrs.md)
* [Archiviazione con ridondanza della zona (ZRS).](storage-redundancy-zrs.md)
* [Archiviazione con ridondanza geografica (GRS)](storage-redundancy-grs.md)
* [Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).](storage-redundancy-grs.md#read-access-geo-redundant-storage)
* [Archiviazione con ridondanza della zona geografica (GZRS)](storage-redundancy-gzrs.md)
* [Archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS)](storage-redundancy-gzrs.md)

La tabella seguente presenta una rapida panoramica dell'ambito di durabilità e disponibilità che ogni strategia di replica è in grado di offrire per un determinato tipo di evento (o un evento di impatto simile).

| Scenario                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Mancata disponibilità di un nodo in un data center                                                                 | Sì                             | Sì                              | Sì                                  | Yes                                  |
| Mancata disponibilità di un intero data center (di zona o non di zona)                                           | No                              | Yes                              | Sì                                  | Sì                                  |
| Interruzione a livello di area                                                                                     | No                              | No                               | Yes                                  | Yes                                  |
| Accesso in lettura ai dati (in un'area remota con replica geografica) in caso di mancata disponibilità a livello di area | No                              | No                               | Sì (con RA-GRS)                                   | Sì (con RA-GZRS)                                 |
| Replica progettata per fornire una durabilità di \_\_ per gli oggetti nel corso di un determinato anno                                          | Almeno 99,999999999% (11 9) | Almeno 99,9999999999% (12 9) | Almeno 99,99999999999999% (16 9) | Almeno 99,99999999999999% (16 9) |
| Tipi di account di archiviazione supportati                                                                   | GPv2, GPv1, BLOB                | GPv2                             | GPv2, GPv1, BLOB                     | GPv2                     |
| Contratto di servizio relativo alla disponibilità per le richieste di lettura | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,99% (99,9% per livello di accesso sporadico) |
| Contratto di servizio relativo alla disponibilità per le richieste di scrittura | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) |

Tutti i dati nell'account di archiviazione vengono replicati, inclusi i BLOB in blocchi e i BLOB di Accodamento, i BLOB di pagine, le code, le tabelle e i file. Tutti i tipi di account di archiviazione vengono replicati, anche se ZRS richiede un account di archiviazione per utilizzo generico V2.

Per informazioni sui prezzi delle varie opzioni di ridondanza, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). 

Per informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure, vedere il [contratto di servizio di Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Archiviazione Premium di Azure supporta solo l'archiviazione con ridondanza locale (con ridondanza locale).

## <a name="changing-replication-strategy"></a>Modifica della strategia di replica

È possibile modificare la strategia di replica dell'account di archiviazione usando il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)o una delle [librerie client di archiviazione di Azure](https://docs.microsoft.com/azure/index#pivot=sdkstools). La modifica del tipo di replica dell'account di archiviazione non comporta un tempo di inattività.

   > [!NOTE]
   > Attualmente, non è possibile usare la portale di Azure o le librerie client di archiviazione di Azure per convertire l'account in ZRS, GZRS o RA-GZRS. Per eseguire la migrazione dell'account a ZRS, vedere [archiviazione con ridondanza della zona (ZRS) per la compilazione di applicazioni di archiviazione di Azure a disponibilità elevata](storage-redundancy-zrs.md) per informazioni dettagliate. Per eseguire la migrazione di GZRS o RA-GZRS, vedere [archiviazione con ridondanza geografica per la disponibilità elevata e durabilità massima (anteprima)](storage-redundancy-zrs.md) per informazioni dettagliate.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Vengono addebitati costi per la modifica della strategia di replica dell'account personale?

Dipende dal percorso di conversione. Ordinamento da almeno a costose offerte di ridondanza di archiviazione di Azure con ridondanza locale, ZRS, GRS, RA-GRS, GZRS e RA-GZRS. Ad esempio, se si passa *da* con ridondanza locale a un altro tipo di replica, verranno addebitati costi aggiuntivi perché si passa a un livello di ridondanza più sofisticato. La migrazione *a* GRS o RA-GRS comporta un addebito per la larghezza di banda in uscita perché i dati dell'area primaria vengono replicati nell'area secondaria remota. Questo addebito è un costo monouso alla configurazione iniziale. Dopo la copia dei dati, non sono previsti ulteriori addebiti per la migrazione. Viene addebitato solo il costo per la replica di eventuali nuovi o aggiornamenti dei dati esistenti. Per informazioni dettagliate sui costi addebitati per la larghezza di banda, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se si esegue la migrazione dell'account di archiviazione da GRS a con ridondanza locale, non sono previsti costi aggiuntivi, ma i dati replicati vengono eliminati dalla posizione secondaria.

Se si esegue la migrazione dell'account di archiviazione da RA-GRS a GRS o con ridondanza locale, tale account viene fatturato come RA-GRS per altri 30 giorni oltre la data in cui è stata convertita.

## <a name="see-also"></a>Vedere anche

- [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per Archiviazione di Azure](storage-redundancy-lrs.md)
- [Archiviazione con ridondanza della zona (ZRS): applicazioni di Archiviazione di Azure a disponibilità elevata](storage-redundancy-zrs.md)
- [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md)
- [Archiviazione con ridondanza geografica (GZRS) per disponibilità elevata e durabilità massima (anteprima)](storage-redundancy-gzrs.md)
- [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md)
- [Progettazione di applicazioni a disponibilità elevata con archiviazione RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Archiviazione di Microsoft Azure opzioni di ridondanza e archiviazione con ridondanza geografica e accesso in lettura](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Paper SOSP - Archiviazione di Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
