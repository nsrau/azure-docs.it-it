---
title: Ridondanza dei dati
titleSuffix: Azure Storage
description: I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le configurazioni della ridondanza includono archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), archiviazione con ridondanza geografica della zona (GZRS) e archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 9502194b2020723801469b511f46d3e806290ba5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213993"
---
# <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure

Archiviazione di Azure archivia sempre più copie dei dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. La ridondanza garantisce che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori.

Quando si decide quale opzione di ridondanza è più adatta allo scenario in uso, considerare il rapporto tra costi inferiori, maggiore disponibilità e durabilità. Ecco i fattori che consentono di determinare l'opzione di ridondanza da scegliere:  

- Modalità di replica dei dati nell'area primaria
- Se i dati vengono replicati in una seconda posizione geograficamente distante rispetto all'area primaria, per proteggerli da emergenze locali
- Se l'applicazione richiede l'accesso in lettura ai dati replicati nell'area secondaria se l'area primaria, per un qualsiasi motivo, non è più disponibile

## <a name="redundancy-in-the-primary-region"></a>Ridondanza nell'area primaria

I dati in un account di Archiviazione di Azure vengono sempre replicati tre volte nell'area primaria. Archiviazione di Azure offre due opzioni per la replica dei dati nell'area primaria:

- L'**archiviazione con ridondanza locale (LRS)** copia i dati in modo sincrono tre volte all'interno di un'unica posizione fisica nell'area primaria. L'archiviazione con ridondanza locale è l'opzione di replica meno costosa, ma non è consigliata per le applicazioni che richiedono una disponibilità elevata.
- L'**archiviazione con ridondanza della zona (ZRS)** copia i dati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria. Per le applicazioni che richiedono la disponibilità elevata, è consigliabile usare ZRS nell'area primaria oltre a eseguire la replica in un'area secondaria.

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale

L'archiviazione con ridondanza locale (LRS) replica i dati tre volte all'interno di un'unica posizione fisica nell'area primaria. L'archiviazione con ridondanza locale garantisce almeno il 99,999999999% (11 nove) di durabilità degli oggetti nell'arco di un anno specifico.

L'archiviazione con ridondanza locale è l'opzione di ridondanza più economica e offre una durabilità inferiore rispetto alle altre opzioni. L'archiviazione con ridondanza locale protegge i dati dagli errori del rack di server e delle unità. Tuttavia, se all'interno del data center si verifica un'emergenza come, ad esempio un incendio o un alluvione, tutte le repliche dell'account di archiviazione che usano l'archiviazione con ridondanza locale potrebbero essere perse o irrecuperabili. Per attenuare questo rischio, è consigliabile usare l'[archiviazione con ridondanza della zona](#zone-redundant-storage) (ZRS), l'[archiviazione con ridondanza geografica](#geo-redundant-storage) (GRS) o l'[archiviazione con ridondanza geografica della zona](#geo-zone-redundant-storage) (GZRS).

Una richiesta di scrittura in un account di archiviazione che usa l'archiviazione con ridondanza locale viene eseguita in modo sincrono. L'operazione di scrittura viene restituita correttamente solo dopo che i dati sono stati scritti in tutte e tre le repliche.

L'archiviazione con ridondanza locale è una scelta ottimale per gli scenari seguenti:

- Se l'applicazione archivia dati che possono essere ricostruiti facilmente in caso di perdita, è possibile scegliere l'archiviazione con ridondanza locale.
- Se l'applicazione è limitata alla sola replica dei dati che si trovano all'interno di un paese o di un'area a causa di requisiti di governance dei dati, è possibile scegliere l'archiviazione con ridondanza locale. In alcuni casi, le aree associate tra le quali per i dati viene eseguita una replica geografica possono trovarsi in un altro paese o in un'altra area. Per altre informazioni sulle aree abbinate, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona

L'archiviazione con ridondanza della zona (ZRS) replica i dati di Archiviazione di Azure in modo sincrono in tre zone di disponibilità di Azure nell'area primaria. Ogni zona di disponibilità è una posizione fisica separata con alimentazione, raffreddamento e rete indipendenti. L'archiviazione con ridondanza della zona offre almeno il 99,9999999999% (12 nove) di durabilità degli oggetti di Archiviazione di Azure nell'arco di un anno specifico.

Con l'archiviazione con ridondanza della zona i dati sono ancora accessibili per le operazioni di lettura e scrittura anche se una zona non è più disponibile. Quando una zona non è disponibile, Azure avvia gli aggiornamenti di rete, ad esempio la modifica del puntamento DNS. Questi aggiornamenti possono interessare l'applicazione se l'utente accede ai dati prima che gli aggiornamenti siano stati completati. Quando si progettano le applicazioni per l'archiviazione con ridondanza della zona, è consigliabile seguire le procedure per la gestione degli errori temporanei, tra cui l'implementazione dei criteri di ripetizione con backoff esponenziale.

Una richiesta di scrittura in un account di archiviazione che usa l'archiviazione con ridondanza della zona viene eseguita in modo sincrono. L'operazione di scrittura viene restituita correttamente solo dopo che i dati sono stati scritti in tutte le repliche nelle tre zone di disponibilità.

È consigliabile usare l'archiviazione con ridondanza della zona nell'area primaria per scenari che richiedono coerenza, durabilità e disponibilità elevata. L'archiviazione con ridondanza della zona offre prestazioni ottimali, bassa latenza e resilienza per i dati in caso di temporanea indisponibilità. Tuttavia, l'archiviazione con ridondanza della zona in sé potrebbe non proteggere i dati in caso di un'emergenza a livello di area in cui più zone sono interessate in modo permanente. Per la protezione da emergenze a livello di area, Microsoft consiglia di usare l'[archiviazione con ridondanza geografica della zona](#geo-zone-redundant-storage) (GZRS), che usa l'archiviazione con ridondanza della zona nell'area primaria ed esegue la replica geografica dei dati in un'area secondaria.

La tabella seguente illustra i tipi di account di archiviazione che supportano l'archiviazione con ridondanza della zona e le relative aree:

|    Tipo di account di archiviazione    |    Aree supportate    |    Servizi supportati    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Utilizzo generico v2<sup>1</sup>    | Asia sudorientale<br /> Australia orientale<br /> Europa settentrionale<br />  Europa occidentale<br /> Francia centrale<br /> Giappone orientale<br /> Sudafrica settentrionale<br /> Regno Unito meridionale<br /> Stati Uniti centrali<br /> Stati Uniti orientali<br /> Stati Uniti orientali 2<br /> Stati Uniti occidentali 2    |    BLOB in blocchi<br /> BLOB di pagine<sup>2</sup><br /> Condivisioni file (standard)<br /> Tabelle<br /> Code<br /> |
|    BlockBlobStorage<sup>1</sup>    | Asia sudorientale<br /> Europa occidentale<br /> Stati Uniti orientali    |    Solo BLOB in blocchi    |
|    FileStorage    | Asia sudorientale<br /> Europa occidentale<br /> Stati Uniti orientali    |    Solo File di Azure    |

<sup>1</sup> Il livello di archiviazione non è attualmente supportato per gli account ZRS.<br />
<sup>2</sup> Gli account di archiviazione contenenti i dischi gestiti di Azure per le macchine virtuali usano sempre con l'archiviazione con ridondanza locale. I dischi non gestiti di Azure devono usare anche l'archiviazione con ridondanza locale. È possibile creare un account di archiviazione per i dischi non gestiti di Azure che usa l'archiviazione con ridondanza geografica, ma non è consigliabile a causa di possibili problemi di coerenza della replica geografica asincrona. Né i dischi gestiti e né quelli non gestiti supportano ZRS o GZRS. Per altre informazioni sui dischi gestiti, vedere [Prezzi per i dischi gestiti di Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Per informazioni sulle aree che supportano ZRS, vedere **Supporto dei servizi in base all'area** in [Quali sono le zone di disponibilità di Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Ridondanza in un'area secondaria

Per le applicazioni che richiedono disponibilità elevata, è anche possibile scegliere di copiare i dati nell'account di archiviazione in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Se l'account di archiviazione viene copiato in un'area secondaria, la durabilità dei dati è assicurata anche in caso di un'interruzione completa locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria associata viene determinata in base all'area primaria e non è possibile modificarla. Per altre informazioni sulle aree supportate da Azure, vedere [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Archiviazione di Azure offre due opzioni per la copia dei dati in un'area secondaria:

- L'**archiviazione con ridondanza geografica (GRS)** copia i dati in modo sincrono tre volte all'interno di un'unica posizione fisica nell'area primaria usando l'archiviazione con ridondanza locale. Copia quindi i dati in modo asincrono in un'unica posizione fisica nell'area secondaria.
- L'**archiviazione con ridondanza geografica della zona (GZRS)** copia i dati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria usando l'archiviazione con ridondanza della zona. Copia quindi i dati in modo asincrono in un'unica posizione fisica nell'area secondaria.

La differenza principale tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica della zona è il modo in cui i dati vengono replicati nell'area primaria. All'interno della posizione secondaria, i dati vengono sempre replicati in modo sincrono tre volte usando l'archiviazione con ridondanza locale. CON ridondanza locale nell'area secondaria protegge i dati da errori hardware.

Con l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica della zona, i dati nella posizione secondaria non sono disponibili per la lettura o la scrittura a meno che non ci sia un failover nell'area secondaria. Per l'accesso in lettura all'area secondaria, configurare l'account di archiviazione per l'uso dell'archiviazione con ridondanza geografica e accesso in lettura(RA-GRS) o l'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS). Per altre informazioni, vedere [Accesso in lettura ai dati nell'area secondaria](#read-access-to-data-in-the-secondary-region).

Se l'area primaria non è più disponibile, è possibile scegliere di effettuare il failover all'area secondaria. Al termine del failover, l'area secondaria diventa l'area primaria ed è di nuovo possibile leggere e scrivere i dati. Per altre informazioni sul ripristino di emergenza e per informazioni su come eseguire il failover nell'area secondaria, vedere [Ripristino di emergenza e failover dell'account di archiviazione](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Poiché i dati vengono replicati in modo asincrono nell'area secondaria, un errore che interessa l'area primaria può provocare la perdita di dati se non è possibile recuperare l'area primaria. L'intervallo tra le scritture più recenti nell'area primaria e l'ultima scrittura nell'area secondaria è noto come obiettivo del punto di ripristino (RPO). e indica il punto nel tempo in cui è possibile recuperare i dati. L'obiettivo del punto di ripristino di Archiviazione di Azure è in genere inferiore a 15 minuti, anche se attualmente non è previsto alcun contratto di servizio sulla durata della replica dei dati nell'area secondaria.

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

L'archiviazione con ridondanza geografica (GRS) copia i dati in modo sincrono tre volte all'interno di un'unica posizione fisica nell'area primaria usando l'archiviazione con ridondanza locale. Copia quindi i dati in modo asincrono in un'unica posizione fisica in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. L'archiviazione con ridondanza della zona offre almeno il 99,99999999999999% (16 nove) di durabilità degli oggetti di Archiviazione di Azure nell'arco di un anno specifico.

Prima di tutto, viene eseguito il commit di un'operazione di scrittura nella posizione primaria e viene eseguita la replica con l'archiviazione con ridondanza locale. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale.

### <a name="geo-zone-redundant-storage"></a>Archiviazione con ridondanza della zona geografica

L'archiviazione con ridondanza geografica della zona (GZRS) combina la disponibilità elevata offerta dalla ridondanza tra le zone di disponibilità, con la protezione dalle interruzioni a livello di area fornite dalla replica geografica. I dati in un account di archiviazione GZRS vengono copiati in tre [zone di disponibilità di Azure](../../availability-zones/az-overview.md) nell'area primaria e vengono anche replicati in un'area geografica secondaria per proteggerli dalle emergenze locali. Microsoft consiglia di usare l'archiviazione con ridondanza geografica della zona per le applicazioni che richiedono la massima coerenza, durabilità e disponibilità, prestazioni ottimali e resilienza per il ripristino di emergenza.

Con un account di archiviazione GZRS, se una zona di disponibilità non è più disponibile o recuperabile è possibile continuare a leggere e scrivere i dati. Inoltre, la durabilità dei dati è assicurata anche in caso di un'interruzione completa a livello di area o in situazioni di emergenza in cui l'area primaria non è recuperabile. L'archiviazione con ridondanza geografica della zona è progettata per garantire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti nell'arco di un anno specifico.

Solo gli account di archiviazione per utilizzo generico v2 supportano l'archiviazione con ridondanza geografica della zona e l'archiviazione con ridondanza geografica della zona e accesso in lettura. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md). L'archiviazione con ridondanza geografica della zona e l'archiviazione con ridondanza geografica della zona e accesso in lettura supportano i BLOB in blocchi, i BLOB di pagine, ad eccezione dei dischi rigidi virtuali, i file, le tabelle e le code.

L'archiviazione con ridondanza geografica della zona e l'archiviazione con ridondanza geografica della zona e accesso in lettura sono supportati nelle aree seguenti:

- Asia sudorientale
- Europa settentrionale
- Europa occidentale
- Giappone orientale
- Regno Unito meridionale
- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali 2

Per informazioni sui prezzi, vedere i dettagli sui prezzi per i [BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), i [file](https://azure.microsoft.com/pricing/details/storage/files/), le [code](https://azure.microsoft.com/pricing/details/storage/queues/) e le [tabelle](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="read-access-to-data-in-the-secondary-region"></a>Accesso in lettura ai dati nell'area secondaria

L'archiviazione con ridondanza geografica, con GRS o GZRS, consente di replicare i dati in un'altra posizione fisica dell'area secondaria per proteggerli da interruzioni a livello di area. Tuttavia, i dati possono essere letti solo se il cliente o Microsoft avvia un failover dall'area primaria a quella secondaria. Quando si Abilita l'accesso in lettura all'area secondaria, i dati sono disponibili per la lettura in qualsiasi momento, anche in una situazione in cui l'area primaria diventa non disponibile. Per l'accesso in lettura all'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o l'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Progettare le applicazioni per l'accesso in lettura all'area secondaria

Se l'account di archiviazione è configurato per l'accesso in lettura all'area secondaria, è possibile progettare le applicazioni in modo da passare facilmente alla lettura dei dati dell'area secondaria se l'area primaria, per un qualsiasi motivo, non è più disponibile. 

L'area secondaria è disponibile per l'accesso in lettura dopo l'abilitazione di RA-GRS o RA-GZRS, in modo che sia possibile testare l'applicazione in anticipo per assicurarsi che venga letta correttamente dal database secondario in caso di interruzione. Per altre informazioni sulla progettazione di applicazioni con disponibilità elevata, vedere [Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata](geo-redundant-design.md).

Quando è abilitato l'accesso in lettura al database secondario, l'applicazione può essere letta dall'endpoint secondario e dall'endpoint primario. L'endpoint secondario accoda il suffisso *–secondary* al nome dell'account. Se ad esempio l'endpoint primario per l'archiviazione BLOB è `myaccount.blob.core.windows.net`, l'endpoint secondario sarà `myaccount-secondary.blob.core.windows.net`. Le chiavi di accesso per l'account di archiviazione sono identiche per gli endpoint primario e secondario.

### <a name="check-the-last-sync-time-property"></a>Controllare la proprietà Ora ultima sincronizzazione

Poiché i dati vengono replicati in modo asincrono nell'area secondaria, questa è spesso dietro l'area primaria. Se si verifica un errore nell'area primaria, è probabile che tutte le scritture nell'area primaria non siano ancora state replicate nell'area secondaria.

Per determinare quali operazioni di scrittura sono state replicate nell'area secondaria, l'applicazione può verificare la proprietà **Last Sync Time** per l'account di archiviazione. Tutte le operazioni di scrittura eseguite nell'area primaria precedenti all'ora dell'ultima sincronizzazione sono state replicate correttamente nell'area secondaria, vale a dire che sono disponibili per la lettura dall'area secondaria. Un'operazione di scrittura eseguita nell'area primaria dopo l'ora dell'ultima sincronizzazione potrebbe o meno essere stata replicata correttamente nell'area secondaria, vale a dire che potrebbe non essere disponibile per le operazioni di lettura.

È possibile eseguire una query sul valore della proprietà **Last Sync Time** usando Azure PowerShell, l'interfaccia della riga di comando di Azure o una delle librerie client di Archiviazione di Azure. La proprietà **Last Sync Time** è un valore di data/ora GMT. Per altre informazioni, vedere [Controllare la proprietà Last Sync Time per un account di archiviazione](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Riepilogo delle opzioni di ridondanza

Le tabelle nelle sezioni seguenti riepilogano le opzioni di ridondanza disponibili per Archiviazione di Azure

### <a name="durability-and-availability-parameters"></a>Parametri di durabilità e disponibilità

La tabella seguente descrive i principali parametri per ogni opzione di ridondanza:

| Parametro                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Percentuale di durabilità degli oggetti nel corso di un determinato anno<sup>1</sup>                                          | Almeno 99,999999999% (11 9) | Almeno 99,9999999999% (12 9) | Almeno 99,99999999999999% (16 9) | Almeno 99,99999999999999% (16 9) |
| Contratto di servizio relativo alla disponibilità per le richieste di lettura<sup>1</sup>  | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) per GRS<br /><br />Almeno 99,99% (99,9% per livello di accesso sporadico) per RA-GRS | Almeno 99,9% (99% per livello di accesso sporadico) per GZRS<br /><br />Almeno 99,99% (99,9% per livello di accesso sporadico) per RA-GZRS |
| Contratto di servizio relativo alla disponibilità per le richieste di scrittura<sup>1</sup>  | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) | Almeno 99,9% (99% per livello di accesso sporadico) |

[1](https://azure.microsoft.com/support/legal/sla/storage/)Per informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure, vedere il <sup>Contratto di servizio di Archiviazione di Azure</sup>.

### <a name="durability-and-availability-by-outage-scenario"></a>Durabilità e disponibilità in base allo scenario di interruzione

La tabella seguente indica se viene garantita la durabilità e la disponibilità dei dati in un determinato scenario, a seconda del tipo di ridondanza attivo per l'account di archiviazione:

| Scenario di interruzione                                                                                                 | Archiviazione con ridondanza locale                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Un nodo all'interno di un data center non è più disponibile                                                                 | Sì                             | Sì                              | Sì                                  | Sì                                 |
| Mancata disponibilità di un intero data center (di zona o non di zona)                                           | No                              | Sì                              | Sì<sup>1</sup>                                  | Sì                                  |
| Nell'area primaria si verifica un'interruzione a livello di area                                                                                     | No                              | No                               | Sì<sup>1</sup>                                  | Sì<sup>1</sup>                                  |
| L'accesso in lettura all'area secondaria è disponibile se l'area primaria non è più disponibile | No                              | No                               | Sì (con RA-GRS)                                   | Sì (con RA-GZRS)                                 |

<sup>1</sup> Il failover dell'account è necessario per ripristinare la disponibilità di scrittura se l'area primaria non è più disponibile. Per altre informazioni, vedere [Ripristino di emergenza e failover dell'account di archiviazione](storage-disaster-recovery-guidance.md).

### <a name="supported-storage-account-types"></a>Tipi di account di archiviazione supportati

La tabella seguente illustra le opzioni di ridondanza supportate per ogni tipo di account di archiviazione. Per informazioni sui tipi di account di archiviazione, vedere la [panoramica degli account di archiviazione](storage-account-overview.md).

| Archiviazione con ridondanza locale                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                              |
| :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Utilizzo generico v2<br /> Utilizzo generico v1<br /> Archiviazione BLOB in blocchi<br /> Archiviazione BLOB<br /> Archiviazione file                | Utilizzo generico v2<br /> Archiviazione BLOB in blocchi<br /> Archiviazione file                             | Utilizzo generico v2<br /> Utilizzo generico v1<br /> Archiviazione BLOB                     | Utilizzo generico v2                     |

Tutti i dati per gli account di archiviazione vengono copiati in base all'opzione di ridondanza per l'account di archiviazione. Vengono copiati gli oggetti tra cui BLOB in blocchi, BLOB di aggiunta, BLOB di pagine, code, tabelle e file. Vengono copiati i dati in tutti i livelli, tra cui il livello di archiviazione. Per altre informazioni sui livelli di BLOB, vedere [Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](../blobs/storage-blob-storage-tiers.md).

Per informazioni sui prezzi delle varie opzioni di ridondanza, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> L'archiviazione Premium su disco di Azure attualmente supporta solo l'archiviazione con ridondanza locale (LRS). Gli account di archiviazione BLOB in blocchi supportano l'archiviazione con ridondanza locale (LRS) e l'archiviazione con ridondanza della zona (ZRS) in determinate aree.

## <a name="data-integrity"></a>Integrità dei dati

Archiviazione di Azure verifica regolarmente l'integrità dei dati archiviati usando i controlli di ridondanza ciclici (CRC). Se viene rilevato un danneggiamento dei dati, questo viene ripristinato usando i dati ridondanti. Archiviazione di Azure calcola anche i checksum su tutto il traffico di rete per rilevare il danneggiamento dei pacchetti di dati durante l'archiviazione o il recupero dei dati.

## <a name="see-also"></a>Vedere anche

- [Controllare la proprietà Last Sync Time per un account di archiviazione](last-sync-time-get.md)
- [Modificare l'opzione di ridondanza per un account di archiviazione](redundancy-migration.md)
- [Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata](geo-redundant-design.md)
- [Ripristino di emergenza e failover dell'account di archiviazione](storage-disaster-recovery-guidance.md)
