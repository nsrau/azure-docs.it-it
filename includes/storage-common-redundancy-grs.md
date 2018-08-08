---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399958"
---
L'archiviazione con ridondanza geografica (GRS) è progettata per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti nell'arco di un anno eseguendo la replica dei dati in un'area secondaria distante centinaia di chilometri dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

Se si opta per l'archiviazione con ridondanza geografica, è possibile scegliere tra due opzioni correlate:

* L'archiviazione con ridondanza geografica replica i dati in un altro data center in un'area secondaria, ma i dati sono disponibili per la lettura solo se Microsoft avvia un failover dall'area primaria a quella secondaria. 
* L'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) è basata sull'archiviazione con ridondanza geografica. L'archiviazione con ridondanza geografica e accesso in lettura replica i dati in un altro data center in un'area secondaria e offre anche la possibilità di lettura dall'area secondaria. Con l'archiviazione con ridondanza geografica e accesso in lettura, è possibile leggere dall'area secondaria indipendentemente dal fatto che Microsoft avvii un failover dall'area primaria a quella secondaria. 

Per un account di archiviazione con ridondanza geografica o di archiviazione con ridondanza geografica e accesso in lettura abilitata, tutti i dati vengono prima replicati con l'archiviazione con ridondanza locale (LRS). Prima di tutto, viene eseguito il commit di un aggiornamento nella località primaria e viene eseguita la replica con l'archiviazione con ridondanza locale. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria tramite l'archiviazione con ridondanza geografica. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale. 

Entrambe le aree primaria e secondaria gestiscono le repliche tra domini di errore e domini di aggiornamento separati all'interno di un'unità di scala di archiviazione. L'unità di scala di archiviazione è l'unità di replica di base nel data center. La replica a questo livello viene fornita dall'archiviazione con ridondanza locale. Per altre informazioni, vedere [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per l'Archiviazione di Azure](../articles/storage/common/storage-redundancy-lrs.md).

Nella scelta dell'opzione di replica da usare, tenere presenti queste considerazioni:

* L'archiviazione con ridondanza della zona (ZRS) fornisce disponibilità elevata con replica sincrona e per alcuni scenari può essere una scelta migliore rispetto all'archiviazione con ridondanza geografica o all'archiviazione con ridondanza geografica e accesso in lettura. Per altre informazioni sull'archiviazione con ridondanza della zona, vedere [Archiviazione con ridondanza della zona](../articles/storage/common/storage-redundancy-zrs.md).
* Poiché la replica asincrona implica un ritardo, in caso di un'emergenza a livello di area, le modifiche non ancora replicate nell'area secondaria potrebbero andare perse se non è possibile recuperare i dati dall'area primaria.
* Con l'archiviazione con ridondanza geografica, la replica non è disponibile a meno che Microsoft non avvii il failover nell'area secondaria. Se Microsoft avvia un failover nell'area secondaria, al termine del failover si avrà accesso in lettura e scrittura a tali dati. Per altre informazioni, vedere le [indicazioni sul ripristino di emergenza](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Per consentire a un'applicazione di leggere nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura.

## <a name="read-access-geo-redundant-storage"></a>Archiviazione con ridondanza geografica e accesso in lettura

L'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) ottimizza la disponibilità per l'account di archiviazione. L'archiviazione con ridondanza geografica e accesso in lettura fornisce l'accesso in sola lettura ai dati nella località secondaria, oltre alla replica geografica tra due aree.

Se si abilita l'accesso in sola lettura ai dati nell'area secondaria, i dati saranno disponibili in un endpoint secondario, oltre che nell'endpoint primario dell'account di archiviazione. L'endpoint secondario è simile a quello primario, ma al nome dell'account viene aggiunto il suffisso `–secondary`. Se ad esempio l'endpoint primario per il servizio BLOB è `myaccount.blob.core.windows.net`, l'endpoint secondario sarà `myaccount-secondary.blob.core.windows.net`. Le chiavi di accesso per l'account di archiviazione sono identiche per gli endpoint primario e secondario.

Alcune considerazioni da ricordare quando si usa l'archiviazione con ridondanza geografica e accesso in lettura:

* L'applicazione deve gestire l'endpoint con cui interagire quando usa l'archiviazione con ridondanza geografica e accesso in lettura.
* Poiché la replica asincrona implica un ritardo, è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria, ad esempio in caso di un'emergenza locale.
* È possibile controllare l'ora dell'ultima sincronizzazione dell'account di archiviazione. L'ora dell'ultima sincronizzazione è un valore di data/ora GMT. Tutte le scritture nell'area primaria precedenti all'ora dell'ultima sincronizzazione sono state scritte correttamente nella località secondaria, vale a dire che sono disponibili per la lettura dalla località secondaria. Le scritture nell'area primaria successive all'ora dell'ultima sincronizzazione possono essere o meno già disponibili per la lettura. È possibile eseguire query su questo valore usando il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md) o una delle librerie client di Archiviazione di Azure.
* Se Microsoft avvia un failover nell'area secondaria, al termine del failover si avrà accesso in lettura e scrittura a tali dati. Per altre informazioni, vedere [Indicazioni sul ripristino di emergenza](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Per informazioni su come passare all'area secondaria, vedere [Cosa fare se si verifica un'interruzione di Archiviazione di Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).
* L'archiviazione con ridondanza geografica e accesso in lettura è pensata per rispondere a requisiti di disponibilità elevata. Per indicazioni sulla scalabilità, vedere l'[elenco di controllo delle prestazioni](../articles/storage/common/storage-performance-checklist.md).
* Per suggerimenti su come progettare la disponibilità elevata con l'archiviazione con ridondanza geografica e accesso in lettura, vedere [Progettazione di applicazioni a disponibilità elevata con RA-GRS](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Quali sono gli obiettivi RPO e RTO con l'archiviazione con ridondanza geografica?
**Obiettivo del punto di ripristino (RPO)**: nel caso dell'archiviazione con ridondanza geografica e dell'archiviazione con ridondanza geografica e accesso in lettura, il servizio di archiviazione esegue la replica geografica asincrona dalla località primaria a quella secondaria. Nell'eventualità di una grave emergenza a livello locale nell'area primaria, Microsoft esegue un failover all'area secondaria. Se viene eseguito un failover, le modifiche recenti non ancora replicate geograficamente potrebbero andare perse. Il numero di minuti di potenziale perdita di dati è detto obiettivo del punto di ripristino e indica il punto nel tempo in cui è possibile ripristinare i dati. L'obiettivo del punto di ripristino di Archiviazione di Azure è in genere inferiore a 15 minuti, anche se attualmente non è previsto alcun contratto di servizio sulla durata della replica geografica.

**Obiettivo del tempo di ripristino:** l'obiettivo del tempo di ripristino è una misura del tempo necessario per eseguire il failover e riportare online l'account di archiviazione. Il tempo necessario per eseguire il failover include le azioni seguenti:

   * Tempo necessario a Microsoft per determinare se i dati possono essere recuperati nella località primaria o se è necessario un failover.
   * Tempo per eseguire il failover dell'account di archiviazione mediante modifica delle voci relative al DNS primario in modo che puntino alla località secondaria.

   Microsoft prende seriamente la responsabilità di salvaguardare i dati degli utenti. Se esiste la possibilità di recuperare i dati nell'area primaria, Microsoft rimanderà il failover e si concentrerà sul recupero dei dati. In una versione futura del servizio sarà possibile attivare un failover a livello di account per poter controllare personalmente l'obiettivo del tempo di ripristino.

## <a name="paired-regions"></a>Aree associate 

L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria associata viene determinata in base all'area primaria e non è possibile modificarla. Per informazioni aggiornate sulle aree supportate da Azure, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../articles/best-practices-availability-paired-regions.md).
