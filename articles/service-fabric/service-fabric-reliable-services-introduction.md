---
title: Panoramica del modello di programmazione Reliable Services
description: Informazioni sul modello di programmazione Reliable Services di Service Fabric e su come sviluppare servizi personalizzati.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77083511"
---
# <a name="reliable-services-overview"></a>Panoramica di Reliable Services

Azure Service Fabric semplifica la scrittura e la gestione di servizi con e senza stato. In questo argomento viene trattato quanto segue:

* Il modello di programmazione Reliable Services per servizi con stato e senza stato.
* Le opzioni disponibili durante lo sviluppo di un servizio Reliable Services.
* Alcuni scenari ed esempi dell'uso di Reliable Services e della relativa modalità di sviluppo.

*Reliable Services* è uno dei modelli di programmazione disponibili in Service Fabric. Un altro è il modello di programmazione *Reliable Actor* , che fornisce un Framework di applicazione [Actor virtuale](https://research.microsoft.com/en-us/projects/orleans/) sul modello di Reliable Services. Per altre informazioni su Reliable Actors, vedere [Introduzione a Reliable Actors in Service Fabric](service-fabric-reliable-actors-introduction.md).

Service Fabric gestisce il ciclo di vita dei servizi, dalle fasi di provisioning e distribuzione fino all'aggiornamento e all'eliminazione, usando gli strumenti di [gestione delle applicazioni di Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Che cosa sono Reliable Services

Reliable Services offre un modello di programmazione di alto livello, semplice e potente, per specificare gli elementi importanti per l'applicazione. Il modello di programmazione Reliable Services offre quanto indicato di seguito:

* Accesso alle API Service Fabric. A differenza dei servizi Service Fabric modellati come [eseguibili Guest](service-fabric-guest-executables-introduction.md), Reliable Services possibile usare direttamente Service Fabric API. Questo consente ai servizi di:
  * Eseguire query sul sistema
  * Segnala integrità informazioni sulle entità nel cluster
  * Ricevere notifiche relative alla configurazione e alle modifiche del codice
  * Trovare e comunicare con altri servizi,
  * Usare le [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * Accedi a molte altre funzionalità, da un modello di programmazione di prima classe in diversi linguaggi di programmazione.
* Un modello semplice per l'esecuzione di codice che si riferisce ad altri modelli di programmazione noti. Il codice ha un punto di ingresso ben definito e un ciclo di vita facile da gestire.
* Un modello di comunicazione modulare. Usare il metodo di trasporto di propria scelta, ad esempio HTTP con [API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolli TCP personalizzati e così via. Reliable Services fornisce alcune interessanti opzioni predefinite, ma consente anche di usarne di personalizzate.
* Per i servizi con stato, il modello di programmazione Reliable Services consente di archiviare lo stato in modo coerente e affidabile all'interno del servizio usando [raccolte Reliable](service-fabric-reliable-services-reliable-collections.md). Le raccolte Reliable sono un semplice set di classi di raccolte Reliable a disponibilità elevata che risulterà familiare a chiunque abbia usato raccolte C#. In passato, per la gestione di uno stato affidabile i servizi dovevano contare su sistemi esterni. Le raccolte Reliable consentono di archiviare lo stato insieme ai calcoli effettuati con gli stessi livelli di disponibilità e affidabilità garantiti dagli archivi esterni a disponibilità elevata. Questo modello riduce anche la latenza perché si condividono le risorse di calcolo e stato necessarie per il funzionamento.

## <a name="what-makes-reliable-services-different"></a>Cosa rende Reliable Services diversi

Reliable Services sono diversi dai servizi che potrebbero essere stati scritti in precedenza, perché Service Fabric fornisce:

* **Affidabilità**: il servizio rimane operativo anche in ambienti non affidabili in cui i computer si guastano o presentano errori di rete o in casi in cui i servizi stessi presentano errori o arresti anomali. Per i servizi con stato, lo stato viene mantenuto anche in presenza di errori di rete o di altro tipo.
* **Disponibilità**: il servizio è raggiungibile e reattivo. Service Fabric mantiene il numero desiderato di copie in esecuzione.
* **Scalabilità**: i servizi vengono separati dall'hardware specifico e possono essere aumentati o ridotti secondo necessità mediante l'aggiunta o la rimozione di risorse hardware o di altro tipo. I servizi, in particolare quelli con stato, possono essere partizionati facilmente. In questo modo, il servizio dispone di scalabilità per gestire gli errori parziali. I servizi possono essere creati ed eliminati in modo dinamico tramite codice, consentendo l'attivazione di più istanze in base alle esigenze, ad esempio in risposta alle richieste dei clienti. Infine, Service Fabric promuove l'uso di servizi leggeri. Service Fabric permette il provisioning di migliaia di servizi con un unico processo, evitando così di richiedere o dedicare intere istanze o processi del sistema operativo a una singola istanza di un servizio.
* **Coerenza** : è possibile garantire la coerenza delle informazioni archiviate in un servizio Reliable Services. Questo vale anche in più raccolte Reliable Collections all'interno di un servizio. È possibile apportare modifiche nelle raccolte all'interno di un servizio con transazioni atomiche.

## <a name="service-lifecycle"></a>Ciclo di vita del servizio

Reliable Services fornisce un ciclo di vita semplice che consente di attivare rapidamente il codice e iniziare a usarlo.  Per rendere operativo un nuovo servizio è necessario implementare due metodi:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners**: questo metodo è dove il servizio definisce gli stack di comunicazione da usare. Lo stack di comunicazione, ad esempio [API Web](service-fabric-reliable-services-communication-webapi.md), definisce l'endpoint di ascolto o gli endpoint per il servizio, ovvero il modo in cui i client potranno raggiungerlo. Definisce anche in che modo i messaggi visualizzati interagiscono con il resto del codice del servizio.
* **RunAsync**: questo metodo è dove il servizio esegue la logica di business e dove avvierà attività in background da eseguire per tutta la durata del servizio. Il token di annullamento fornito indica quando l'operazione deve essere interrotta. Se, ad esempio, il servizio deve effettuare il pull dei messaggi da un oggetto Reliable Queue per elaborarli, questa è l'area in cui vengono eseguite tali attività.

Se è la prima volta che si sente parlare di Reliable Services, è consigliabile continuare a leggere. Se si sta cercando una procedura dettagliata del ciclo di vita di Reliable Services, vedere [Panoramica del ciclo](service-fabric-reliable-services-lifecycle.md)di vita Reliable Services.

## <a name="example-services"></a>Servizi di esempio

Verrà ora illustrato in dettaglio il funzionamento del modello di Reliable Services con i servizi con e senza stato.

### <a name="stateless-reliable-services"></a>Reliable Services senza stato

Un *servizio* senza stato è un servizio in cui non viene mantenuto alcuno stato all'interno del servizio tra le chiamate. Qualsiasi stato presente è interamente eliminabile e non richiede sincronizzazione, replica, persistenza o disponibilità elevata.

Si consideri ad esempio un servizio Calculator privo di memoria che riceve contemporaneamente tutti i termini e le operazioni da eseguire.

In questo caso il metodo `RunAsync()` (C#) o `runAsync()` (Java) del servizio può essere vuoto, dal momento che il servizio non deve eseguire alcuna elaborazione di attività in background. Quando viene creato, il servizio Calculator restituisce un `ICommunicationListener` (C#) o un `CommunicationListener` (Java), ad esempio [API Web](service-fabric-reliable-services-communication-webapi.md), che apre un endpoint di ascolto su una porta. L'endpoint di ascolto si collegherà ai diversi metodi, ad esempio di calcolo, ad esempio "Add(n1, n2)", che definiscono l'API pubblica del servizio Calculator.

Quando viene effettuata una chiamata da un client, viene richiamato il metodo appropriato. Il servizio Calculator esegue le operazioni sui dati forniti e restituisce il risultato, senza archiviare alcuno stato.

La mancata archiviazione di qualsiasi stato interno rende semplice il servizio Calculator di esempio. La maggior parte dei servizi non è però realmente senza stato. Al contrario, esternalizzano il proprio stato in un altro archivio. Ad esempio, un'app Web che mantiene lo stato delle sessioni in una cache o in un archivio di backup non è senza stato.

Un esempio comune di come i servizi senza stato vengono usati in Service Fabric è un servizio front-end che espone l'API pubblica di un'applicazione Web. Il servizio front-end richiede quindi ai servizi con stato di completare la richiesta di un utente. In questo caso, le chiamate provenienti dai client vengono indirizzate a una porta conosciuta, ad esempio la porta 80, dove è in ascolto il servizio senza stato. Il servizio senza stato riceve la chiamata, determina se quest'ultima proviene da una parte attendibile e identifica il servizio a cui è destinata.  Il servizio senza stato inoltra quindi la chiamata alla partizione corretta del servizio con stato e attende una risposta. Quando il servizio senza stato riceve una risposta, risponde al client originale. Un esempio di tale servizio è costituito dall'esempio *Service Fabric Introduzione* ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), tra gli altri esempi di Service Fabric nel repository.

### <a name="stateful-reliable-services"></a>Reliable Services con stato

Un *servizio con stato* è uno che deve avere una parte dello stato mantenuta coerente e presente per poter funzionare il servizio. Si consideri, ad esempio, un servizio che deve calcolare costantemente la media mobile di alcuni valori in base agli aggiornamenti ricevuti. Per eseguire questa operazione, deve avere il set corrente di richieste in ingresso, oltre alla media corrente. Qualsiasi servizio che recupera, elabora e archivia informazioni in un archivio esterno, ad esempio un archivio tabelle o un BLOB di Azure, è un servizio con stato. Conserva il proprio stato nell'archivio stati esterno.

Attualmente la maggior parte dei servizi archivia il proprio stato esternamente, perché gli archivi esterni assicurano affidabilità, disponibilità, scalabilità e coerenza dello stato. In Service Fabric, i servizi non devono necessariamente archiviare esternamente il proprio stato. Service Fabric si occupa di questi requisiti sia per il codice che per lo stato del servizio.

Si supponga di voler scrivere un servizio di elaborazione immagini. Per l'elaborazione, il servizio accetta un'immagine e la serie di conversioni da eseguire su di essa. Il servizio restituisce un listener di comunicazione (si supponga che sia un'API Web) che espone un'API come `ConvertImage(Image i, IList<Conversion> conversions)`. Quando riceve una richiesta, il servizio lo archivia in un oggetto `IReliableQueue`e restituisce un ID al client in modo che possa tenere traccia della richiesta.

In questo servizio `RunAsync()` potrebbe essere più complesso. Il servizio dispone di un ciclo all'interno di relativo `RunAsync()` che effettua il pull delle richieste da `IReliableQueue` ed esegue le conversioni richieste. I risultati vengono memorizzati un `IReliableDictionary` in modo che, al ritorno del client, possano ottenere le immagini convertite. Per garantire che anche in caso di problemi l'immagine non vada persa, questo servizio Reliable Services effettua il pull dalla coda, esegue le conversioni e archivia il risultato in una singola transazione. In questo modo il messaggio viene rimosso dalla coda e i risultati vengono archiviati nel dizionario dei risultati solo dopo il completamento delle conversioni. In alternativa, il servizio potrebbe estrarre l'immagine dalla coda e memorizzarla immediatamente in un archivio remoto. Questo ridurrebbe la quantità di stato che il servizio deve gestire, ma aumenterebbe la complessità, poiché il servizio deve a quel punto conservare i metadati necessari per gestire l'archivio remoto. Con entrambi gli approcci, in caso di errori durante l'operazione, la richiesta rimane nella coda in attesa di elaborazione.

Sebbene questo servizio suoni come un tipico servizio .NET, la differenza consiste nel fatto che le strutture di dati`IReliableQueue` utilizzate `IReliableDictionary`(e) sono fornite da Service Fabric e sono altamente affidabili, disponibili e coerenti.

## <a name="when-to-use-reliable-services-apis"></a>Quando usare le API Reliable Services

Prendere in considerazione Reliable Services API se:

* Si vuole che il codice del servizio (e facoltativamente lo stato) sia a disponibilità elevata e affidabile.
* Sono necessarie garanzie transazionali in più unità di stato multiple, ad esempio elementi di ordine e righe di ordine.
* Lo stato dell'applicazione può essere modellato naturalmente sotto forma di oggetti ReliableDictionary e ReliableQueue.
* Il codice o lo stato delle applicazioni deve disporre di un'elevata disponibilità, con operazioni di lettura e scrittura a bassa latenza.
* L'applicazione deve controllare la concorrenza o la granularità di operazioni transazionali su una o più raccolte Reliable Collections.
* Si vogliono gestire le comunicazioni o controllare lo schema di partizionamento del servizio.
* Il codice necessita di un ambiente di runtime a thread libero.
* L'applicazione deve creare o eliminare definitivamente in modo dinamico gli oggetti ReliableDictionary, ReliableQueue o interi servizi in fase di esecuzione.
* È necessario controllare a livello di codice i backup forniti da Service Fabric e ripristinare le funzionalità per lo stato del servizio.
* L'applicazione deve gestire la cronologia delle modifiche per le unità di stato.
* Si vogliono utilizzare provider di stato di terze parti o svilupparne di personalizzati.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
* [Modello di programmazione Reliable Actors](service-fabric-reliable-actors-introduction.md)
