---
title: Eliminazione temporanea per l'archiviazione BLOB
titleSuffix: Azure Storage
description: Abilitare l'eliminazione temporanea per gli oggetti BLOB in modo che sia possibile ripristinare i dati in modo più semplice quando vengono erroneamente modificati o eliminati da un'applicazione o da un altro utente dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f8e84e845910b8f84a9b3f84ad414f2ecdd250a5
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223789"
---
# <a name="soft-delete-for-blob-storage"></a>Eliminazione temporanea per l'archiviazione BLOB

L'eliminazione temporanea protegge i dati BLOB da modifiche accidentali o erronee o eliminazioni. Quando l'eliminazione temporanea è abilitata per un account di archiviazione, i BLOB, le versioni BLOB (anteprima) e gli snapshot nell'account di archiviazione possono essere ripristinati dopo l'eliminazione, entro il periodo di conservazione specificato.

Se esiste la possibilità che i dati vengano accidentalmente modificati o eliminati da un'applicazione o da un utente con un altro account di archiviazione, Microsoft consiglia di abilitare l'eliminazione temporanea.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete"></a>Informazioni sull'eliminazione temporanea

Quando l'eliminazione temporanea è abilitata in un account di archiviazione, è possibile ripristinare gli oggetti dopo che sono stati eliminati, entro il periodo di conservazione dei dati specificato. Questa protezione si estende a tutti i dati BLOB (BLOB in blocchi, BLOB di Accodamento e BLOB di pagine) cancellati come risultato di una sovrascrittura.

Se i dati di un BLOB o di uno snapshot esistente vengono eliminati mentre l'eliminazione temporanea è abilitata ma il controllo delle versioni del BLOB (anteprima) non è abilitato, viene generato uno snapshot eliminato temporaneamente per salvare lo stato dei dati sovrascritti. Dopo la scadenza del periodo di memorizzazione specificato, l'oggetto viene eliminato definitivamente.

Se per l'account di archiviazione sono abilitate le funzionalità di controllo delle versioni e eliminazione temporanea dei BLOB, l'eliminazione di un BLOB crea una nuova versione anziché uno snapshot eliminato temporaneamente. La nuova versione non viene eliminata temporaneamente e non viene rimossa al termine del periodo di memorizzazione dell'eliminazione temporanea. Le versioni eliminate temporaneamente di un BLOB possono essere ripristinate entro il periodo di memorizzazione chiamando l'operazione di [annullamento dell'eliminazione del BLOB](/rest/api/storageservices/undelete-blob) . Il BLOB può essere successivamente ripristinato da una delle relative versioni chiamando l'operazione [Copy Blob](/rest/api/storageservices/copy-blob) . Per altre informazioni sull'uso combinato del controllo delle versioni e dell'eliminazione temporanea, vedere [controllo delle versioni dei BLOB e eliminazione](versioning-overview.md#blob-versioning-and-soft-delete)temporanea.

Gli oggetti eliminati temporaneamente sono invisibili, a meno che non vengano elencati in modo esplicito.

L'eliminazione temporanea è compatibile con le versioni precedenti, pertanto non è necessario apportare modifiche alle applicazioni per sfruttare i vantaggi offerti dalle protezioni offerte da questa funzionalità. Il [ripristino dei dati](#recovery) introduce tuttavia una nuova API **Undelete Blob**.

L'eliminazione temporanea è disponibile per gli account di archiviazione BLOB, per utilizzo generico V2, per utilizzo generico V1 e per utilizzo generico. Sono supportati entrambi i tipi di account standard e Premium. L'eliminazione temporanea è disponibile per tutti i livelli di archiviazione, ad esempio frequente, ad accesso sporadico e archivio. L'eliminazione temporanea è disponibile per i dischi non gestiti, ovvero i BLOB di pagine sotto le quinte, ma non è disponibile per Managed Disks.

### <a name="configuration-settings"></a>Impostazioni di configurazione

Quando si crea un nuovo account, l'eliminazione temporanea è disabilitata per impostazione predefinita. L'eliminazione temporanea è disabilitata per impostazione predefinita anche per gli account di archiviazione esistenti. È possibile abilitare o disabilitare l'eliminazione temporanea per un account di archiviazione in qualsiasi momento.

Quando si Abilita l'eliminazione temporanea, è necessario configurare il periodo di conservazione. Il periodo di conservazione indica per quanto tempo i dati eliminati temporaneamente restano memorizzati e disponibili per il ripristino. Per gli oggetti eliminati in modo esplicito, l'orologio del periodo di memorizzazione viene avviato quando i dati vengono eliminati. Per le versioni eliminate temporaneamente o gli snapshot generati dalla funzionalità di eliminazione temporanea quando i dati vengono sovrascritti, il clock viene avviato quando viene generata la versione o lo snapshot. Il periodo di memorizzazione può essere compreso tra 1 e 365 giorni.

È possibile modificare il periodo di conservazione dell'eliminazione temporanea in qualsiasi momento. Un periodo di memorizzazione aggiornato si applica solo ai dati appena eliminati. I dati eliminati in precedenza scadono in base al periodo di memorizzazione configurato quando i dati sono stati eliminati. Il tentativo di eliminare un oggetto eliminato temporaneamente non influisce sull'ora di scadenza.

Se si disabilita l'eliminazione temporanea, è possibile continuare ad accedere e ripristinare i dati eliminati temporaneamente nell'account di archiviazione salvato durante l'abilitazione della funzionalità.

### <a name="saving-deleted-data"></a>Salvataggio dei dati eliminati

L'eliminazione temporanea consente di mantenere i dati in molti casi in cui gli oggetti vengono eliminati o sovrascritti.

Quando un BLOB viene sovrascritto con **Put Blob**, **Put Block List**o **Copy Blob**, viene generata automaticamente una versione o uno snapshot dello stato del BLOB prima dell'operazione di scrittura. Questo oggetto è invisibile, a meno che non vengano elencati in modo esplicito gli oggetti eliminati temporaneamente. Vedere la sezione [Ripristino](#recovery) per informazioni su come elencare gli oggetti eliminati temporaneamente.

![Diagramma che illustra come vengono archiviati gli snapshot dei BLOB quando vengono sovrascritti usando Put Blob, Put Block List o copy BLOB.](media/soft-delete-overview/storage-blob-soft-delete-overwrite.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono blu. I dati scritti più di recente vengono visualizzati sotto i dati meno recenti. Quando B0 viene sovrascritto con B1, viene generato uno snapshot di B0 eliminato temporaneamente. Quando B1 viene sovrascritto con B2, viene generato uno snapshot di B1 eliminato temporaneamente.*

> [!NOTE]  
> L'eliminazione temporanea consente solo la protezione da sovrascrittura per le operazioni di copia quando è abilitata per l'account del BLOB di destinazione.

> [!NOTE]  
> L'eliminazione temporanea non consente la protezione da sovrascrittura per i BLOB nel livello di archiviazione archivio. Se un BLOB nel livello di archiviazione archivio viene sovrascritto con un nuovo BLOB in qualsiasi livello, il BLOB sovrascritto scade definitivamente.

Quando viene chiamato **Delete Blob** su uno snapshot, lo snapshot viene contrassegnato come eliminato temporaneamente. Non viene generato un nuovo snapshot.

![Diagramma che Mostra come vengono eliminati temporaneamente gli snapshot dei BLOB quando si usa Elimina BLOB.](media/soft-delete-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono blu. I dati scritti più di recente vengono visualizzati sotto i dati meno recenti. Quando viene chiamato il **BLOB dello snapshot** , B0 diventa uno snapshot e B1 è lo stato attivo del BLOB. Quando lo snapshot B0 viene eliminato, viene contrassegnato come eliminato temporaneamente.*

Quando viene chiamato **Delete Blob** su un BLOB di base (qualsiasi BLOB che non è di per sé uno snapshot), tale BLOB viene contrassegnato come eliminato temporaneamente. Coerentemente con il comportamento precedente, una chiamata **Delete Blob** su un BLOB contenente snapshot attivi restituisce un errore. Una chiamata **Delete Blob** su un BLOB con snapshot eliminati temporaneamente non restituisce un errore. Quando l'eliminazione temporanea è abilitata, è comunque possibile eliminare un BLOB e tutti i relativi snapshot in un'unica operazione. In questo modo il BLOB di base e gli snapshot vengono contrassegnati come eliminati temporaneamente.

![Un diagramma che mostra cosa accade quando il Blog Delete viene chiamato su un BLOB di base.](media/soft-delete-overview/storage-blob-soft-delete-explicit-include.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono blu. I dati scritti più di recente vengono visualizzati sotto i dati meno recenti. Qui viene eseguita una chiamata **Delete Blob** per eliminare B2 e tutti gli snapshot associati. Il BLOB attivo, B2 e tutti gli snapshot associati sono contrassegnati come eliminati temporaneamente.*

> [!NOTE]  
> Quando un BLOB eliminato temporaneamente viene sovrascritto, viene generato automaticamente uno snapshot eliminato temporaneamente dello stato del BLOB prima dell'operazione di scrittura. Il nuovo BLOB eredita il livello del BLOB sovrascritto.

L'eliminazione temporanea non salva i dati in caso di eliminazioni di contenitori o account, né quando vengono sovrascritti i metadati e le proprietà del BLOB. Per proteggere un account di archiviazione in caso di errata eliminazione, è possibile configurare un blocco tramite Azure Resource Manager. Vedere l'articolo su Azure Resource Manager [Bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/management/lock-resources.md) per altre informazioni.

La tabella seguente illustra il comportamento previsto quando l'eliminazione temporanea è abilitata:

| Operazione API REST | Tipo di risorsa | Descrizione | Modifica del comportamento |
|--------------------|---------------|-------------|--------------------|
| [Eliminazione](/rest/api/storagerp/StorageAccounts/Delete) | Account | Elimina l'account di archiviazione, inclusi tutti i contenitori e i BLOB contenuti al suo interno.                           | Nessuna modifica. I contenitori e i BLOB contenuti nell'account eliminato non sono recuperabili. |
| [Delete Container](/rest/api/storageservices/delete-container) | Contenitore | Elimina il contenitore, inclusi tutti i BLOB contenuti al suo interno. | Nessuna modifica. I BLOB contenuti nell'account eliminato non sono recuperabili. |
| [Put Blob](/rest/api/storageservices/put-blob) | BLOB in blocchi, di Accodamento e di pagine | Crea un nuovo BLOB o sostituisce un BLOB esistente all'interno di un contenitore | Se usata per sostituire un BLOB esistente, viene generato automaticamente uno snapshot dello stato del BLOB prima della chiamata. Questo vale anche per un BLOB eliminato temporaneamente in precedenza solo se è stato sostituito da un BLOB dello stesso tipo (blocco, Accodamento o pagina). Se viene sostituito da un BLOB di tipo diverso, tutti i dati eliminati temporaneamente esistenti scadranno definitivamente. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | BLOB in blocchi, di Accodamento e di pagine | Contrassegna un BLOB o uno snapshot di BLOB per l'eliminazione. Il BLOB o lo snapshot verrà eliminato in seguito durante la Garbage Collection | Se usata per eliminare uno snapshot di BLOB, tale snapshot viene contrassegnato come eliminato temporaneamente. Se usata per eliminare un BLOB, tale BLOB viene contrassegnato come eliminato temporaneamente. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | BLOB in blocchi, di Accodamento e di pagine | Copia un BLOB di origine in un BLOB di destinazione nello stesso account di archiviazione o in un altro account di archiviazione. | Se usata per sostituire un BLOB esistente, viene generato automaticamente uno snapshot dello stato del BLOB prima della chiamata. Questo vale anche per un BLOB eliminato temporaneamente in precedenza solo se è stato sostituito da un BLOB dello stesso tipo (blocco, Accodamento o pagina). Se viene sostituito da un BLOB di tipo diverso, tutti i dati eliminati temporaneamente esistenti scadranno definitivamente. |
| [Put Block](/rest/api/storageservices/put-block) | BLOB in blocchi | Crea un nuovo blocco di cui eseguire il commit come parte di un BLOB in blocchi. | Se utilizzato per eseguire il commit di un blocco in un BLOB attivo, non viene apportata alcuna modifica. In caso di commit di un blocco in un BLOB eliminato temporaneamente, viene creato un nuovo BLOB e generato automaticamente uno snapshot per acquisire lo stato del BLOB eliminato temporaneamente. |
| [Put Block List](/rest/api/storageservices/put-block-list) | BLOB in blocchi | Esegue il commit di un BLOB specificando il set di ID dei blocchi che compongono il BLOB in blocchi. | Se usata per sostituire un BLOB esistente, viene generato automaticamente uno snapshot dello stato del BLOB prima della chiamata. Questo vale anche per un BLOB eliminato temporaneamente in precedenza se e solo se si tratta di un BLOB in blocchi. Se viene sostituito da un BLOB di tipo diverso, tutti i dati eliminati temporaneamente esistenti scadranno definitivamente. |
| [Put Page](/rest/api/storageservices/put-page) | BLOB di pagine | Scrive un intervallo di pagine in un BLOB di pagine. | Nessuna modifica. I dati BLOB di pagine sovrascritti o cancellati con questa operazione non vengono salvati e non sono recuperabili. |
| [Append Block](/rest/api/storageservices/append-block) | BLOB di accodamento | Scrive un blocco di dati alla fine di un BLOB di accodamento | Nessuna modifica. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | BLOB in blocchi, di Accodamento e di pagine | Imposta i valori delle proprietà di sistema definite per un BLOB. | Nessuna modifica. Le proprietà del BLOB sovrascritto non sono recuperabili. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | BLOB in blocchi, di Accodamento e di pagine | Imposta i metadati definiti dall'utente per il BLOB specificato come una o più coppie nome-valore. | Nessuna modifica. I metadati del BLOB sovrascritto non sono recuperabili. |

È importante notare che la chiamata di **Put Page** per sovrascrivere o cancellare gli intervalli di un BLOB di pagine non genererà automaticamente snapshot. I dischi delle macchine virtuali sono supportati da BLOB di pagine e usano la **pagina put** per scrivere i dati.

### <a name="recovery"></a>Ripristino

La chiamata dell'operazione [Undelete BLOB](/rest/api/storageservices/undelete-blob) su un BLOB di base eliminato temporaneamente li ripristina e tutti gli snapshot eliminati temporaneamente associati come attivi. Quando si chiama l'operazione **Undelete BLOB** su un BLOB di base attivo, tutti gli snapshot eliminati temporaneamente associati vengono ripristinati come attivi. Quando gli snapshot vengono ripristinati come attivi, sono simili agli snapshot generati dall'utente e non sovrascrivono il BLOB di base.

Per ripristinare un BLOB in uno snapshot eliminato temporaneamente specifico, è possibile chiamare **Undelete BLOB** sul BLOB di base. quindi copiare lo snapshot sul BLOB ora attivo. È anche possibile copiare lo snapshot in un nuovo BLOB.

![Diagramma che mostra cosa accade quando si usa Undelete BLOB.](media/soft-delete-overview/storage-blob-soft-delete-recover.png)

*I dati eliminati temporaneamente sono di colore grigio, mentre i dati attivi sono blu. I dati scritti più di recente vengono visualizzati sotto i dati meno recenti. Qui viene chiamato **Undelete BLOB** sul BLOB B, ripristinando in questo modo il BLOB di base, B1 e tutti gli snapshot associati, in questo caso B0, come Active. Nel secondo passaggio, B0 viene copiato sul BLOB di base. Questa operazione di copia genera uno snapshot di B1 eliminato temporaneamente.*

Per visualizzare i BLOB e gli snapshot dei BOB eliminati temporaneamente, è possibile scegliere di includere i dati eliminati in **List Blobs**. Si può scegliere di visualizzare solo i BLOB di base eliminati temporaneamente o includere anche gli snapshot dei BLOB eliminati temporaneamente. Per tutti i dati eliminati temporaneamente, è possibile visualizzare l'ora di eliminazione, nonché il numero di giorni che mancano alla scadenza definitiva.

### <a name="example"></a>Esempio

Di seguito è riportato l'output della console di uno script .NET che carica, sovrascrive, snapshot, Elimina e ripristina un BLOB denominato *HelloWorld* quando l'eliminazione temporanea è attivata:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Vedere la sezione [Passaggi successivi](#next-steps) per un puntatore all'applicazione che ha generato questo output.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti i dati eliminati temporaneamente vengono fatturati alla stessa tariffa dei dati attivi. Non si incorre in alcun addebito per i dati che vengono eliminati definitivamente al termine del periodo di conservazione configurato. Per approfondimenti sugli snapshot e sull'incremento dei costi dovuto agli snapshot, vedere [Informazioni sull'incremento dei costi dovuto agli snapshot](storage-blob-snapshots.md).

Non verrà addebitato alcun costo per le transazioni relative alla generazione automatica di snapshot. Verranno addebitate le transazioni di **annullamento dell'eliminazione dei BLOB** alla tariffa per le operazioni di scrittura.

Per altri dettagli sui prezzi per Archiviazione BLOB di Azure in generale, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando inizialmente si attiva l'eliminazione temporanea, Microsoft consiglia di usare un breve periodo di conservazione per comprendere meglio il modo in cui la funzionalità influirà sulla fattura.

L'abilitazione dell'eliminazione temporanea per i dati sovrascritti frequentemente può comportare un aumento degli addebiti per la capacità di archiviazione e una latenza maggiore È possibile mitigare questo costo aggiuntivo e la latenza archiviando i dati sovrascritti di frequente in un account di archiviazione separato in cui l'eliminazione temporanea è disabilitata.

## <a name="faq"></a>Domande frequenti

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>È possibile usare l'API Imposta livello BLOB per impostare il livello dei BLOB con gli snapshot con eliminazione temporanea?

Sì. Gli snapshot con eliminazione temporanea rimangono nel livello originale, ma il BLOB di base viene spostato nel nuovo livello.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Gli account di archiviazione Premium hanno un limite di 100 snapshot per BLOB. Gli snapshot eliminati temporaneamente vengono considerati ai fini questo limite?

No, gli snapshot eliminati temporaneamente non vengono considerati ai fini questo limite.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Se si elimina un intero account o un intero contenitore con l'eliminazione temporanea abilitata, tutti i BLOB associati verranno salvati?

No, se si elimina un intero account o un intero contenitore, tutti i BLOB associati verranno eliminati definitivamente. Per altre informazioni sulla protezione di un account di archiviazione da eliminazioni accidentali, vedere [bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>È possibile visualizzare le metriche relative alla capacità per i dati eliminati?

I dati eliminati temporaneamente sono inclusi nella capacità totale dell'account di archiviazione. Per ulteriori informazioni sul rilevamento e il monitoraggio della capacità di archiviazione, vedere [analisi archiviazione](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>È possibile leggere e copiare gli snapshot del BLOB eliminati temporaneamente?  

Sì, ma prima è necessario chiamare Undelete sul BLOB.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>L'eliminazione temporanea è disponibile per i dischi delle macchine virtuali?  

L'eliminazione temporanea è disponibile per i dischi non gestiti sia Premium che standard, ovvero i BLOB di pagine sotto le quinte. L'eliminazione temporanea consente solo di ripristinare i dati eliminati da **Elimina BLOB**, **Put Blob**, **Put Block List**e **Copy Blob** Operation. I dati sovrascritti tramite una chiamata a **Put Page** non sono recuperabili.

Una macchina virtuale di Azure scrive in un disco non gestito usando le chiamate a **Put Page**, quindi l'uso dell'eliminazione temporanea per annullare le Scritture in un disco non gestito da una macchina virtuale di Azure non è uno scenario supportato.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>È necessario modificare le applicazioni esistenti per usare l'eliminazione temporanea?

È possibile sfruttare i vantaggi dell'eliminazione temporanea indipendentemente dalla versione dell'API in uso. Tuttavia, per elencare e recuperare i BLOB eliminati temporaneamente e gli snapshot BLOB, è necessario usare la versione 2017-07-29 dell' [API REST di archiviazione di Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) o superiore. Microsoft consiglia sempre di usare la versione più recente dell'API di archiviazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Controllo delle versioni dei BLOB (anteprima)](versioning-overview.md)
