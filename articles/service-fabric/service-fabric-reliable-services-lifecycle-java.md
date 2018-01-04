---
title: Panoramica del ciclo di vita di Reliable Services di Azure Service Fabric | Microsoft Docs
description: Informazioni sui diversi eventi del ciclo di vita di Reliable Services di Service Fabric
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 93fd003ff5ba7673e5a719fb1ced0cbb97034610
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Panoramica del ciclo di vita di Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java su Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando si pensa al ciclo di vita di Reliable Services, le nozioni di base del ciclo di vita sono quelle più importanti. In generale:

* Durante l'avvio
  * I servizi vengono costruiti
  * Hanno la possibilità di creare e restituire zero o più listener
  * Qualsiasi listener restituito è aperto, consentendo la comunicazione con il servizio
  * Viene chiamato il metodo RunAsync del servizio che consente al servizio stesso di eseguire operazioni a esecuzione prolungata o in background
* Durante l'arresto
  * Il token di annullamento passato a RunAsync viene cancellato e i listener vengono chiusi
  * Una volta completata l'operazione, viene eliminato l'oggetto servizio stesso

Sono disponibili informazioni dettagliate sull'esatto ordine di questi eventi. In particolare, l'ordine degli eventi potrebbe cambiare leggermente a seconda che il servizio Reliable sia con o senza stato. Inoltre, per i servizi con stato, è necessario affrontare lo scenario di scambio del ruolo di primario. Durante tale sequenza il ruolo di primario viene trasferito a un'altra replica (o ritorna) senza l'arresto del servizio. Infine è necessario considerare le condizioni di errore.

## <a name="stateless-service-startup"></a>Avvio di un servizio senza stato
Il ciclo di vita di un servizio senza stato è piuttosto semplice. Di seguito è riportato l'ordine degli eventi:

1. Il servizio viene costruito
2. Quindi si verificano due cose in parallelo:
    - Viene chiamato `StatelessService.createServiceInstanceListeners()` e viene aperto qualsiasi listener restituito (viene chiamato `CommunicationListener.openAsync()` su ogni listener)
    - Viene chiamato il metodo RunAsync (`StatelessService.runAsync()`) del servizio
3. Se è presente, viene chiamato il metodo OnOpenAsync del servizio (in particolare, viene chiamato `StatelessService.onOpenAsync()`; si tratta di un override insolito ma comunque disponibile).

È importante notare che non vi è alcun ordine tra le chiamate per creare e aprire i listener e RunAsync. I listener possono essere aperti prima dell'avvio di RunAsync. Analogamente RunAsync potrebbe essere chiamato prima che i listener di comunicazione vengano aperti o persino creati. Se è necessaria una sincronizzazione, la si lascia come esercizio per il responsabile dell'implementazione. Soluzioni comuni:

* Talvolta i listener non possono funzionare fino a quando non vengono create altre informazioni o eseguite determinate operazioni. Per i servizi senza stato queste operazioni solitamente possono essere eseguite nel costruttore del servizio, durante la chiamata di `createServiceInstanceListeners()` o come parte della costruzione del listener stesso.
* In alcuni casi il codice in RunAsync non si avvia fino a quando i listener sono aperti. In questo caso è necessario un po' di coordinamento in più. Una soluzione comune è un flag interno ai listener che indica quando vengono completati e viene controllato in RunAsync prima di procedere con il lavoro effettivo.

## <a name="stateless-service-shutdown"></a>Arresto di un servizio senza stato
Quando si arresta un servizio senza stato, si segue lo stesso modello, solo in senso inverso:

1. In parallelo
    - Tutti i listener aperti vengono chiusi (viene chiamato `CommunicationListener.closeAsync()` su ogni listener)
    - Il token di annullamento passato a `runAsync()` viene annullato (il controllo della proprietà `isCancelled` del token di annullamento restituisce true e, se viene chiamato, il metodo `throwIfCancellationRequested` del token restituisce `CancellationException`)
2. Dopo il completamento di `closeAsync()` su ogni listener e il completamento di `runAsync()`, viene chiamato il metodo `StatelessService.onCloseAsync()` del servizio, se è presente (anche in questo caso si tratta di un override insolito).
3. Dopo il completamento di `StatelessService.onCloseAsync()`, l'oggetto servizio viene eliminato

## <a name="stateful-service-startup"></a>Avvio di un servizio con stato
I servizi con stato hanno un modello simile ai servizi senza stato, con poche modifiche. Quando si avvia un servizio con stato, l'ordine degli eventi è il seguente:

1. Il servizio viene costruito.
2. Viene chiamato `StatefulServiceBase.onOpenAsync()`. L'override della chiamata nel servizio non è comune.
3. Si verificano gli eventi seguenti, in parallelo:
    - Viene richiamato `StatefulServiceBase.createServiceReplicaListeners()`. 
      - Se il servizio è di tipo primario, tutti i listener restituiti vengono aperti. Viene chiamato `CommunicationListener.openAsync()` su ciascun listener.
      - Se il servizio è di tipo secondario, solo i listener contrassegnati come `listenOnSecondary = true` vengono aperti. La presenza di listener aperti nei servizi secondari è meno comune.
    - Se il servizio è attualmente di tipo primario, viene chiamato il metodo `StatefulServiceBase.runAsync()` del servizio.
4. Dopo che tutte le chiamate di `openAsync()` del listener della replica vengono completate e dopo la chiamata di `runAsync()`, viene chiamato `StatefulServiceBase.onChangeRoleAsync()`. L'override della chiamata nel servizio non è comune.

Analogamente ai servizi senza stati, non è non coordinamento tra l'ordine in cui il listener viene creato e aperto e quando **runAsync** viene chiamato. Se è necessario che queste operazioni siano coordinate, le soluzioni sono perlopiù simili. C'è un caso aggiuntivo per il servizio con stato. Si supponga che le chiamate in arrivo ai listener di comunicazione richiedano informazioni conservate in alcune [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Poiché è stato possibile aprire i listener di comunicazione prima che le raccolte affidabile sono leggibile o scrivibile e prima di **runAsync** è stato possibile avviare, alcuni coordinamento aggiuntivi. La soluzione più semplice e più comune è che i listener di comunicazione restituiscano un codice di errore che il client usa per ripetere la richiesta.

## <a name="stateful-service-shutdown"></a>Arresto di un servizio con stato
Analogamente ai servizi senza stato, gli eventi del ciclo di vita durante l'arresto corrispondono a quelli durante l'avvio, ma invertiti. Quando viene arrestato un servizio con stato, si verificano gli eventi seguenti:

1. In parallelo:
    - Tutti i listener aperti vengono chiusi. Viene chiamato `CommunicationListener.closeAsync()` su ciascun listener.
    - Passata il token di annullamento `runAsync()` è stata annullata. Una chiamata per il token di annullamento `isCancelled()` metodo restituisce true e se viene chiamato, il token `throwIfCancellationRequested()` metodo genera un `OperationCanceledException`.
2. Dopo il completamento di `closeAsync()` in ogni listener e il completamento di `runAsync()`, viene chiamato il metodo `StatefulServiceBase.onChangeRoleAsync()` del servizio. L'override della chiamata nel servizio non è comune.

   > [!NOTE]  
   > La necessità di attendere **runAsync** fine è necessaria solo se la replica è una replica primaria.

3. Dopo il completamento del metodo `StatefulServiceBase.onChangeRoleAsync()`, viene chiamato il metodo `StatefulServiceBase.onCloseAsync()`. Questa chiamata è un override insolito, ma comunque disponibile.
3. Dopo il completamento di `StatefulServiceBase.onCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-primary-swaps"></a>Scambi della replica primaria di un servizio con stato
Durante l'esecuzione di un servizio con stato solo sulle repliche primarie di che i servizi con stato sono i listener di comunicazione aperti e le relative **runAsync** metodo chiamato. Le repliche secondarie vengono costruite, ma non ricevono altre chiamate. Quando un servizio con stato è in esecuzione, la replica primaria può cambiare. Che cosa significa questo in termini di eventi del ciclo di vita che una replica può vedere? Il comportamento che la replica con stato vede dipende dal fatto che la replica venga abbassata o alzata di livello durante lo scambio.

### <a name="for-the-primary-thats-demoted"></a>Per la replica primaria abbassata di livello
Per la replica primaria abbassata di livello, Service Fabric richiede che questa replica interrompa l'elaborazione dei messaggi e qualsiasi attività in background che sta eseguendo. Di conseguenza, questo passaggio è simile a quando il servizio viene arrestato. Una differenza è che il servizio non viene eliminato o chiuso, in quanto rimane come secondario. Vengono chiamate le API seguenti:

1. In parallelo:
    - Tutti i listener aperti vengono chiusi. Viene chiamato `CommunicationListener.closeAsync()` su ciascun listener.
    - Il token di annullamento passato a `runAsync()` viene annullato. Un controllo del token di annullamento `isCancelled()` metodo restituisce true e se viene chiamato, il token `throwIfCancellationRequested()` metodo genera un `OperationCanceledException`.
2. Dopo il completamento di `closeAsync()` in ogni listener e il completamento di `runAsync()`, viene chiamato il metodo `StatefulServiceBase.onChangeRoleAsync()` del servizio. L'override della chiamata nel servizio non è comune.

### <a name="for-the-secondary-thats-promoted"></a>Per la replica secondaria alzata di livello
In modo analogo, Service Fabric richiede che la replica secondaria alzata di livello inizi ad ascoltare i messaggi in transito e avvii le attività in background necessarie. Di conseguenza, questo processo è simile a quando viene creato il servizio, ad eccezione del fatto che la replica esiste già. Vengono chiamate le API seguenti:

1. In parallelo:
    - Viene richiamato `StatefulServiceBase.createServiceReplicaListeners()` e i listener restituiti vengono aperti. Viene chiamato `CommunicationListener.openAsync()` su ciascun listener.
    - Viene chiamato il metodo `StatefulServiceBase.runAsync()` del servizio.
2. Dopo che tutte le chiamate di `openAsync()` del listener della replica vengono completate e dopo la chiamata di `runAsync()`, viene chiamato `StatefulServiceBase.onChangeRoleAsync()`. L'override della chiamata nel servizio non è comune.


### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemi comuni durante l'arresto di un servizio con stato e l'abbassamento di livello di una replica primaria
Service Fabric modifica lo stato Primario di un servizio con stato per una serie di motivi. I più comuni sono il [ribilanciamento del cluster](service-fabric-cluster-resource-manager-balancing.md) e l'[aggiornamento dell'applicazione](service-fabric-application-upgrade.md). Durante queste operazioni (nonché durante il normale arresto del servizio, come è possibile notare in caso di eliminazione del servizio), è importante che il servizio rispetti `cancellationToken`. 

I servizi che non gestiscono correttamente l'annullamento possono essere soggetti a diversi problemi. Queste operazioni sono lente perché Service Fabric attende l'arresto normale dei servizi. Questo può infine comportare la mancata riuscita degli aggiornamenti che raggiungono timeout ed eseguono il rollback. Il mancato rispetto del token di annullamento può anche provocare uno sbilanciamento dei cluster. I cluster diventano sbilanciati perché viene eseguito un accesso frequente ai nodi, ma i servizi non possono essere ribilanciati in quanto il loro spostamento richiede troppo tempo. 

Poiché si tratta di servizi con stato, è anche probabile che usino [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). In Service Fabric, quando un servizio primario viene abbassato di livello, una delle prime cose che accade è che viene revocato l'accesso in scrittura allo stato sottostante. Ciò comporta un secondo set di problemi che possono influire sul ciclo di vita del servizio. Le raccolte restituiscono eccezioni in base alla tempistica e al fatto che la replica venga spostata o arrestata. Queste eccezioni devono essere gestite correttamente. Le eccezioni generate da Service Fabric rientrano nelle categorie permanente [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception) e temporanea [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception). Le eccezioni permanenti devono essere registrate e generate, mentre quelle temporanee possono essere ripetute in base a una logica di ripetizione.

La gestione delle eccezioni che derivano dall'uso di `ReliableCollections` in combinazione con gli eventi del ciclo di vita del servizio è una parte importante di test e convalida di un servizio Reliable. Si consiglia sempre di eseguire il servizio in condizioni di carico durante l'esecuzione di aggiornamenti e [test CHAOS](service-fabric-controlled-chaos.md) prima della distribuzione nell'ambiente di produzione. Questi passaggi di base contribuiscono ad assicurare che il servizio sia implementato correttamente e gestisca gli eventi del ciclo di vita nel modo giusto.

## <a name="notes-on-service-lifecycle"></a>Note sul ciclo di vita del servizio
* Entrambe le chiamate del metodo `runAsync()` e di `createServiceInstanceListeners/createServiceReplicaListeners` sono facoltative. Un servizio può averne una, entrambe o nessuna. Ad esempio, se il servizio esegue tutte le attività in risposta alle chiamate dell'utente, non è necessario implementare `runAsync()`. Sono necessari solo i listener di comunicazione e il codice associato. Analogamente creare e restituire i listener di comunicazione è facoltativo, in quanto il servizio potrebbe avere solo operazioni in background da eseguire e perciò richiedere solo l'implementazione di `runAsync()`
* È un comportamento valido per un servizio completare `runAsync()` correttamente e ritornare. Questa non è considerata una condizione di errore e rappresenterebbe il completamento dell'attività in background del servizio. Per i servizi Reliable Services con stato viene chiamato nuovamente `runAsync()` se il servizio è stato abbassato dal livello primario e poi alzato di nuovo al livello primario.
* Se un servizio esce da `runAsync()` generando un'eccezione imprevista, questo è un errore: l'oggetto servizio viene arrestato e viene segnalato un errore di integrità.
* Mentre non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere e quindi non è possibile completare alcuna operazione effettiva. È consigliabile completare al più presto la richiesta di annullamento ricevuta. Se il servizio non risponde a queste chiamate API entro un intervallo di tempo ragionevole, Service Fabric può terminare forzatamente il servizio. In genere ciò accade solo durante gli aggiornamenti delle applicazioni o quando viene eliminato un servizio. Questo timeout è di 15 minuti per impostazione predefinita.
* Gli errori nel percorso `onCloseAsync()` generano la chiamata di `onAbort()` che rappresenta l'ultima e migliore opportunità del servizio di pulire e rilasciare le risorse che sono state richieste.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start-java.md)
* [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)
