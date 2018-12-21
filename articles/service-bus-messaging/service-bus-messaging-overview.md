---
title: Panoramica della messaggistica del bus di servizio di Azure | Microsoft Docs
description: Descrizione della messaggistica del bus di servizio
services: service-bus-messaging
documentationcenter: ''
author: spelluru
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 09/22/2018
ms.custom: mvc
ms.author: spelluru
ms.openlocfilehash: 55a651080c4ed1d22901681317046f0853e257f0
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163107"
---
# <a name="what-is-azure-service-bus"></a>Cos'è il bus di servizio di Azure?

Il bus di servizio di Microsoft Azure è un broker di messaggi di [integrazione](http://azure.com/integration) aziendale completamente gestito. Il bus di servizio viene in genere usato per disaccoppiare applicazioni e servizi ed è una piattaforma affidabile e sicura per il trasferimento dello stato e dei dati asincrono. I dati vengono trasferiti tra applicazioni e servizi diversi usando i *messaggi*. Un messaggio è in formato binario e può contenere solo testo, XML o JSON. 

Alcuni scenari di messaggistica comuni sono:

* Messaggistica: trasferire i dati aziendali, ad esempio gli ordini di vendita o di acquisto, i giornali di registrazione o i movimenti delle scorte.
* Disaccoppiare le applicazioni: migliorare l'affidabilità e la scalabilità di applicazioni e servizi. Non è necessario che il client e il servizio siano online contemporaneamente.
* Argomenti e sottoscrizioni: abilitare 1:*n* relazioni tra server di pubblicazione e sottoscrittori.
* Sessioni di messaggistica: implementare i flussi di lavoro che richiedono l'ordinamento di messaggi o il differimento di messaggi.

## <a name="namespaces"></a>Spazi dei nomi

Uno spazio dei nomi è un contenitore di ambito per tutti i componenti di messaggistica. Più code e argomenti possono risiedere in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni.

## <a name="queues"></a>Queues

I messaggi vengono inviati e ricevuti dalle *code*. Le code consentono di archiviare i messaggi fino a quando l'applicazione ricevente è disponibile per riceverli ed elaborarli.

![Coda](./media/service-bus-messaging-overview/about-service-bus-queue.png)

I messaggi nelle code vengono ordinati e vi viene aggiunto un timestamp all'arrivo. Dopo essere stato accettato, il messaggio viene conservato in modo sicuro nella risorsa di archiviazione ridondante. I messaggi vengono recapitati in modalità  *pull*, ovvero su richiesta.

## <a name="topics"></a>Argomenti

È anche possibile usare gli *argomenti* per inviare e ricevere i messaggi. Mentre una coda viene spesso usata per la comunicazione da punto a punto, gli argomenti sono utili negli scenari di pubblicazione/sottoscrizione.

![Argomento](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Gli argomenti possono avere più sottoscrizioni indipendenti. Un sottoscrittore a un argomento può ricevere una copia di ogni messaggio inviato a tale argomento. Le sottoscrizioni sono entità denominate, che vengono create in modo permanente, ma possono facoltativamente scadere o essere eliminate automaticamente.

In alcuni scenari potrebbe essere necessario che singole sottoscrizioni non ricevano tutti i messaggi inviati a un argomento. In questo caso, è possibile usare [regole e filtri](topic-filters.md) per definire le condizioni che attivano [azioni](topic-filters.md#actions) facoltative, filtrare i messaggi specificati e impostare o modificare le proprietà dei messaggi.

## <a name="advanced-features"></a>Funzionalità avanzate

Il bus di servizio include anche funzionalità avanzate che consentono di risolvere problemi di messaggistica più complessi. Le sezioni seguenti descrivono queste funzionalità chiave:

### <a name="message-sessions"></a>Sessioni di messaggistica

Per realizzare una garanzia FIFO (First-In-First-Out) nel bus di servizio, usare le sessioni. Le [sessioni di messaggistica](message-sessions.md) consentono la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati. 

### <a name="auto-forwarding"></a>Inoltro automatico

La funzionalità di [inoltro automatico](service-bus-auto-forwarding.md) consente di concatenare una coda o una sottoscrizione a un'altra coda o a un altro argomento che fa parte dello stesso spazio dei nomi. Quando l'inoltro automatico è abilitato, il bus di servizio rimuove automaticamente i messaggi presenti nella prima coda o sottoscrizione (origine) e li inserisce nella seconda coda o argomento (destinazione).

### <a name="dead-lettering"></a>Inserimento nella coda di messaggi non recapitabili

Il bus di servizio supporta una [coda di messaggi non recapitabili](service-bus-dead-letter-queues.md) per conservare i messaggi che non possono essere recapitati ai ricevitori o che non possono essere elaborati. È quindi possibile rimuovere messaggi dalla coda di messaggi non recapitabili ed esaminarli.

### <a name="scheduled-delivery"></a>Recapito pianificato

È possibile inviare messaggi a una coda o un argomento [per l'elaborazione ritardata](message-sequencing.md#scheduled-messages), ad esempio pianificando un processo in modo che diventi disponibile per l'elaborazione da parte di un sistema a una determinata ora.

### <a name="message-deferral"></a>Differimento di messaggi

Se un client di coda o di sottoscrizione riceve un messaggio che è disposto a elaborare, ma l'elaborazione non è possibile a causa di circostanze particolari all'interno dell'applicazione, l'entità può [rinviare il recupero del messaggio](message-deferral.md) a un secondo momento. Il messaggio rimane nella coda o nella sottoscrizione, ma viene messo da parte.

### <a name="batching"></a>Creazione di batch

L'[invio in batch sul lato client](service-bus-performance-improvements.md#client-side-batching) consente a un client di coda o argomento di ritardare l'invio di un messaggio per un determinato periodo di tempo. Se il client invia messaggi aggiuntivi durante questo periodo di tempo, trasmette i messaggi in un singolo batch. 

### <a name="transactions"></a>Transazioni

Una [transazione](service-bus-transactions.md) raggruppa due o più operazioni in un ambito di esecuzione. Il bus di servizio supporta le operazioni di raggruppamento in una singola entità di messaggistica (coda, argomento, sottoscrizione) nell'ambito di una transazione.

### <a name="filtering-and-actions"></a>Filtro e azioni

I sottoscrittori possono definire i messaggi che vogliono ricevere da un argomento. Per specificare tali messaggi, viene usata una o più [regole di sottoscrizione denominate](topic-filters.md). Per ogni condizione di regola corrispondente, la sottoscrizione genera una copia del messaggio, che può essere annotata in modo diverso per ogni regola.

### <a name="auto-delete-on-idle"></a>Eliminazione automatica in caso di inattività

L'[eliminazione automatica in caso di inattività](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) consente di specificare un intervallo di inattività dopo il quale la coda viene automaticamente eliminata. La durata minima è 5 minuti.

### <a name="duplicate-detection"></a>Rilevamento duplicati

Se si verifica un errore a causa del quale il client è in dubbio sul risultato di un'operazione di invio, il [rilevamento dei duplicati](duplicate-detection.md) elimina il dubbio da queste situazioni, consentendo al mittente di inviare nuovamente lo stesso messaggio che, se duplicato, verrà rimosso automaticamente dalla coda o dall'argomento.

### <a name="sas-rbac-and-managed-identities-for-azure-resources"></a>Firma di accesso condiviso, controllo degli accessi in base al ruolo e identità gestite per risorse di Azure

Il bus di servizio supporta i protocolli di sicurezza, ad esempio [firme di accesso condiviso](service-bus-sas.md), [controllo degli accessi in base al ruolo](service-bus-role-based-access-control.md) e [identità gestite per risorse di Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Ripristino di emergenza geografico

Quando le aree o i data center di Azure riscontrano tempo di inattività, il [ripristino di emergenza geografico](service-bus-geo-dr.md) fa in modo che l'elaborazione dei dati continui a funzionare in un'area o in un data center diverso.

### <a name="security"></a>Sicurezza

Il bus di servizio supporta i protocolli [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP/REST](/rest/api/servicebus/) standard.

## <a name="client-libraries"></a>Librerie client

Il bus di servizio supporta le librerie client per [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master), [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integrazione

Il bus di servizio si integra completamente con i servizi di Azure seguenti:

- [Griglia di eventi](https://azure.microsoft.com/services/event-grid/) 
- [App per la logica](https://azure.microsoft.com/services/logic-apps/) 
- [Funzioni](https://azure.microsoft.com/services/functions/) 
- [Dynamics 365](https://dynamics.microsoft.com)
- [Analisi dei flussi](https://azure.microsoft.com/services/stream-analytics/)
 
## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la messaggistica del bus di servizio, vedere gli articoli seguenti:

* [Confrontare i servizi di messaggistica di Azure](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* Altre informazioni sui livelli [Standard e Premium](https://azure.microsoft.com/pricing/details/service-bus/) del bus di servizio di Azure e sui relativi prezzi
* [Performance and Latency of Azure Service Bus Premium tier (Prestazioni e latenza del livello Premium del bus di servizio di Azure)](https://blogs.msdn.microsoft.com/servicebus/2016/07/18/premium-messaging-how-fast-is-it/)
* Provare le guide introduttive per [.NET](service-bus-quickstart-powershell.md), [Java](service-bus-quickstart-powershell.md) o [JMS](service-bus-quickstart-powershell.md)
