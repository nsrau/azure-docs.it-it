---
title: Ciclo di vita di Reliable Services di Azure Service Fabric | Microsoft Docs
description: Informazioni sugli eventi del ciclo di vita di Reliable Services di Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: ad4228ade68f4494e5be0454643752e742c1cc81
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2018
---
# <a name="reliable-services-lifecycle"></a>Ciclo di vita di Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java su Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services è uno dei modelli di programmazione disponibili in Azure Service Fabric. Quando si parla del ciclo di vita di Reliable Services, è estremamente importante comprendere gli eventi di base del ciclo di vita. L'ordine esatto degli eventi dipende dai dettagli di configurazione. 

In generale, il ciclo di vita di Reliable Services include gli eventi seguenti:

* Durante l'avvio:
  * I servizi vengono costruiti.
  * I servizi hanno la possibilità di costruire e restituire zero o più listener.
  * Qualsiasi listener restituito viene aperto per la comunicazione con il servizio.
  * Viene chiamato il metodo `runAsync` del servizio, in modo che il servizio stesso possa eseguire operazioni a esecuzione prolungata o in background.
* Durante l'arresto:
  * Il token di annullamento che è stato passato a `runAsync` viene annullato e i listener vengono chiusi.
  * L'oggetto servizio stesso viene eliminato.

L'ordine degli eventi in Reliable Services potrebbe cambiare leggermente a seconda che il servizio Reliable sia con o senza stato. 

Inoltre, per i servizi con stato, è necessario gestire lo scenario di scambio del ruolo primario. Durante questa sequenza il ruolo di primario viene trasferito a un'altra replica (o ritorna) senza che il servizio venga arrestato. 

Infine è necessario considerare le condizioni di errore.

## <a name="stateless-service-startup"></a>Avvio di un servizio senza stato
Il ciclo di vita di un servizio senza stato è piuttosto semplice. Di seguito è riportato l'ordine degli eventi:

1. Il servizio viene costruito.
2. Questi eventi si verificano in parallelo:
    - Viene richiamato `StatelessService.createServiceInstanceListeners()` e i listener restituiti vengono aperti. Viene chiamato `CommunicationListener.openAsync()` su ciascun listener.
    - Viene chiamato il metodo `runAsync` del servizio (`StatelessService.runAsync()`).
3. Se presente, viene chiamato il metodo `onOpenAsync` del servizio. Nello specifico, viene chiamato `StatelessService.onOpenAsync()`. Si tratta di un override insolito ma comunque disponibile.

È importante notare che non vi è alcun ordine tra la chiamata per creare e aprire i listener e la chiamata di `runAsync`. I listener potrebbero aprirsi prima che venga avviato `runAsync`. Analogamente, `runAsync` potrebbe essere chiamato prima che i listener di comunicazione siano aperti o addirittura prima che siano stati creati. Se è necessaria una sincronizzazione, deve essere eseguita dall'implementazione. Ecco alcune delle soluzioni comuni:

* Talvolta i listener non possono funzionare finché non vengono create altre informazioni o eseguite altre operazioni. Per i servizi senza stato, queste operazioni sono in genere eseguibili nel costruttore del servizio. Possono essere effettuate durante la chiamata di `createServiceInstanceListeners()` o nell'ambito della costruzione del listener stesso.
* In alcuni casi il codice di `runAsync` non viene avviato finché i listener sono aperti. In questo caso, è necessario un po' di coordinamento aggiuntivo. Una soluzione comune consiste nell'aggiungere un flag nei listener. Il flag indica quando i listener sono terminati. Il metodo `runAsync` esegue questa verifica prima di continuare il lavoro effettivo.

## <a name="stateless-service-shutdown"></a>Arresto di un servizio senza stato
Quando si arresta un servizio senza stato, si segue lo stesso modello, ma in senso inverso:

1. Questi eventi si verificano in parallelo:
    - Tutti i listener aperti vengono chiusi. Viene chiamato `CommunicationListener.closeAsync()` su ciascun listener.
    - Il token di annullamento che è stato passato a `runAsync()` viene annullato. La verifica della proprietà `isCancelled` del token di annullamento restituisce `true` e, se viene chiamato, il metodo `throwIfCancellationRequested` del token genera un'eccezione `CancellationException`.
2. Al completamento di `closeAsync()` in ogni listener e al completamento di `runAsync()`, viene chiamato il metodo `StatelessService.onCloseAsync()` del servizio, se presente. Anche in questo caso, non si tratta di un override comune.
3. Dopo il completamento di `StatelessService.onCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-startup"></a>Avvio di un servizio con stato
I servizi con stato hanno un modello simile ai servizi senza stato, con poche modifiche. L'ordine degli eventi per l'avvio di un servizio con stato è il seguente:

1. Il servizio viene costruito.
2. Viene chiamato `StatefulServiceBase.onOpenAsync()`. L'override della chiamata nel servizio non è comune.
3. Questi eventi si verificano in parallelo:
    - Viene richiamato `StatefulServiceBase.createServiceReplicaListeners()`. 
      - Se il servizio è di tipo primario, tutti i listener restituiti vengono aperti. Viene chiamato `CommunicationListener.openAsync()` su ciascun listener.
      - Se il servizio è di tipo secondario, solo i listener contrassegnati come `listenOnSecondary = true` vengono aperti. La presenza di listener aperti nei servizi secondari è meno comune.
    - Se il servizio è attualmente di tipo primario, viene chiamato il metodo `StatefulServiceBase.runAsync()` del servizio.
4. Dopo che tutte le chiamate di `openAsync()` del listener della replica vengono completate e dopo la chiamata di `runAsync()`, viene chiamato `StatefulServiceBase.onChangeRoleAsync()`. L'override della chiamata nel servizio non è comune.

Analogamente ai servizi senza stato, nel servizio con stato non c'è alcun collegamento tra l'ordine in cui i listener vengono creati e aperti e il momento in cui viene chiamato `runAsync`. Se è necessario che queste operazioni siano coordinate, le soluzioni sono perlopiù simili. Ma c'è un caso aggiuntivo per il servizio con stato. Si supponga che le chiamate in arrivo ai listener di comunicazione richiedano informazioni conservate in alcune [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Poiché i listener di comunicazione potrebbero venire aperti prima che le raccolte Reliable Collections possano essere lette o scritte e prima dell'avvio di `runAsync`, è necessario un po' di coordinamento aggiuntivo. La soluzione più semplice e più comune è che i listener di comunicazione restituiscano un codice di errore che il client usa per ripetere la richiesta.

## <a name="stateful-service-shutdown"></a>Arresto di un servizio con stato
Analogamente ai servizi senza stato, gli eventi del ciclo di vita durante l'arresto corrispondono a quelli durante l'avvio, ma invertiti. Quando viene arrestato un servizio con stato, si verificano gli eventi seguenti:

1. Questi eventi si verificano in parallelo:
    - Tutti i listener aperti vengono chiusi. Viene chiamato `CommunicationListener.closeAsync()` su ciascun listener.
    - Il token di annullamento che è stato passato a `runAsync()` viene annullato. Una chiamata del metodo `isCancelled()` del token di annullamento restituisce `true` e, se viene chiamato, il metodo `throwIfCancellationRequested()` del token genera un'eccezione `OperationCanceledException`.
2. Dopo il completamento di `closeAsync()` in ogni listener e il completamento di `runAsync()`, viene chiamato il metodo `StatefulServiceBase.onChangeRoleAsync()` del servizio. L'override della chiamata nel servizio non è comune.

   > [!NOTE]  
   > È necessario attendere il completamento di `runAsync` solo se la replica è una replica primaria.

3. Dopo il completamento del metodo `StatefulServiceBase.onChangeRoleAsync()`, viene chiamato il metodo `StatefulServiceBase.onCloseAsync()`. Questa chiamata è un override insolito, ma comunque disponibile.
3. Dopo il completamento di `StatefulServiceBase.onCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-primary-swaps"></a>Scambi primari di un servizio con stato
Quando un servizio con stato è in esecuzione, i listener di comunicazione vengono aperti e il metodo `runAsync` viene chiamato solo per le repliche primarie di tali servizi con stato. Le repliche secondarie vengono costruite, ma non ricevono altre chiamate. Quando un servizio con stato è in esecuzione, la replica primaria può cambiare. Gli eventi del ciclo di vita che una replica con stato può vedere dipendono dal fatto che la replica venga abbassata o alzata di livello durante lo scambio.

### <a name="for-the-demoted-primary"></a>Per la replica primaria abbassata di livello
Service Fabric richiede che la replica primaria abbassata di livello interrompa l'elaborazione dei messaggi e qualsiasi attività in background. Questo passaggio è simile a quando il servizio viene arrestato. Una differenza è che il servizio non viene eliminato o chiuso, in quanto rimane come secondario. Si verificano gli eventi seguenti:

1. Questi eventi si verificano in parallelo:
    - Tutti i listener aperti vengono chiusi. Viene chiamato `CommunicationListener.closeAsync()` su ciascun listener.
    - Il token di annullamento che è stato passato a `runAsync()` viene annullato. Una verifica del metodo `isCancelled()` del token di annullamento restituisce `true`. Se chiamato, il metodo `throwIfCancellationRequested()` del token genera un'eccezione `OperationCanceledException`.
2. Dopo il completamento di `closeAsync()` in ogni listener e il completamento di `runAsync()`, viene chiamato il metodo `StatefulServiceBase.onChangeRoleAsync()` del servizio. L'override della chiamata nel servizio non è comune.

### <a name="for-the-promoted-secondary"></a>Per la replica secondaria alzata di livello
In modo analogo, Service Fabric richiede che la replica secondaria alzata di livello inizi ad ascoltare i messaggi in transito e avvii le attività in background necessarie. Questo processo è simile a quando il servizio viene creato. La differenza è che la replica stessa esiste già. Si verificano gli eventi seguenti:

1. Questi eventi si verificano in parallelo:
    - Viene richiamato `StatefulServiceBase.createServiceReplicaListeners()` e i listener restituiti vengono aperti. Viene chiamato `CommunicationListener.openAsync()` su ciascun listener.
    - Viene chiamato il metodo `StatefulServiceBase.runAsync()` del servizio.
2. Dopo che tutte le chiamate di `openAsync()` del listener della replica vengono completate e dopo la chiamata di `runAsync()`, viene chiamato `StatefulServiceBase.onChangeRoleAsync()`. L'override della chiamata nel servizio non è comune.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemi comuni durante l'arresto del servizio con stato e l'abbassamento di livello primario
Service Fabric modifica lo stato primario di un servizio con stato per diversi motivi. Le ragioni più comuni sono il [ribilanciamento del cluster](service-fabric-cluster-resource-manager-balancing.md) e l'[aggiornamento dell'applicazione](service-fabric-application-upgrade.md). Durante queste operazioni, è importante che il servizio rispetti il `cancellationToken`. Questo vale anche durante l'arresto normale del servizio, ad esempio se il servizio è stato eliminato.

I servizi che non gestiscono correttamente l'annullamento possono essere soggetti a diversi problemi. Queste operazioni sono lente perché Service Fabric attende l'arresto normale dei servizi. Questo può infine comportare la mancata riuscita degli aggiornamenti che raggiungono il timeout ed eseguono il rollback. Il mancato rispetto del token di annullamento può anche provocare uno sbilanciamento dei cluster. I cluster diventano sbilanciati perché viene eseguito un accesso frequente ai nodi. Tuttavia i servizi non possono essere ribilanciati in quanto il loro spostamento richiede troppo tempo. 

Trattandosi di servizi con stato, è anche probabile che i servizi usino [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). In Service Fabric, quando un servizio primario viene abbassato di livello, una delle prime cose che accade è che viene revocato l'accesso in scrittura allo stato sottostante. Ciò comporta una seconda serie di problemi che potrebbero influire sul ciclo di vita del servizio. Le raccolte restituiscono eccezioni in base alla tempistica e al fatto che la replica venga spostata o arrestata. È importante gestire queste eccezioni correttamente. 

Le eccezioni generate da Service Fabric sono permanenti [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception) o temporanee [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception). Le eccezioni permanenti dovrebbero essere registrate e generate, mentre quelle temporanee possono essere ripetute in base a una logica di ripetizione.

Una parte importante del test e della convalida di Reliable Services consiste nella gestione delle eccezioni che derivano dall'uso di `ReliableCollections` in combinazione con gli eventi del ciclo di vita del servizio. Si consiglia sempre di eseguire il servizio in condizioni di carico. È inoltre opportuno eseguire aggiornamenti e [test CHAOS](service-fabric-controlled-chaos.md) prima della distribuzione nell'ambiente di produzione. Questi passaggi di base contribuiscono ad assicurare che il servizio sia implementato correttamente e che gestisca gli eventi del ciclo di vita nel modo giusto.

## <a name="notes-on-service-lifecycle"></a>Note sul ciclo di vita del servizio
* Entrambe le chiamate del metodo `runAsync()` e di `createServiceInstanceListeners/createServiceReplicaListeners` sono facoltative. Un servizio potrebbe averne una, entrambe o nessuna. Ad esempio, se il servizio esegue tutte le attività in risposta alle chiamate dell'utente, non è necessario implementare `runAsync()`. Sono necessari solo i listener di comunicazione e il codice associato. 

  Analogamente, la creazione e la restituzione di listener di comunicazione sono facoltative. Il servizio potrebbe avere solo attività in background da eseguire, pertanto è necessario implementare solo `runAsync()`.
* È un comportamento valido per un servizio completare `runAsync()` correttamente e ritornare. Questa non è considerata una condizione di errore e rappresenta il completamento dell'attività in background del servizio. Per i servizi Reliable Services con stato viene chiamato nuovamente `runAsync()` se il servizio è stato abbassato dal livello primario e poi alzato di nuovo al livello primario.
* Se un servizio esce da `runAsync()` generando un'eccezione imprevista, questo è un errore. L'oggetto servizio viene arrestato e viene segnalato un errore di integrità.
* Anche se non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere. Pertanto, non è possibile completare alcuna operazione effettiva. Si consiglia di completare al più presto la richiesta di annullamento ricevuta. Se il servizio non risponde a queste chiamate API entro un intervallo di tempo ragionevole, Service Fabric potrebbe terminare forzatamente il servizio. In genere ciò accade solo durante gli aggiornamenti delle applicazioni o quando viene eliminato un servizio. Questo timeout è di 15 minuti per impostazione predefinita.
* Gli errori nel percorso `onCloseAsync()` comportano la chiamata di `onAbort()`. Questa chiamata rappresenta l'ultima e migliore opportunità del servizio di pulire e rilasciare le risorse che sono state richieste.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start-java.md)
* [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)
