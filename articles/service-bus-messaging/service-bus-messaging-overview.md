---
title: Panoramica della messaggistica del bus di servizio di Azure | Microsoft Docs
description: Descrizione della messaggistica del bus di servizio
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 12d4bada4f84098f1559ea7b59fbbd35e0801347
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561590"
---
# <a name="what-is-azure-service-bus"></a>Cos'è il bus di servizio di Azure?

Il bus di servizio di Microsoft Azure è un broker di messaggi di integrazione aziendale completamente gestito. Il bus di servizio può separare applicazioni e servizi e offre una piattaforma affidabile e sicura per il trasferimento asincrono di dati e stati.

I dati vengono trasferiti tra applicazioni e servizi diversi usando i *messaggi*. Un messaggio è in formato binario e può contenere codice JSON o XML o solo testo. Per altre informazioni, vedere [Integration Services](https://azure.com/integration).

Alcuni scenari di messaggistica comuni sono:

* *Messaggistica*. Trasferimento di dati aziendali, ad esempio gli ordini di vendita o di acquisto, i giornali di registrazione o i movimenti delle scorte.
* *Separazione delle applicazioni*. Miglioramento dell'affidabilità e della scalabilità di applicazioni e servizi. Client e servizio non devono essere online nello stesso momento.
* *Argomenti e sottoscrizioni*. Abilitazione di relazioni 1:*n* tra editori e sottoscrittori.
* *Sessioni di messaggistica*. Implementazione di flussi di lavoro che richiedono l'ordinamento o il differimento di messaggi.

## <a name="namespaces"></a>Spazi dei nomi

Uno spazio dei nomi è un contenitore per tutti i componenti di messaggistica. Più code e argomenti possono essere presenti in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni.

## <a name="queues"></a>Queues

I messaggi vengono inviati e ricevuti dalle *code*. Le code consentono di archiviare i messaggi fino a quando l'applicazione di destinazione è disponibile a riceverli ed elaborarli.

![Coda](./media/service-bus-messaging-overview/about-service-bus-queue.png)

I messaggi nelle code vengono ordinati e vi viene aggiunto un timestamp all'arrivo. Dopo essere stato accettato, il messaggio viene conservato in modo sicuro nella risorsa di archiviazione ridondante. I messaggi vengono recapitati in modalità *pull*, ovvero solo quando è richiesto.

## <a name="topics"></a>Argomenti

È anche possibile usare gli *argomenti* per inviare e ricevere i messaggi. Mentre una coda viene spesso usata per la comunicazione da punto a punto, gli argomenti sono utili negli scenari di pubblicazione/sottoscrizione.

![Argomento](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Gli argomenti possono avere più sottoscrizioni indipendenti. Un sottoscrittore a un argomento può ricevere una copia di ogni messaggio inviato a tale argomento. Le sottoscrizioni sono entità denominate. Le sottoscrizioni vengono mantenute, ma possono scadere o essere eliminate in modo automatico.

Potrebbe essere necessario che singole sottoscrizioni non ricevano tutti i messaggi inviati a un argomento. In tal caso, è possibile usare *regole* e *filtri* per definire condizioni che attivano *azioni* facoltative. È possibile filtrare i messaggi specificati e impostare o modificare le proprietà del messaggio. Per altre informazioni, vedere [Filtri e azioni per gli argomenti](topic-filters.md).

## <a name="advanced-features"></a>Funzionalità avanzate

Il bus di servizio include anche funzionalità avanzate che consentono di risolvere problemi di messaggistica più complessi. Le sezioni seguenti descrivono alcune di queste funzionalità.

### <a name="message-sessions"></a>Sessioni di messaggistica

Per creare una garanzia FIFO (First-In-First-Out) nel bus di servizio, usare le sessioni. Le sessioni di messaggistica consentono la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati. Per altre informazioni, vedere [Sessioni di messaggistica: FIFO (First In, First Out)](message-sessions.md).

### <a name="autoforwarding"></a>Inoltro automatico

La funzionalità di inoltro automatico consente di concatenare una coda o una sottoscrizione a un'altra coda oppure a un altro argomento. Gli elementi devono appartenere allo stesso spazio dei nomi. Con l'inoltro automatico, il bus di servizio rimuove automaticamente i messaggi da una coda o da una sottoscrizione e li inserisce in una coda oppure in un argomento diverso. Per altre informazioni, vedere [Concatenamento di entità del bus di servizio con l'inoltro automatico](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Coda di messaggi non recapitabili

Il bus di servizio supporta una coda di messaggi non recapitabili, ovvero una coda che contiene messaggi che non possono essere recapitati ad alcun ricevitore e che non possono essere elaborati. Il bus di servizio consente di rimuovere i messaggi dalla coda di messaggi non recapitabili e di esaminarli. Per altre informazioni, vedere [Panoramica delle code dei messaggi non recapitabili del bus di servizio](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Recapito pianificato

È possibile inviare messaggi a una coda oppure a un argomento per l'elaborazione posticipata. È possibile pianificare un processo affinché diventi disponibile per l'elaborazione da parte di un sistema in un determinato momento. Per altre informazioni, vedere [Messaggi pianificati](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Differimento di messaggi

Un client di coda o di sottoscrizione può rinviare il recupero di un messaggio fino a un momento successivo. Questo rinvio potrebbe essere causato da particolari circostanze nell'applicazione. Il messaggio rimane nella coda o nella sottoscrizione, ma viene messo da parte. Per altre informazioni, vedere [Differimento di messaggi](message-deferral.md).

### <a name="batching"></a>Creazione di batch

L'invio in batch sul lato client consente a un client di coda o di argomento di ritardare l'invio di un messaggio per un determinato periodo di tempo. Se il client invia messaggi aggiuntivi durante questo periodo di tempo, trasmette i messaggi in un singolo batch. Per altre informazioni, vedere [Invio in batch sul lato client](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transazioni

Una transazione raggruppa due o più operazioni in un *ambito di esecuzione*. Il bus di servizio supporta il raggruppamento di operazioni rispetto a un'unica entità di messaggistica nell'ambito di una singola transazione. Un'entità di messaggistica può essere una coda, un argomento o una sottoscrizione. Per altre informazioni, vedere [Panoramica dell'elaborazione delle transazioni del bus di servizio](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtro e azioni

I sottoscrittori possono definire i messaggi che vogliono ricevere da un argomento. Per specificare tali messaggi, vengono usate una o più regole di sottoscrizione denominate. Per ogni condizione di regola corrispondente, la sottoscrizione genera una copia del messaggio, che può essere annotata in modo diverso per ogni regola. Per altre informazioni, vedere [Filtri e azioni per gli argomenti](topic-filters.md).

### <a name="autodelete-on-idle"></a>Eliminazione automatica in caso di inattività

L'eliminazione automatica in caso di inattività consente di specificare un intervallo di inattività dopo il quale la coda viene automaticamente eliminata. La durata minima è 5 minuti. Per altre informazioni, vedere [Proprietà QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Rilevamento duplicati

Un errore può causare nel cliente un dubbio sul risultato di un'operazione di invio. Il rilevamento dei duplicati consente al mittente di inviare nuovamente lo stesso messaggio. Un'altra opzione prevede che la coda o l'argomento elimini eventuali copie duplicate. Per altre informazioni, vedere [Rilevamento duplicati](duplicate-detection.md).

### <a name="security-protocols"></a>Protocolli di sicurezza
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Il bus di servizio supporta i protocolli di sicurezza, ad esempio [firme di accesso condiviso](service-bus-sas.md), [controllo degli accessi in base al ruolo](authenticate-application.md) e [identità gestite per risorse di Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Ripristino di emergenza geografico

Quando le aree o i data center di Azure riscontrano tempo di inattività, il ripristino di emergenza geografico fa in modo che l'elaborazione dei dati continui a funzionare in un'area o in un data center diverso. Per altre informazioni, vedere [Ripristino di emergenza geografico per il bus di servizio di Azure](service-bus-geo-dr.md).

### <a name="security"></a>Security

Il bus di servizio supporta i protocolli [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP/REST](/rest/api/servicebus/) standard.

## <a name="client-libraries"></a>Librerie client

Il bus di servizio supporta le librerie client per [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master) e [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integrazione

Il bus di servizio si integra completamente con i servizi di Azure seguenti:

* [Griglia di eventi](https://azure.microsoft.com/services/event-grid/)
* [App per la logica](https://azure.microsoft.com/services/logic-apps/)
* [Funzioni di Azure](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la messaggistica del bus di servizio, vedere gli articoli seguenti:

* Per confrontare i servizi di messaggistica di Azure, vedere [Confronto dei servizi](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Provare le guide introduttive per [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) o [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Per gestire le risorse del bus di servizio, vedere [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Per altre informazioni sui livelli Standard e Premium e i relativi prezzi, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).
* Per informazioni sulle prestazioni e la latenza per il livello Premium, vedere [Messaggistica Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
