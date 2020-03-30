---
title: Ridondanza dei dati
titleSuffix: Azure Storage
description: I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le configurazioni di ridondanza includono l'archiviazione con ridondanza locale (LRS), l'archiviazione con ridondanza di zona (GRS), l'archiviazione con ridondanza con accesso in lettura (RA-GRS), l'archiviazione con ridondanza geografica (GRS) (anteprima) e l'accesso in lettura archiviazione con ridondanza geografica (RA-G-RS) (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7ae5f59a1bd96362d5466b2f6363185ba168d942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255262"
---
# <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure

Archiviazione di Azure archivia sempre più copie dei dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e disastri naturali di grandi dimensioni. La ridondanza garantisce che l'account di archiviazione soddisfi il contratto di servizio [(SLA) per Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori.

Quando si decide quale opzione di ridondanza è più adatta allo scenario, considerare i compromessi tra costi inferiori e maggiore disponibilità e durata. I fattori che consentono di determinare l'opzione di ridondanza da scegliere includono:  

- Modalità di replica dei dati nell'area primaria
- Se i dati vengono replicati in una seconda posizione geograficamente distante rispetto all'area primaria, per proteggersi da disastri regionali
- Se l'applicazione richiede l'accesso in lettura ai dati replicati nell'area secondaria se l'area primaria diventa non disponibile per qualsiasi motivo

## <a name="redundancy-in-the-primary-region"></a>Ridondanza nell'area primaria

I dati in un account di Archiviazione di Azure vengono sempre replicati tre volte nell'area primaria. Archiviazione di Azure offre due opzioni per la modalità di replica dei dati nell'area primaria:Azure Storage offers two options for how your data is replicated in the primary region:

- **L'archiviazione con ridondanza locale (LRS)** copia i dati in modo sincrono tre volte all'interno di una singola posizione fisica nell'area primaria. LRS è l'opzione di replica meno costosa, ma non è consigliata per le applicazioni che richiedono disponibilità elevata.
- **L'archiviazione con ridondanza** di zona copia i dati in modo sincrono tra tre zone di disponibilità di Azure nell'area primaria. Per le applicazioni che richiedono disponibilità elevata, Microsoft consiglia di usare il sistema di replica nell'area primaria e di eseguire anche la replica in un'area secondaria.

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale

L'archiviazione con ridondanza locale replica i dati tre volte all'interno di una singola posizione fisica nell'area primaria. LRS fornisce una durata di almeno 99.999999999% (11 nove) di oggetti in un determinato anno.

LRS è l'opzione di ridondanza più economica e offre la durata minima rispetto ad altre opzioni. LRS protegge i dati da guasti del rack del server e delle unità. Tuttavia, se si verifica un'emergenza, ad esempio un incendio o un'inondazione all'interno del data center, tutte le repliche di un account di archiviazione che usano LRS potrebbero essere perse o irrecuperabili. Per ridurre questo rischio, Microsoft consiglia di utilizzare [l'archiviazione con ridondanza](#zone-redundant-storage) di zona , l'archiviazione [con ridondanza geografica](#geo-redundant-storage) (GRS) o l'archiviazione con ridondanza geografica [(anteprima)](#geo-zone-redundant-storage-preview) (G-RS).

Una richiesta di scrittura a un account di archiviazione che usa LRS viene eseguita in modo sincrono. L'operazione di scrittura restituisce correttamente solo dopo che i dati sono stati scritti in tutte e tre le repliche.

LRS è una buona scelta per i seguenti scenari:

- Se l'applicazione archivia dati che possono essere ricostruiti facilmente in caso di perdita, è possibile scegliere l'archiviazione con ridondanza locale.
- Se l'applicazione è limitata alla replica dei dati solo all'interno di un paese o di un'area geografica a causa dei requisiti di governance dei dati, è possibile optare per LRS. In alcuni casi, le aree associate in cui i dati sono replicati geograficamente possono trovarsi in un altro paese o in un'altra area geografica. Per altre informazioni sulle aree abbinate, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona

L'archiviazione con ridondanza di zona (ORS) replica i dati di Archiviazione di Azure in modo sincrono tra tre zone di disponibilità di Azure nell'area primaria. Ogni zona di disponibilità è una posizione fisica separata con alimentazione, raffreddamento e rete indipendenti. La durata per gli oggetti dati di Archiviazione di Azure di almeno 99.9999999999% (12 9) in un determinato anno.

Con il sistema di controllo di accesso (RS), i dati sono ancora accessibili sia per le operazioni di lettura che per la scrittura, anche se una zona non è più disponibile. Se una zona non è più disponibile, Azure esegue gli aggiornamenti di rete, ad esempio il reindirizzamento DNS. Questi aggiornamenti possono influire sull'applicazione se si accede ai dati prima del completamento degli aggiornamenti. Quando si progettano le applicazioni per il sistema di gestione degli errori temporanei, seguire le procedure per la gestione degli errori temporanei, inclusa l'implementazione di criteri di ripetizione dei tentativi con back-off esponenziale.

Una richiesta di scrittura a un account di archiviazione che usa il metodo . L'operazione di scrittura restituisce correttamente solo dopo che i dati sono stati scritti in tutte le repliche nelle tre zone di disponibilità.

Microsoft consiglia di utilizzare il sistema di installazione di RS nell'area primaria per gli scenari che richiedono coerenza, durabilità e disponibilità elevata. Il servizio di gestione delle prestazioni offre prestazioni eccellenti, bassa latenza e resilienza per i dati se diventano temporaneamente non disponibili. Tuttavia, è possibile che i dati non vengano protetti da un'emergenza regionale in cui più zone sono interessate in modo permanente. Per la protezione da disastri regionali, Microsoft consiglia di utilizzare [l'archiviazione con ridondanza](#geo-zone-redundant-storage-preview) di zona geografica, che usa il sistema di replica dei dati nell'area primaria e replica anche i dati in un'area secondaria.

Nella tabella seguente vengono illustrati i tipi di account di archiviazione che supportano il supporto di RS in quali aree:The following table shows which types of storage accounts support -RS in which regions:

|    Tipo di account di archiviazione    |    Aree supportate    |    Servizi supportati    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Generico v2<sup>1</sup>    | Asia sudorientale<br /> Australia orientale<br /> Europa settentrionale<br />  Europa occidentale<br /> Francia centrale<br /> Giappone orientale<br /> Sudafrica settentrionale<br /> Regno Unito meridionale<br /> Stati Uniti centrali<br /> Stati Uniti orientali<br /> Stati Uniti orientali 2<br /> Stati Uniti occidentali 2    |    BLOB in blocchi<br /> Blob di pagine<sup>2</sup><br /> Condivisioni di file (standard)<br /> Tabelle<br /> Code<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa occidentale<br /> Stati Uniti orientali    |    Solo BLOB in blocchi    |
|    FileStorage (Archiviazione file)    | Europa occidentale<br /> Stati Uniti orientali    |    Solo file di Azure    |

<sup>1</sup> Il livello di archiviazione non è attualmente supportato per gli account .RS.<br />
<sup>2</sup> Gli account di archiviazione che contengono dischi gestiti di Azure per le macchine virtuali usano sempre LRS.2 Storage accounts that contain Azure managed disks for virtual machines always use LRS. Anche i dischi non gestiti di Azure devono usare LRS. È possibile creare un account di archiviazione per i dischi non gestiti di Azure che usa GRS, ma non è consigliabile a causa di potenziali problemi di coerenza rispetto alla replica geografica asincrona. I dischi gestiti e non gestiti non supportano il formato RS o G-RS. Per altre informazioni sui dischi gestiti, vedere [Determinazione dei prezzi per i dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/)di Azure.For more information on managed disks, see Pricing for Azure managed disks .

Per informazioni sulle aree che supportano il supporto per il servizio, vedere **Supporto dei servizi per area** in Che cosa sono le zone di disponibilità di [Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Ridondanza in un'area secondaria

Per le applicazioni che richiedono la disponibilità elevata, è possibile scegliere di copiare i dati nell'account di archiviazione in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Se l'account di archiviazione viene copiato in un'area secondaria, i dati sono durevoli anche in caso di interruzione completa dell'area geografica o di un'emergenza in cui l'area primaria non è recuperabile.

L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria associata viene determinata in base all'area primaria e non è possibile modificarla. Per altre informazioni sulle aree supportate da Azure, vedere Aree di [Azure.For](https://azure.microsoft.com/global-infrastructure/regions/)more information about regions supported by Azure, see Azure regions.

Archiviazione di Azure offre due opzioni per copiare i dati in un'area secondaria:Azure Storage offers two options for copying your data to a secondary region:

- **L'archiviazione con ridondanza geografica (GRS, Geoperunisci Storage)** copia i dati in modo sincrono tre volte all'interno di una singola posizione fisica nell'area primaria utilizzando LRS. Copia quindi i dati in modo asincrono in una singola posizione fisica nell'area secondaria.
- L'archiviazione con ridondanza geografica (GRS) (anteprima) copia i dati in modo sincrono tra tre zone di disponibilità di Azure nell'area primaria usando il sistema di disponibilità di **Azure.Geo-zone-redundant storage (G-RS)** (preview) copies your data synchronously across three Azure availability zones in the primary region using .RS. Copia quindi i dati in modo asincrono in una singola posizione fisica nell'area secondaria.

La differenza principale tra GRS e G-RS è il modo in cui i dati vengono replicati nell'area primaria. All'interno della posizione secondaria, i dati vengono sempre replicati in modo sincrono tre volte utilizzando LRS.

Con GRS o G-RS, i dati nella posizione secondaria non sono disponibili per l'accesso in lettura o scrittura a meno che non si esista un failover nell'area secondaria. Per l'accesso in lettura alla posizione secondaria, configurare l'account di archiviazione per l'utilizzo dell'archiviazione con ridondanza geografica ad accesso in lettura (RA-GRS) o dell'archiviazione con ridondanza zona geografica ad accesso in lettura .RA-G-RS. Per ulteriori informazioni, vedere [Accesso in lettura ai dati nell'area secondaria.](#read-access-to-data-in-the-secondary-region)

Se l'area primaria non è più disponibile, è possibile scegliere di eseguire il failover nell'area secondaria (anteprima). Al termine del failover, l'area secondaria diventa l'area primaria ed è possibile leggere e scrivere nuovamente i dati. Per ulteriori informazioni sul ripristino di emergenza e su come eseguire il failover nell'area secondaria, vedere [Ripristino di emergenza e failover dell'account (anteprima).](storage-disaster-recovery-guidance.md)

> [!IMPORTANT]
> Poiché i dati vengono replicati nell'area secondaria in modo asincrono, un errore che influisce sull'area primaria può causare la perdita di dati se l'area primaria non può essere ripristinata. L'intervallo tra le scritture più recenti nell'area primaria e l'ultima scrittura nell'area secondaria è noto come obiettivo del punto di ripristino (RPO). e indica il punto nel tempo in cui è possibile recuperare i dati. Archiviazione di Azure ha in genere un RPO inferiore a 15 minuti, anche se attualmente non è disponibile alcun sLA sul tempo necessario per replicare i dati nell'area secondaria.

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

L'archiviazione con ridondanza geografica (GRS, Geoperunisci Storage) copia i dati in modo sincrono tre volte all'interno di una singola posizione fisica nell'area primaria utilizzando LRS. Copia quindi i dati in modo asincrono in una singola posizione fisica in un'area secondaria che si trova a centinaia di chilometri di distanza dall'area primaria. GRS offre durabilità per gli oggetti dati di Archiviazione di Azure di almeno 99.9999999999999% (16 9) in un determinato anno.

Un'operazione di scrittura viene prima eseguito il commit nella posizione primaria e replicata tramite LRS. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale.

### <a name="geo-zone-redundant-storage-preview"></a>Archiviazione con ridondanza geografica (anteprima)Geo-zone-redundant storage (preview)

L'archiviazione con ridondanza di zona geografica (GRS) (anteprima) combina l'elevata disponibilità fornita dalla ridondanza tra le zone di disponibilità con la protezione dalle interruzioni regionali fornite dalla replica geografica. I dati in un account di archiviazione G-RS vengono copiati in tre zone di disponibilità di [Azure](../../availability-zones/az-overview.md) nell'area primaria e vengono inoltre replicati in un'area geografica secondaria per la protezione da emergenze regionali. Microsoft consiglia di utilizzare il sistema Di gestione delle applicazioni per le applicazioni che richiedono la massima coerenza, durata e disponibilità, prestazioni eccellenti e resilienza per il ripristino di emergenza.

Con un account di archiviazione G-RS, è possibile continuare a leggere e scrivere dati se una zona di disponibilità non è più disponibile o non è recuperabile. Inoltre, i dati sono durevoli anche in caso di un'interruzione completa dell'area o di un'emergenza in cui l'area primaria non è recuperabile. L'obiettivo è quello di fornire almeno la durata degli oggetti al 99.9999999999999% (16 9's) in un determinato anno.

Solo gli account di archiviazione generici v2 supportano G-RS e RA-G-RS. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md). I BLOB dei blocchi, i BLOB di pagina (ad eccezione dei dischi VHD), i file, le tabelle e le code sono supportati da R-RS e RA-G-RS.

Sono attualmente disponibili per l'anteprima in modalità di anteprima per l'anteprima nelle seguenti aree geografiche:

- Asia sudorientale
- Europa settentrionale
- Europa occidentale
- Giappone orientale
- Regno Unito meridionale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centrali
- Stati Uniti occidentali 2

Microsoft continua ad abilitare g-RS e RA-G-RS in altre aree di Azure. Controllare regolarmente la pagina [Aggiornamenti del servizio](https://azure.microsoft.com/updates/) di Azure per informazioni sulle aree supportate.

Per informazioni sui prezzi di anteprima, fare riferimento a Prezzi di anteprima di G-RS per [BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), [file](https://azure.microsoft.com/pricing/details/storage/files/), [code](https://azure.microsoft.com/pricing/details/storage/queues/)e [tabelle](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft consiglia di non usare le funzionalità di anteprima per i carichi di lavoro di produzione.

## <a name="read-access-to-data-in-the-secondary-region"></a>Accesso in lettura ai dati nell'area secondariaRead access to data in the secondary region

L'archiviazione con ridondanza geografica (con GRS o G-RS) replica i dati in un'altra posizione fisica nell'area secondaria per proteggerli da interruzioni regionali. Tuttavia, tali dati sono disponibili per la lettura solo se il cliente o Microsoft avvia un failover dall'area primaria a quella secondaria. Quando si abilita l'accesso in lettura all'area secondaria, i dati sono disponibili per la lettura se l'area primaria non è più disponibile. Per l'accesso in lettura all'area secondaria, abilitare l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS) o l'archiviazione con ridondanza di zona geografica ad accesso in lettura (RA-G-RS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Progettare le applicazioni per l'accesso in lettura al

Se l'account di archiviazione è configurato per l'accesso in lettura all'area secondaria, è possibile progettare le applicazioni in modo che si spostino senza problemi alla lettura dei dati dall'area secondaria se l'area primaria non è più disponibile per qualsiasi motivo. L'area secondaria è sempre disponibile per l'accesso in lettura, pertanto è possibile testare l'applicazione per assicurarsi che venga letta dal database secondario in caso di interruzione. Per ulteriori informazioni su come progettare le applicazioni per la disponibilità elevata, vedere Progettazione di [applicazioni a disponibilità elevata utilizzando l'archiviazione con ridondanza geografica di accesso](storage-designing-ha-apps-with-ragrs.md)in lettura .

Quando l'accesso in lettura al database secondario è abilitato, i dati possono essere letti dall'endpoint secondario e dall'endpoint primario per l'account di archiviazione. L'endpoint secondario aggiunge il suffisso *–secondario* al nome dell'account. Ad esempio, se l'endpoint `myaccount.blob.core.windows.net`primario per l'archiviazione BLOB è , l'endpoint secondario è `myaccount-secondary.blob.core.windows.net`. Le chiavi di accesso all'account di archiviazione sono le stesse per gli endpoint primario e secondario.

### <a name="check-the-last-sync-time-property"></a>Controllare la proprietà Ora ultima sincronizzazione

Poiché i dati vengono replicati nell'area secondaria in modo asincrono, l'area secondaria si trova spesso dietro l'area primaria. Se si verifica un errore nell'area primaria, è probabile che tutte le scritture nel database primario non siano ancora state replicate nel database secondario.

Per determinare quali operazioni di scrittura sono state replicate nell'area secondaria, l'applicazione può controllare la proprietà **Ora ultima sincronizzazione** per l'account di archiviazione. Tutte le operazioni di scrittura scritte nell'area primaria prima dell'ora dell'ultima sincronizzazione sono state replicate correttamente nell'area secondaria, ovvero sono disponibili per la lettura dal database secondario. Tutte le operazioni di scrittura scritte nell'area primaria dopo l'ora dell'ultima sincronizzazione possono essere state replicate o meno nell'area secondaria, il che significa che potrebbero non essere disponibili per le operazioni di lettura.

È possibile eseguire una query sul valore della proprietà **Ultima sincronizzazione** usando Azure PowerShell, l'interfaccia della riga di comando di Azure o una delle librerie client di Archiviazione di Azure.You can query the value of the Last Sync Time property using Azure PowerShell, Azure CLI, or one of the Azure Storage client libraries. La proprietà **Ora ultima sincronizzazione** è un valore di data/ora GMT. Per altre informazioni, vedere [Controllare la proprietà Ultima sincronizzazione per un account di archiviazione.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>Riepilogo delle opzioni di ridondanza

Nella tabella seguente viene illustrato il periodo di gestione durevole e disponibile dei dati in un determinato scenario, a seconda del tipo di ridondanza attivo per l'account di archiviazione:The following table shows how durable and available your data is in a given scenario, depending on which type of redundancy is in effect for your storage account:

| Scenario                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | GRS/RA-GRS                                  | RS/RA-G-RS (anteprima)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Un nodo all'interno di un data center non è più disponibile                                                                 | Sì                             | Sì                              | Sì                                  | Sì                                  |
| Mancata disponibilità di un intero data center (di zona o non di zona)                                           | No                              | Sì                              | Sì                                  | Sì                                  |
| Si verifica un'interruzione a livello di area                                                                                     | No                              | No                               | Sì                                  | Sì                                  |
| Accesso in lettura ai dati nell'area secondaria se l'area primaria non è più disponibileRead access to data in the secondary region if the primary region becomes unavailable | No                              | No                               | Sì (con RA-GRS)                                   | Sì (con RA-G-RS)                                 |
| Durata percento degli oggetti in un determinato anno<sup>1</sup>                                          | Almeno 99,999999999% (11 9) | Almeno 99,9999999999% (12 9) | Almeno 99,99999999999999% (16 9) | Almeno 99,99999999999999% (16 9) |
| Tipi di account di archiviazione supportati<sup>2Supported</sup> storage account types 2                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, Archiviazione BLOB                     | GPv2                     |
| SLA di disponibilità per le richieste di lettura<sup>1Availability</sup> SLA for read requests 1  | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno il 99,9% (99% per il livello di accesso<br /><br />Almeno il 99,99% (99,9% per il livello di accesso fresco) per RA-GRS | Almeno il 99,9% (99% per il livello di accesso cool) per<br /><br />Almeno il 99,99% (99,9% per il livello di accesso automatico) per RA-G-RS |
| SLA di disponibilità per le richieste di scrittura<sup>1Availability</sup> SLA for write requests 1  | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) |

<sup>1</sup> Per informazioni sulle garanzie di Archiviazione di Azure per la durabilità e la disponibilità, vedere il servizio di archiviazione di [Azure.](https://azure.microsoft.com/support/legal/sla/storage/)

<sup>2</sup> Per informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione](storage-account-overview.md).

Tutti i dati per tutti i tipi di account di archiviazione vengono copiati in base all'opzione di ridondanza per l'account di archiviazione. Vengono copiati oggetti, tra cui BLOB di blocchi, BLOB di aggiunta, BLOB di pagine, code, tabelle e file.

Per informazioni sui prezzi per ogni opzione di ridondanza, vedere Prezzi di Archiviazione di Azure.For pricing information for each redundancy option, see [Azure Storage pricing.](https://azure.microsoft.com/pricing/details/storage/)

> [!NOTE]
> Archiviazione su disco Premium di Azure attualmente supporta solo l'archiviazione con ridondanza locale (LRS). Gli account di archiviazione BLOB a blocchi supportano l'archiviazione con ridondanza locale (LRS) e l'archiviazione con ridondanza di zona in determinate aree.

## <a name="data-integrity"></a>Integrità dei dati

Archiviazione di Azure verifica regolarmente l'integrità dei dati archiviati utilizzando i controlli di ridondanza ciclici (CRC). Se viene rilevato un danneggiamento dei dati, questi vengono riparati utilizzando dati ridondanti. Archiviazione di Azure calcola anche i checksum su tutto il traffico di rete per rilevare il danneggiamento dei pacchetti di dati durante l'archiviazione o il recupero dei dati.

## <a name="see-also"></a>Vedere anche

- [Controllare la proprietà Ora ultima sincronizzazione per un account di archiviazioneCheck the Last Sync Time property for a storage account](last-sync-time-get.md)
- [Modificare l'opzione di ridondanza per un account di archiviazioneChange the redundancy option for a storage account](redundancy-migration.md)
- [Progettazione di applicazioni a disponibilità elevata utilizzando RA-GRS Storage](../storage-designing-ha-apps-with-ragrs.md)
- [Ripristino di emergenza e failover dell'account (anteprima)Disaster recovery and account failover (preview)](storage-disaster-recovery-guidance.md)
