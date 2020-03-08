---
title: Ridondanza dei dati
titleSuffix: Azure Storage
description: I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le configurazioni di ridondanza includono archiviazione con ridondanza locale (con ridondanza locale), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), archiviazione con ridondanza geografica (GZRS) (anteprima) e accesso in lettura archiviazione con ridondanza della zona geografica (RA-GZRS) (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6b36694c2fe1bf264c876944b054d39371db616c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674278"
---
# <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure

Archiviazione di Azure archivia sempre più copie dei dati in modo che siano protette da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e calamità naturali di grandi dimensioni. La ridondanza garantisce che l'account di archiviazione soddisfi il [contratto di servizio (SLA) per archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori.

Quando si decide quale opzione di ridondanza è migliore per lo scenario in uso, considerare i compromessi tra i costi più bassi e la disponibilità e la durabilità più elevate. I fattori che consentono di determinare l'opzione di ridondanza da scegliere includono:  

- Modalità di replica dei dati nell'area primaria
- Se i dati vengono replicati in una seconda posizione geograficamente distante rispetto all'area primaria, per la protezione da emergenze a livello di area
- Indica se l'applicazione richiede l'accesso in lettura ai dati replicati nell'area secondaria se l'area primaria non è più disponibile per qualsiasi motivo

## <a name="redundancy-in-the-primary-region"></a>Ridondanza nell'area primaria

I dati in un account di archiviazione di Azure vengono sempre replicati per tre volte nell'area primaria. Archiviazione di Azure offre due opzioni per la modalità di replica dei dati nell'area primaria:

- **Archiviazione con ridondanza locale (con ridondanza locale)** copia i dati in modo sincrono tre volte all'interno di una singola posizione fisica nell'area primaria. CON ridondanza locale è l'opzione di replica meno costosa, ma non è consigliata per le applicazioni che richiedono una disponibilità elevata.
- **Archiviazione con ridondanza della zona (ZRS)** copia i dati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria. Per le applicazioni che richiedono la disponibilità elevata, è consigliabile usare ZRS nell'area primaria e anche la replica in un'area secondaria.

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale

Archiviazione con ridondanza locale (con ridondanza locale) replica i dati tre volte all'interno di una singola posizione fisica nell'area primaria. CON ridondanza locale offre almeno il 99,999999999% (11 nove) di durabilità degli oggetti nel corso di un determinato anno.

CON ridondanza locale è l'opzione di ridondanza a costo più basso e offre la durabilità minima rispetto ad altre opzioni. CON ridondanza locale protegge i dati dagli errori del rack server e delle unità. Tuttavia, se si verifica una situazione di emergenza, ad esempio incendi o inondazioni, all'interno del data center, tutte le repliche di un account di archiviazione che usano con ridondanza locale potrebbero essere perse o irrecuperabili. Per attenuare questo rischio, Microsoft consiglia di usare l'archiviazione con ridondanza della [zona](#zone-redundant-storage) (ZRS), l' [archiviazione con ridondanza geografica](#geo-redundant-storage) (GRS) o l' [archiviazione con ridondanza della zona geografica (anteprima)](#geo-zone-redundant-storage-preview) (GZRS).

Una richiesta di scrittura a un account di archiviazione che usa con ridondanza locale viene eseguita in modo sincrono. L'operazione di scrittura viene restituita correttamente solo dopo che i dati sono stati scritti in tutte e tre le repliche.

CON ridondanza locale è una scelta ottimale per gli scenari seguenti:

- Se l'applicazione archivia dati che possono essere ricostruiti facilmente in caso di perdita, è possibile scegliere l'archiviazione con ridondanza locale.
- Se l'applicazione è limitata a replicare i dati solo all'interno di un paese o di una regione a causa dei requisiti di governance dei dati, è possibile optare per con ridondanza locale. In alcuni casi, le aree abbinate in cui viene eseguita la replica geografica dei dati possono trovarsi in un altro paese o area geografica. Per altre informazioni sulle aree abbinate, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona

Archiviazione con ridondanza della zona (ZRS) replica i dati di archiviazione di Azure in modo sincrono in tre zone di disponibilità di Azure nell'area primaria. Ogni zona di disponibilità è una posizione fisica separata con alimentazione, raffreddamento e rete indipendenti. ZRS offre durabilità per gli oggetti dati di archiviazione di Azure di almeno 99,9999999999% (12 9) in un determinato anno.

Con ZRS, i dati sono ancora accessibili per le operazioni di lettura e scrittura anche se una zona diventa non disponibile. Se una zona non è più disponibile, Azure sottomette gli aggiornamenti di rete, ad esempio il reindirizzamento DNS. Questi aggiornamenti possono influire sull'applicazione se si accede ai dati prima del completamento degli aggiornamenti. Quando si progettano applicazioni per ZRS, seguire le procedure per la gestione degli errori temporanei, inclusa l'implementazione di criteri di ripetizione con il backup esponenziale.

Una richiesta di scrittura a un account di archiviazione che usa ZRS viene eseguita in modo sincrono. L'operazione di scrittura viene restituita correttamente solo dopo che i dati sono stati scritti in tutte le repliche nelle tre zone di disponibilità.

Microsoft consiglia di usare ZRS nell'area primaria per scenari che richiedono coerenza, durabilità e disponibilità elevata. ZRS offre prestazioni ottimali, bassa latenza e resilienza per i dati se diventa temporaneamente non disponibile. Tuttavia, ZRS può non proteggere i dati da un'emergenza a livello di area in cui più zone sono influiscono in modo permanente. Per la protezione contro le emergenze a livello di area, Microsoft consiglia di usare l' [archiviazione con ridondanza geografica](#geo-zone-redundant-storage-preview) (GZRS), che usa ZRS nell'area primaria e anche la replica geografica dei dati in un'area secondaria.

La tabella seguente illustra i tipi di account di archiviazione che supportano ZRS in quali aree:

|    Tipo di account di archiviazione    |    Aree supportate    |    Servizi supportati    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Utilizzo generico v2<sup>1</sup>    | Asia sudorientale<br /> Europa settentrionale<br />  Europa occidentale<br /> Francia centrale<br /> Giappone orientale<br /> Regno Unito meridionale<br /> Stati Uniti centrali<br /> Stati Uniti orientali<br /> Stati Uniti orientali 2<br /> Stati Uniti occidentali 2    |    BLOB in blocchi<br /> BLOB di pagine<sup>2</sup><br /> Condivisioni file (standard)<br /> Tabelle<br /> Code<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa occidentale<br /> Stati Uniti orientali    |    Solo BLOB in blocchi    |
|    FileStorage    | Europa occidentale<br /> Stati Uniti orientali    |    Solo File di Azure    |

<sup>1</sup> il livello archivio non è attualmente supportato per gli account ZRS.<br />
<sup>2</sup> gli account di archiviazione che contengono Azure Managed disks per le macchine virtuali usano sempre con ridondanza locale. I dischi non gestiti di Azure devono anche usare con ridondanza locale. È possibile creare un account di archiviazione per i dischi non gestiti di Azure che usa GRS, ma non è consigliabile a causa di problemi potenziali con la coerenza della replica geografica asincrona. I dischi gestiti e non gestiti non supportano ZRS o GZRS. Per altre informazioni sui dischi gestiti, vedere [prezzi per Azure Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Per informazioni sulle aree che supportano ZRS, vedere **supporto dei servizi in base all'area** in [zone di disponibilità di Azure](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Ridondanza in un'area secondaria

Per le applicazioni che richiedono disponibilità elevata, è possibile scegliere di copiare i dati nell'account di archiviazione in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Se l'account di archiviazione viene copiato in un'area secondaria, i dati sono durevoli anche in caso di un'interruzione a livello di area completa o di un'emergenza in cui l'area primaria non è recuperabile.

L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria associata viene determinata in base all'area primaria e non è possibile modificarla. Per altre informazioni sulle aree supportate da Azure, vedere [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Archiviazione di Azure offre due opzioni per la copia dei dati in un'area secondaria:

- L' **archiviazione con ridondanza geografica (GRS)** copia i dati in modo sincrono tre volte all'interno di una singola posizione fisica nell'area primaria usando con ridondanza locale. Quindi copia i dati in modo asincrono in un'unica posizione fisica nell'area secondaria.
- **Archiviazione con ridondanza della zona geografica (GZRS)** (anteprima) copia i dati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria usando ZRS. Quindi copia i dati in modo asincrono in un'unica posizione fisica nell'area secondaria.

La differenza principale tra GRS e GZRS è il modo in cui i dati vengono replicati nell'area primaria. All'interno della posizione secondaria, i dati vengono sempre replicati in modo sincrono tre volte usando con ridondanza locale.

Con GRS o GZRS, i dati nella posizione secondaria non sono disponibili per l'accesso in lettura o in scrittura, a meno che non esista un failover nell'area secondaria. Per l'accesso in lettura al percorso secondario, configurare l'account di archiviazione in modo che usi l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS). Per altre informazioni, vedere [accesso in lettura ai dati nell'area secondaria](#read-access-to-data-in-the-secondary-region).

Se l'area primaria non è più disponibile, è possibile scegliere di eseguire il failover nell'area secondaria (anteprima). Al termine del failover, l'area secondaria diventa l'area primaria ed è possibile leggere e scrivere nuovamente i dati. Per altre informazioni sul ripristino di emergenza e per informazioni su come eseguire il failover nell'area secondaria, vedere [ripristino di emergenza e failover degli account (anteprima)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Poiché i dati vengono replicati in modo asincrono nell'area secondaria, un errore che interessa l'area primaria può causare la perdita di dati se non è possibile recuperare l'area primaria. L'intervallo tra le scritture più recenti nell'area primaria e l'ultima scrittura nell'area secondaria è noto come obiettivo del punto di ripristino (RPO). e indica il punto nel tempo in cui è possibile recuperare i dati. Archiviazione di Azure ha in genere un RPO di meno di 15 minuti, anche se attualmente non esiste alcun contratto di contratto per il tempo necessario per la replica dei dati nell'area secondaria.

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

L'archiviazione con ridondanza geografica (GRS) copia i dati in modo sincrono tre volte all'interno di una singola posizione fisica nell'area primaria usando con ridondanza locale. Quindi copia i dati in modo asincrono in un'unica posizione fisica in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. GRS offre durabilità per gli oggetti dati di archiviazione di Azure di almeno 99,99999999999999% (16 9) in un determinato anno.

Viene innanzitutto eseguito il commit di un'operazione di scrittura nel percorso primario e la replica viene eseguita utilizzando con ridondanza locale. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale.

### <a name="geo-zone-redundant-storage-preview"></a>Archiviazione con ridondanza della zona geografica (anteprima)

L'archiviazione con ridondanza geografica (GZRS) (anteprima) combina la disponibilità elevata fornita dalla ridondanza tra le zone di disponibilità, con protezione dalle interruzioni a livello di area fornite dalla replica geografica. I dati in un account di archiviazione GZRS vengono copiati in tre [zone di disponibilità di Azure](../../availability-zones/az-overview.md) nell'area primaria e vengono anche replicati in un'area geografica secondaria per la protezione da emergenze a livello di area. Microsoft consiglia di usare GZRS per le applicazioni che richiedono la massima coerenza, durabilità e disponibilità, prestazioni ottimali e resilienza per il ripristino di emergenza.

Con un account di archiviazione GZRS, è possibile continuare a leggere e scrivere dati se una zona di disponibilità diventa non disponibile o non è recuperabile. Inoltre, i dati sono anche durevoli in caso di un'interruzione completa dell'area o di un'emergenza in cui l'area primaria non è recuperabile. GZRS è progettato per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti in un determinato anno.

Solo gli account di archiviazione per utilizzo generico V2 supportano GZRS e RA-GZRS. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md). I BLOB in blocchi, i BLOB di pagine (ad eccezione dei dischi VHD), i file, le tabelle e le code sono supportati da GZRS e RA-GZRS.

GZRS e RA-GZRS sono attualmente disponibili per l'anteprima nelle aree seguenti:

- Asia sudorientale
- Europa settentrionale
- Europa occidentale
- Regno Unito meridionale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centrali

Microsoft continua ad abilitare GZRS e RA-GZRS in altre aree di Azure. Per informazioni sulle aree supportate, vedere regolarmente la pagina [aggiornamenti dei servizi di Azure](https://azure.microsoft.com/updates/) .

Per informazioni sui prezzi di anteprima, vedere prezzi di anteprima di GZRS per [BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), [file](https://azure.microsoft.com/pricing/details/storage/files/), [Code](https://azure.microsoft.com/pricing/details/storage/queues/)e [tabelle](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft consiglia di non usare le funzionalità di anteprima per i carichi di lavoro di produzione.

## <a name="read-access-to-data-in-the-secondary-region"></a>Accesso in lettura ai dati nell'area secondaria

Con l'archiviazione con ridondanza geografica, con GRS o GZRS, i dati vengono replicati in un'altra posizione fisica dell'area secondaria per la protezione da interruzioni a livello di area. Tuttavia, i dati sono disponibili per essere letti solo se il cliente o Microsoft avvia un failover dall'area primaria a quella secondaria. Quando si Abilita l'accesso in lettura all'area secondaria, i dati sono disponibili per la lettura se l'area primaria non è più disponibile. Per l'accesso in lettura all'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Progettare le applicazioni per l'accesso in lettura al database secondario

Se l'account di archiviazione è configurato per l'accesso in lettura all'area secondaria, è possibile progettare le applicazioni in modo da passare facilmente alla lettura dei dati dall'area secondaria se l'area primaria non è più disponibile per qualsiasi motivo. L'area secondaria è sempre disponibile per l'accesso in lettura, quindi è possibile testare l'applicazione per assicurarsi che venga letta dal database secondario in caso di interruzione. Per altre informazioni su come progettare le applicazioni per la disponibilità elevata, vedere Progettazione di applicazioni a disponibilità elevata [tramite l'archiviazione con ridondanza geografica e accesso in lettura](storage-designing-ha-apps-with-ragrs.md).

Quando è abilitato l'accesso in lettura al database secondario, i dati possono essere letti dall'endpoint secondario e dall'endpoint primario per l'account di archiviazione. L'endpoint secondario aggiunge il suffisso *-secondario* al nome dell'account. Ad esempio, se l'endpoint primario per l'archiviazione BLOB è `myaccount.blob.core.windows.net`, l'endpoint secondario viene `myaccount-secondary.blob.core.windows.net`. Le chiavi di accesso dell'account per l'account di archiviazione sono le stesse per gli endpoint primario e secondario.

### <a name="check-the-last-sync-time-property"></a>Controllare la proprietà Ora ultima sincronizzazione

Poiché i dati vengono replicati in modo asincrono nell'area secondaria, l'area secondaria è spesso dietro l'area primaria. Se si verifica un errore nell'area primaria, è probabile che tutte le scritture nel database primario non siano ancora state replicate nel database secondario.

Per determinare quali operazioni di scrittura sono state replicate nell'area secondaria, l'applicazione può controllare la proprietà **ora ultima sincronizzazione** per l'account di archiviazione. Tutte le operazioni di scrittura scritte nell'area primaria precedenti all'ora dell'ultima sincronizzazione sono state replicate correttamente nell'area secondaria, vale a dire che sono disponibili per la lettura dal database secondario. Tutte le operazioni di scrittura scritte nell'area primaria dopo l'ora dell'ultima sincronizzazione potrebbero essere state replicate nell'area secondaria, vale a dire che potrebbero non essere disponibili per le operazioni di lettura.

È possibile eseguire una query sul valore della proprietà dell' **ora dell'ultima sincronizzazione** usando Azure PowerShell, l'interfaccia della riga di comando di Azure o una delle librerie client di archiviazione di Azure. La proprietà **ora ultima sincronizzazione** è un valore di data/ora GMT. Per altre informazioni, vedere [controllare la proprietà dell'ora dell'ultima sincronizzazione per un account di archiviazione](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Riepilogo delle opzioni di ridondanza

La tabella seguente illustra la durabilità e la disponibilità dei dati in un determinato scenario, a seconda del tipo di ridondanza attivo per l'account di archiviazione:

| Scenario                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (anteprima)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Un nodo all'interno di un data center diventa non disponibile                                                                 | Sì                             | Sì                              | Sì                                  | Sì                                  |
| Mancata disponibilità di un intero data center (di zona o non di zona)                                           | No                              | Sì                              | Sì                                  | Sì                                  |
| Si verifica un'interruzione A livello di area                                                                                     | No                              | No                               | Sì                                  | Sì                                  |
| Accesso in lettura ai dati nell'area secondaria se l'area primaria non è più disponibile | No                              | No                               | Sì (con RA-GRS)                                   | Sì (con RA-GZRS)                                 |
| Percentuale di durabilità degli oggetti in un determinato anno<sup>1</sup>                                          | Almeno 99,999999999% (11 9) | Almeno 99,9999999999% (12 9) | Almeno 99,99999999999999% (16 9) | Almeno 99,99999999999999% (16 9) |
| Tipi di account di archiviazione supportati<sup>2</sup>                                                                   | GPv2, utilizzo generico V1, BlockBlobStorage, BlobStorage, filestorage                | GPv2, BlockBlobStorage, filestorage                             | GPv2, utilizzo generico V1, BlobStorage                     | GPv2                     |
| CONTRATTO di disponibilità per richieste di lettura<sup>1</sup>  | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno il 99,9% (99% per il livello di accesso sporadico) per GRS<br /><br />Almeno il 99,99% (99,9% per il livello di accesso sporadico) per RA-GRS | Almeno il 99,9% (99% per il livello di accesso sporadico) per GZRS<br /><br />Almeno il 99,99% (99,9% per il livello di accesso sporadico) per RA-GZRS |
| CONTRATTO di disponibilità per richieste di scrittura<sup>1</sup>  | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) |

<sup>1</sup> per informazioni sulle garanzie per la durabilità e la disponibilità di archiviazione di Azure, vedere il [contratto di Service di archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> per informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione](storage-account-overview.md).

Tutti i dati per tutti i tipi di account di archiviazione vengono copiati in base all'opzione di ridondanza per l'account di archiviazione. Vengono copiati oggetti, inclusi BLOB in blocchi, BLOB di Accodamento, BLOB di pagine, code, tabelle e file.

Per informazioni sui prezzi per ogni opzione di ridondanza, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium archiviazione su disco supporta attualmente solo l'archiviazione con ridondanza locale (con ridondanza locale). Gli account di archiviazione BLOB in blocchi supportano archiviazione con ridondanza locale (con ridondanza locale) e archiviazione con ridondanza della zona (ZRS) in determinate aree.

## <a name="data-integrity"></a>Integrità dei dati

Archiviazione di Azure verifica regolarmente l'integrità dei dati archiviati usando i controlli di ridondanza ciclici (CRC). Se viene rilevato un danneggiamento dei dati, viene ripristinato utilizzando dati ridondanti. Archiviazione di Azure calcola inoltre i checksum su tutto il traffico di rete per rilevare il danneggiamento dei pacchetti di dati durante l'archiviazione o il recupero dei dati.

## <a name="see-also"></a>Vedere anche

- [Controllare la proprietà ora ultima sincronizzazione per un account di archiviazione](last-sync-time-get.md)
- [Modificare l'opzione di ridondanza per un account di archiviazione](redundancy-migration.md)
- [Progettazione di applicazioni a disponibilità elevata con archiviazione RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Ripristino di emergenza e failover dell'account (anteprima)](storage-disaster-recovery-guidance.md)
