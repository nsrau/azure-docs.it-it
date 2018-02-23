---
title: Domande frequenti sugli Hub eventi di Azure | Documentazione Microsoft
description: Domande frequenti sugli Hub eventi di Azure (FAQ)
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 6bdcbbe37613d5384017409f3be2772085e276ae
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="event-hubs-frequently-asked-questions"></a>Domande frequenti sugli Hub eventi di Azure

## <a name="general"></a>Generale

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual è la differenza tra i livelli Standard e Base di Hub eventi?

Il piano Standard di Hub eventi di Azure offre funzionalità superiori a quelle disponibili con il piano Basic. Il piano Standard offre le funzionalità seguenti:
* Periodo di conservazione degli eventi più lungo
* Connessioni negoziate aggiuntive, con sovrapprezzo per un numero di connessioni superiore a quello incluso
* Più di un singolo gruppo di consumer
* [Acquisire](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

Per informazioni dettagliate sui piani tariffari, incluso il livello Dedicato di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Cosa sono le unità elaborate in Hub eventi?

Le unità elaborate in Hub eventi vengono selezionate in modo esplicito tramite il portale di Azure o i modelli di Resource Manager in Hub eventi. Le unità elaborate sono applicabili a tutti gli hub eventi in uno spazio dei nomi di Hub eventi e ogni unità elaborata offre allo spazio dei nomi le capacità seguenti:

* Fino a 1 MB al secondo di eventi in ingresso (gli eventi inviati a un hub eventi), ma non più di 1000 eventi in ingresso, operazioni di gestione o chiamate API di controllo al secondo.
* Fino a 2 MB al secondo di eventi in uscita (gli eventi utilizzati da un hub eventi).
* Fino a 84 GB di archiviazione eventi (sufficienti per il periodo di conservazione di 24 ore predefinito).

Le unità elaborate in Hub eventi vengono fatturate con tariffe orarie, in base al numero massimo di unità selezionate in una determinata ora. È possibile [aumentare il numero unità elaborate](event-hubs-auto-inflate.md) automaticamente man mano che aumenta l'utilizzo.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Come vengono applicati i limiti di unità elaborate in Hub eventi
Se la velocità effettiva totale in ingresso o la frequenza degli eventi totali in ingresso fra tutti gli hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i mittenti vengono limitati e ricevono errori indicanti che è stata superata la quota in ingresso.

Se la velocità effettiva totale in uscita o la frequenza degli eventi totali in uscita fra tutti gli hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i destinatari vengono limitati e ricevono errori indicanti che è stata superata la quota in uscita. Le quote in ingresso e uscita vengono applicate separatamente, in modo che nessun mittente possa causare il rallentamento dell'utilizzo e nessun destinatario possa impedire l'invio di eventi a un hub eventi.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Esiste un limite al numero di unità elaborate che possono essere selezionate?
Esiste una quota predefinita di 20 unità elaborate per ogni spazio dei nomi. È possibile richiedere una quota maggiore di unità elaborate creando un ticket di supporto. Oltre il limite di 20 unità elaborate, sono disponibili bundle di 20 e 100 unità elaborate. Si noti che l'utilizzo di più di 20 unità di elaborate elimina la possibilità di modificare il numero di unità elaborate senza creare ticket di supporto.

Usando la funzionalità [Aumento automatico](event-hubs-auto-inflate.md) è possibile aumentare automaticamente il numero unità elaborate man mano che aumenta l'utilizzo.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>È possibile usare una singola connessione AMQP per l'invio a e la ricezione da più hub eventi?
Sì, purché tutti gli hub eventi si trovino nello stesso spazio dei nomi.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual è il periodo di conservazione massimo per gli eventi?
Il livello Standard di Hub eventi supporta attualmente un periodo di conservazione massimo di 7 giorni. Si noti che gli hub eventi non sono intesi come archivi dati permanenti. Sono previsti periodi di conservazione maggiori di 24 ore per scenari in cui è opportuno riprodurre un flusso di eventi negli stessi sistemi, ad esempio, per la formazione o per verificare un nuovo modello di machine learning sui dati esistenti. Se è necessario conservare i messaggi per più di 7 giorni, abilitando la funzionalità [Acquisizione di Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) nell'hub eventi viene effettuato il pull dei dati dall'hub all'account di archiviazione o all'account del servizio Azure Data Lake scelto. L'abilitazione dell'acquisizione prevede un costo in base alle unità elaborate acquistate.

### <a name="where-is-azure-event-hubs-available"></a>Dove sono disponibili gli hub eventi di Azure?
Hub eventi di Azure è disponibile in tutte le aree di Azure supportate. Per un elenco, visitare la pagina [Aree di Azure](https://azure.microsoft.com/regions/).  

## <a name="best-practices"></a>Procedure consigliate

### <a name="how-many-partitions-do-i-need"></a>Quante partizioni sono necessarie?

Si noti che il numero di partizioni in un hub eventi non può essere modificato dopo la configurazione. Dato tale presupposto, è importante riflettere sul numero di partizioni necessario prima di iniziare. 

Hub eventi è progettato per consentire un solo lettore di partizione per ogni gruppo di consumer. Nella maggior parte dei casi, è sufficiente l'impostazione predefinita di quattro partizioni. Se si intende ridimensionare l'elaborazione degli eventi, è possibile considerare di aggiungere altre partizioni. Non esiste alcun limite specifico della velocità effettiva in una partizione. Tuttavia, la velocità effettiva aggregata nello spazio dei nomi è limitata dal numero di unità della velocità effettiva. Quando si aumenta il numero di unità della velocità effettiva nello spazio dei nomi, è opportuno aggiungere partizioni per consentire ai lettori simultanei di raggiungere la velocità effettiva personale massima.

Tuttavia, se si dispone di un modello in cui l'applicazione ha un'affinità con una determinata partizione, l'aumento del numero di partizioni non comporta alcun vantaggio. Per altre informazioni al riguardo, vedere l'argomento relativo a [disponibilità e coerenza](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Prezzi

### <a name="where-can-i-find-more-pricing-information"></a>Dove sono reperibili altre informazioni sui prezzi?
Per informazioni complete sui prezzi di Hub eventi, vedere [Prezzi Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>È previsto un addebito per conservare gli eventi di Hub eventi per più di 24 ore?
Il livello Standard di Hub eventi consente periodi di conservazione dei messaggi superiori alle 24 ore, per un massimo di 7 giorni. Se le dimensioni del numero totale di eventi archiviati supera i limiti di archiviazione per il numero di unità elaborate selezionate, ovvero 84 GB per unità elaborata, alle dimensioni che superano i limiti saranno applicate le tariffe pubblicate per l'archivio BLOB di Azure. La capacità di archiviazione consentita in ogni unità elaborata copre tutti i costi di archiviazione per periodi di conservazione di 24 ore (impostazione predefinita) anche se l'unità elaborata viene utilizzata fino alla massima capacità consentita in ingresso.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Come vengono calcolate e addebitate le dimensioni di archiviazione di Hub eventi?
Le dimensioni totali di tutti gli eventi archiviati, incluso il sovraccarico interno per le intestazioni degli eventi o nelle strutture di archiviazione su disco in tutti gli hub eventi vengono misurate nel corso della giornata. Alla fine della giornata, viene calcolata la dimensione di archiviazione massima. L'archiviazione giornaliera consentita viene calcolata in base al numero minimo di unità elaborate selezionate durante il giorno (ogni unità elaborata fornisce una capacità massima di 84 GB). Se la dimensione totale supera la capacità massima di archiviazione giornaliera calcolata, l'archiviazione in eccesso viene fatturata in base alle tariffe di archiviazione BLOB di Azure (alla tariffa di **archiviazione con ridondanza locale** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Come vengono calcolati gli eventi in ingresso di Hub eventi?
Ogni evento inviato a un hub eventi conta come messaggio fatturabile. Un *evento in ingresso* è definito come un'unità di dati minore o uguale a 64 KB. Qualsiasi evento di dimensioni minori o uguali a 64 KB è considerato un evento fatturabile. Se l'evento è maggiore di 64 KB, il numero di eventi fatturabili viene calcolato in base alla dimensione dell'evento, in multipli di 64 KB. Ad esempio, un evento di 8 KB inviato all'hub eventi viene fatturato come un solo evento, mentre un messaggio di 96 KB inviato all'hub eventi viene fatturato come due eventi.

Gli eventi utilizzati da un hub eventi, nonché le operazioni di gestione e le chiamate di controllo come i checkpoint, non vengono conteggiati come eventi in ingresso fatturabili, ma si accumulano fino alla capacità massima di unità elaborate.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Si applicano costi di connessione negoziata ad Hub eventi?
I costi di connessione si applicano solo quando viene usato il protocollo AMQP. Non sono previsti costi di connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di sistemi o dispositivi di invio. Se si prevede di usare AMQP, ad esempio per ottenere un flusso di eventi più efficiente o consentire la comunicazione bidirezionale in scenari di comando e controllo IoT, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/) per i dettagli relativi al numero di connessioni incluse in ogni livello del servizio.

### <a name="how-is-event-hubs-capture-billed"></a>Come viene fatturata l'acquisizione di Hub eventi?
L'acquisizione di Hub eventi è abilitata quando per qualsiasi hub eventi nello spazio dei nomi è abilitata l'opzione Acquisizione. L'acquisizione di Hub eventi viene fatturata su base oraria per le unità di velocità effettiva acquistate. Man mano che il numero di unità elaborate aumenta o diminuisce, queste variazioni si riflettono sulla fatturazione dell'acquisizione di Hub eventi in incrementi di ore intere. Per altre informazioni sulla fatturazione dell'acquisizione di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Come viene fatturato l'account di archiviazione selezionato per l'acquisizione di Hub eventi?
L'acquisizione usa un account di archiviazione fornito dall'utente, quando la funzione è abilitata in un hub eventi. Poiché si tratta dell'account di archiviazione dell'utente, eventuali modifiche alla configurazione verranno fatturati sulla sua sottoscrizione di Azure.

## <a name="quotas"></a>Quote

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Sono previste quote associate all'hub eventi?
Per un elenco di tutte le quote dell'hub eventi, vedere [quote](event-hubs-quotas.md).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quali sono alcune delle eccezioni generate dagli hub eventi e dalle azioni consigliate?
Per un elenco delle possibili eccezioni degli hub eventi, vedere [Eccezioni della messaggistica di Hub eventi](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Log di diagnostica
Hub eventi supporta due tipi di [log di diagnostica](event-hubs-diagnostic-logs.md), i log degli errori di acquisizione e i log operativi, entrambi rappresentati in JSON e attivabili tramite il Portale di Azure.

### <a name="support-and-sla"></a>Contratto di servizio e supporto
Il supporto tecnico per Hub eventi è disponibile tramite i [forum della community](https://social.msdn.microsoft.com/forums/azure/home). Il supporto per fatturazione e gestione delle sottoscrizioni viene fornito gratuitamente.

Per altre informazioni sul Contratto di servizio, visitare la pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Aumento automatico di Hub eventi](event-hubs-auto-inflate.md)
