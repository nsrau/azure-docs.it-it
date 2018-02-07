---
title: Replica dei dati in Archiviazione di Azure | Documentazione Microsoft
description: "I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le opzioni di replica includono archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: a8a8d8e95af3e6d98aa4dd98b11c066dca81421b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="azure-storage-replication"></a>Replica di Archiviazione di Azure

I dati nell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne la durabilità e la disponibilità elevata. La replica copia i dati in modo che siano protetti da errori hardware temporanei, mantenendo il tempo di attività dell'applicazione. 

È possibile scegliere di replicare i dati nello stesso data center, tra data center nella stessa area o tra aree. Se i dati vengono replicati tra più data center o tra aree, vengono anche protetti da un errore irreversibile in una singola località.

Garantisce infine che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Altre informazioni sulla garanzia di durabilità e disponibilità di Archiviazione di Azure sono reperibili nel Contratto di servizio.

Quando si crea un account di archiviazione, è possibile selezionare una delle opzioni di replica seguenti:

* [Archiviazione con ridondanza locale (LRS)](#locally-redundant-storage)
* [Archiviazione con ridondanza della zona (ZRS).](#zone-redundant-storage)
* [Archiviazione con ridondanza geografica (GRS)](#geo-redundant-storage)
* [Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).](#read-access-geo-redundant-storage)

L'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) è l'opzione predefinita quando si crea un account di archiviazione.

La tabella seguente fornisce una rapida panoramica delle differenze tra LRS, ZRS, GRS e RA-GRS. Le sezioni successive di questo articolo illustrano più in dettaglio ogni tipo di replica.

| Strategia di replica | Archiviazione con ridondanza locale | ZRS | Archiviazione con ridondanza geografica | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| I dati vengono replicati in più data center. |No  |Sì |Sì |Sì |
| I dati possono essere letti da una posizione secondaria oltre che da quella primaria. |No  |No  |No  |Sì |
| Progettata per fornire la durabilità degli oggetti nel corso di un determinato anno. |Almeno 99,999999999% (11 9)|Almeno 99,9999999999% (12 9)|Almeno 99,99999999999999% (16 9)|Almeno 99,99999999999999% (16 9)|

Per informazioni sui prezzi delle varie opzioni di ridondanza, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

> [!NOTE]
> Archiviazione Premium supporta solo l'archiviazione con ridondanza locale. Per informazioni su Archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona

L'archiviazione con ridondanza della zona (ZRS) (anteprima) è progettata per semplificare lo sviluppo di applicazioni a disponibilità elevata. Offre almeno il 99,9999999999% (12 9) di durabilità degli oggetti di archiviazione nell'arco di un anno. Replica i dati in modo sincrono in più zone di disponibilità. Valutare l'uso dell'archiviazione con ridondanza della zona per scenari, quali quelli delle applicazioni transazionali, in cui i tempi di inattività non sono accettabili.

L'archiviazione con ridondanza della zona consente ai clienti di leggere e scrivere dati anche se una singola zona non è disponibile o non può essere ripristinata. Gli inserimenti e gli aggiornamenti dei dati vengono eseguiti in modo sincrono e sono notevolmente coerenti.   

L'archiviazione con ridondanza della zona è attualmente disponibile in anteprima nelle aree seguenti e altre aree saranno presto disponibili:

- Stati Uniti orientali 2 
- Stati Uniti centrali 
- Francia centrale. Quest'area è attualmente disponibile in anteprima. Per richiedere l'accesso, vedere [Microsoft Azure preview with Azure Availability Zones now open in France](https://azure.microsoft.com/blog/microsoft-azure-preview-with-azure-availability-zones-now-open-in-france) (Anteprima di Microsoft Azure con Zone di disponibilità di Azure ora accessibile in Francia).

### <a name="zrs-classic-accounts"></a>Account della versione classica dell'archiviazione con ridondanza della zona

La funzionalità esistente di archiviazione con ridondanza della zona è ora nota come versione classica dell'archiviazione con ridondanza della zona. Gli account della versione classica dell'archiviazione con ridondanza della zona sono disponibili solo per i BLOB in blocchi negli account di archiviazione per utilizzo generico V1. 

La versione classica dell'archiviazione con ridondanza della zona replica i dati in modo asincrono nei data center in una o due aree. Una replica potrebbe non essere disponibile a meno che Microsoft non avvii il failover all'area secondaria. 

Gli account della versione classica dell'archiviazione con ridondanza della zona non possono essere convertiti in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura. Gli account della versione classica dell'archiviazione con ridondanza della zona non supportano inoltre le metriche o la registrazione.   

Quando l'archiviazione con ridondanza della zona sarà disponibile a livello generale in un'area, non sarà più possibile creare un account della versione classica dell'archiviazione con ridondanza della zona dal portale in tale area, ma sarà possibile crearne uno in altro modo.  
In futuro sarà disponibile un processo automatizzato di migrazione dalla versione classica dell'archiviazione con ridondanza della zona all'archiviazione con ridondanza della zona.

Gli account di archiviazione con ridondanza della zona supportano la migrazione manuale di un account di archiviazione con ridondanza della zona in tale area a o da un account di archiviazione con ridondanza locale, di archiviazione con ridondanza geografica o di archiviazione con ridondanza geografica e accesso in lettura. Per eseguire questa migrazione manuale, è possibile usare AzCopy, Azure Storage Explorer, Azure PowerShell, l'interfaccia della riga di comando di Azure o una delle librerie client di Archiviazione di Azure.

> [!NOTE]
> Gli account della versione classica dell'archiviazione con ridondanza della zona in futuro saranno deprecati e sarà necessario eseguirne la migrazione il 31 marzo 2021. Microsoft invierà altri dettagli ai clienti della versione classica dell'archiviazione con ridondanza della zona prima che siano deprecati.

Altre domande e le relative risposte sono elencate nella sezione [Domande frequenti](#frequently-asked-questions). 

## <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Archiviazione con ridondanza geografica e accesso in lettura
L'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) ottimizza la disponibilità per l'account di archiviazione. L'archiviazione con ridondanza geografica e accesso in lettura fornisce l'accesso in sola lettura ai dati nella località secondaria, oltre alla replica geografica tra due aree.

Se si abilita l'accesso in sola lettura ai dati nell'area secondaria, i dati saranno disponibili in un endpoint secondario, oltre che nell'endpoint primario dell'account di archiviazione. L'endpoint secondario è simile a quello primario, ma al nome dell'account viene aggiunto il suffisso `–secondary` . Se ad esempio l'endpoint primario per il servizio BLOB è `myaccount.blob.core.windows.net`, l'endpoint secondario sarà `myaccount-secondary.blob.core.windows.net`. Le chiavi di accesso per l'account di archiviazione sono identiche per gli endpoint primario e secondario.

Alcune considerazioni da ricordare quando si usa l'archiviazione con ridondanza geografica e accesso in lettura:

* L'applicazione deve gestire l'endpoint con cui interagire quando usa l'archiviazione con ridondanza geografica e accesso in lettura.
* Poiché la replica asincrona implica un ritardo, è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria, ad esempio in caso di un'emergenza locale.
* Se Microsoft avvia un failover nell'area secondaria, al termine del failover si avrà accesso in lettura e scrittura a tali dati. Per altre informazioni, vedere [Indicazioni sul ripristino di emergenza](../storage-disaster-recovery-guidance.md).
* L'archiviazione con ridondanza geografica e accesso in lettura è pensata per rispondere a requisiti di disponibilità elevata. Per indicazioni sulla scalabilità, vedere l'[elenco di controllo delle prestazioni](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Come si può modificare il tipo di replica geografica dell'account di archiviazione?

   Si può modificare il tipo di replica geografica dell'account di archiviazione usando il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md) o una delle librerie client di Archiviazione di Azure.

   > [!NOTE]
   > Gli account di archiviazione con ridondanza della zona non possono essere convertiti in account di archiviazione con ridondanza locale o di archiviazione con ridondanza geografica. Allo stesso modo, non è possibile convertire un account di archiviazione con ridondanza locale o di archiviazione con ridondanza geografica in un account di archiviazione con ridondanza della zona.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. La modifica del tipo di replica dell'account di archiviazione comporta un tempo di inattività?

   No, la modifica del tipo di replica dell'account di archiviazione non comporta un tempo di inattività.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. Sono previsti costi aggiuntivi per cambiare il tipo di replica dell'account di archiviazione?

   Sì. Il passaggio dall'archiviazione con ridondanza locale all'archiviazione con ridondanza geografica (o con ridondanza geografica e accesso in lettura) per l'account di archiviazione comporta un addebito aggiuntivo sul traffico in uscita associato alla copia dei dati esistenti dalla località primaria alla località secondaria. Dopo la copia dei dati iniziali non sono previsti ulteriori addebiti in uscita per la replica geografica dalla località primaria a quella secondaria. Per informazioni dettagliate sui costi addebitati per la larghezza di banda, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Se si passa dall'archiviazione con ridondanza geografica all'archiviazione con ridondanza locale non sono previsti costi aggiuntivi, ma i dati vengono eliminati dalla località secondaria.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Qual è l'utilità di RA-GRS?

   Con l'archiviazione con ridondanza geografica i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Con l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile. L'archiviazione con ridondanza geografica e accesso in lettura offre la replica dell'archiviazione con ridondanza geografica e, oltre a questo, la possibilità di leggere i dati dalla località secondaria. Per suggerimenti su come progettare la disponibilità elevata, vedere [Progettazione di applicazioni a disponibilità elevata con l'archiviazione con ridondanza geografica e accesso in lettura](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Esiste un modo per determinare il tempo necessario per replicare i dati dall'area primaria a quella secondaria?

   Se si usa l'archiviazione RA-GRS, è possibile controllare l'ora dell'ultima sincronizzazione dell'account di archiviazione. L'ora dell'ultima sincronizzazione è un valore di data/ora GMT. Tutte le scritture nell'area primaria precedenti all'ora dell'ultima sincronizzazione sono state scritte correttamente nella località secondaria, vale a dire che sono disponibili per la lettura dalla località secondaria. Le scritture nell'area primaria successive all'ora dell'ultima sincronizzazione possono essere o meno già disponibili per la lettura. È possibile eseguire query su questo valore usando il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md) o una delle librerie client di Archiviazione di Azure.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. Se si verifica un'interruzione nell'area primaria, come si passa all'area secondaria?

   Per altre informazioni, vedere [Cosa fare se si verifica un'interruzione di Archiviazione di Azure](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Quali sono gli obiettivi RPO e RTO con l'archiviazione con ridondanza geografica?

   **Obiettivo del punto di ripristino (RPO)**: nel caso dell'archiviazione con ridondanza geografica e dell'archiviazione con ridondanza geografica e accesso in lettura, il servizio di archiviazione esegue la replica geografica asincrona dalla località primaria a quella secondaria. Nell'eventualità di una grave emergenza a livello locale nell'area primaria, Microsoft esegue un failover all'area secondaria. Se viene eseguito un failover, le modifiche recenti non ancora replicate geograficamente potrebbero andare perse. Il numero di minuti di potenziale perdita di dati è detto obiettivo del punto di ripristino e indica il punto nel tempo in cui è possibile ripristinare i dati. L'obiettivo del punto di ripristino di Archiviazione di Azure è in genere inferiore a 15 minuti, anche se attualmente non è previsto alcun contratto di servizio sulla durata della replica geografica.

   **Obiettivo del tempo di ripristino:** l'obiettivo del tempo di ripristino è una misura del tempo necessario per eseguire il failover e riportare online l'account di archiviazione. Il tempo necessario per eseguire il failover include le azioni seguenti:

   * Tempo necessario a Microsoft per determinare se i dati possono essere recuperati nella località primaria o se è necessario un failover.
   * Tempo per eseguire il failover dell'account di archiviazione mediante modifica delle voci relative al DNS primario in modo che puntino alla località secondaria.

   Microsoft prende seriamente la responsabilità di salvaguardare i dati degli utenti. Se esiste la possibilità di recuperare i dati nell'area primaria, Microsoft rimanderà il failover e si concentrerà sul recupero dei dati. In una versione futura del servizio sarà possibile attivare un failover a livello di account per poter controllare personalmente l'obiettivo del tempo di ripristino.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. Quali oggetti di Archiviazione di Azure supportano l'archiviazione con ridondanza della zona? 
I BLOB in blocchi, i BLOB di pagine (a eccezione di quelli che supportano i dischi delle VM), le tabelle, i file e le code. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. L'archiviazione con ridondanza della zona include anche la replica geografica? 
L'archiviazione con ridondanza della zona attualmente non supporta la replica geografica. Se lo scenario richiede la replica tra aree ai fini del ripristino di emergenza, usare invece un account di archiviazione con ridondanza geografica o con ridondanza geografica e accesso in lettura.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. Cosa accade quando una o più zone di archiviazione con ridondanza della zona sono inattive? 
Quando la prima zona diventa inattiva, l'archiviazione con ridondanza della zona continua a scrivere le repliche dei dati nelle altre due zone dell'area. Se un'altra zona diventa inattiva, l'accesso in lettura e scrittura non è disponibile finché almeno due zone non sono di nuovo disponibili. 

## <a name="next-steps"></a>Passaggi successivi
* [Progettazione di applicazioni a disponibilità elevata con archiviazione RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
* [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md)
* [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md)
* [Opzioni di ridondanza di Archiviazione di Microsoft Azure e archiviazione con ridondanza geografica e accesso in lettura ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [Paper SOSP - Archiviazione di Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
