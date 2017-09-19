---
title: Panoramica del ciclo di vita di Reliable Services di Azure Service Fabric | Microsoft Docs
description: Informazioni sui diversi eventi del ciclo di vita di Reliable Services di Service Fabric
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 3ff04f031b2d170ac63fc1c6f4e2b6ba6a6c46bd
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---

# <a name="reliable-services-lifecycle-overview"></a>Panoramica del ciclo di vita di Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java su Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando si pensa al ciclo di vita di Reliable Services, le nozioni di base del ciclo di vita sono quelle più importanti. In generale:

- Durante l'avvio
  - I servizi vengono costruiti
  - Hanno la possibilità di creare e restituire zero o più listener
  - Qualsiasi listener restituito è aperto, consentendo la comunicazione con il servizio
  - Viene chiamato il metodo RunAsync del servizio che consente al servizio stesso di eseguire operazioni a esecuzione prolungata o in background
- Durante l'arresto
  - Il token di annullamento passato a RunAsync viene cancellato e i listener vengono chiusi
  - Una volta completata l'operazione, viene eliminato l'oggetto servizio stesso

Sono disponibili informazioni dettagliate sull'esatto ordine di questi eventi. In particolare, l'ordine degli eventi potrebbe cambiare leggermente a seconda che il servizio Reliable sia con o senza stato. Inoltre, per i servizi con stato, è necessario affrontare lo scenario di scambio del ruolo di primario. Durante tale sequenza il ruolo di primario viene trasferito a un'altra replica (o ritorna) senza l'arresto del servizio. Infine è necessario considerare le condizioni di errore.

## <a name="stateless-service-startup"></a>Avvio di un servizio senza stato
Il ciclo di vita di un servizio senza stato è piuttosto semplice. Di seguito è riportato l'ordine degli eventi:

1. Il servizio viene costruito
2. Quindi si verificano due cose in parallelo:
    - Viene richiamato `StatelessService.CreateServiceInstanceListeners()` ed eventuali listener restituito hanno lo stato Aperto. Viene chiamato `ICommunicationListener.OpenAsync()` su ciascun listener.
    - Viene chiamato il metodo `StatelessService.RunAsync()` del servizio.
3. Se presente, viene chiamato il metodo `StatelessService.OnOpenAsync()` del servizio. Si tratta di un override insolito ma comunque disponibile.

È importante notare che non vi è alcun ordine tra le chiamate per creare e aprire i listener e RunAsync. I listener possono essere aperti prima dell'avvio di RunAsync. Analogamente RunAsync potrebbe essere chiamato prima che i listener di comunicazione siano aperti o siano persino stati creati. Se è necessaria una sincronizzazione, la si lascia come esercizio per il responsabile dell'implementazione. Soluzioni comuni:

  - Talvolta i listener non possono funzionare fino a quando non vengono create altre informazioni o eseguite determinate operazioni. Per i servizi senza stato le cui operazioni possono essere in genere eseguite in altre posizioni, ad esempio: 
    - nel costruttore del servizio
    - mentre viene chiamato `CreateServiceInstanceListeners()`
    - nell'ambito della costruzione del listener stesso
  - In alcuni casi il codice in RunAsync non si avvia fino a quando i listener non vengono aperti. In questo caso è necessario un po' di coordinamento in più. Una soluzione comune è un flag interno ai listener che indica quando vengono completati. Questo flag viene quindi verificato in RunAsync prima di continuare con le operazioni effettive.

## <a name="stateless-service-shutdown"></a>Arresto di un servizio senza stato
Quando si arresta un servizio senza stato, si segue lo stesso modello, solo in senso inverso:

1. In parallelo
    - Gli eventuali listener aperti vengono chiusi. Viene chiamato `ICommunicationListener.CloseAsync()` su ciascun listener.
    - Il token di annullamento passato a `RunAsync()` viene annullato. La verifica della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se chiamato, il metodo `ThrowIfCancellationRequested` del token genera `OperationCanceledException`.
2. Dopo il completamento di `CloseAsync()` su ciascun listener e il completamento di `RunAsync()`, viene chiamato il metodo `StatelessService.OnCloseAsync()` del servizio, se presente. L'override di `StatelessService.OnCloseAsync()` è insolito.
3. Dopo il completamento di `StatelessService.OnCloseAsync()`, l'oggetto servizio viene eliminato

## <a name="stateful-service-startup"></a>Avvio di un servizio con stato
I servizi con stato hanno un modello simile ai servizi senza stato, con poche modifiche. Quando si avvia un servizio con stato, l'ordine degli eventi è il seguente:

1. Il servizio viene costruito
2. Viene chiamato `StatefulServiceBase.OnOpenAsync()`. È insolito che venga eseguito l'override nel servizio.
3. Vengono eseguite contemporaneamente le operazioni seguenti
    - Viene richiamato `StatefulServiceBase.CreateServiceReplicaListeners()`. 
      - Se il servizio è di tipo primario tutti i listener restituiti sono aperti. Viene chiamato `ICommunicationListener.OpenAsync()` su ciascun listener.
      - Se il servizio è di tipo secondario, solo i listener contrassegnato come `ListenOnSecondary = true` sono aperti. È meno comune che i listener in un servizio secondario siano aperti.
    - Se attualmente il servizio è di tipo primario, viene chiamato il metodo `StatefulServiceBase.RunAsync()` del servizio.
4. Una volta che tutte le repliche delle chiamate `OpenAsync()` del listener vengono completate e viene chiamato `RunAsync()`, viene chiamato `StatefulServiceBase.OnChangeRoleAsync()`. È insolito che venga eseguito l'override nel servizio.

Analogamente ai servizi senza stato, non c'è alcun collegamento tra l'ordine in cui vengono completate la creazione e l'apertura dei listener e il momento in cui viene chiamato RunAsync. Se è necessario che queste operazioni siano coordinate, le soluzioni sono perlopiù simili. C'è un altro caso: si supponga che le chiamate in arrivo ai listener di comunicazione richiedano informazioni conservate in alcune [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Poiché i listener di comunicazione potrebbero essere aperti prima che le raccolte Reliable Collections siano leggibili o scrivibili e prima che RunAsync si avvii, è necessario un po' di coordinamento aggiuntivo. La soluzione più semplice e più comune è che i listener di comunicazione restituiscano un codice di errore che il client usa per ripetere la richiesta.

## <a name="stateful-service-shutdown"></a>Arresto di un servizio con stato
Analogamente ai servizi senza stato, gli eventi del ciclo di vita durante l'arresto sono gli stesse dell'avvio, ma invertiti. Quando viene arrestato un servizio con stato, si verificano gli eventi seguenti:

1. In parallelo
    - Gli eventuali listener aperti vengono chiusi. Viene chiamato `ICommunicationListener.CloseAsync()` su ciascun listener.
    - Il token di annullamento passato a `RunAsync()` viene annullato. La verifica della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se chiamato, il metodo `ThrowIfCancellationRequested` del token genera `OperationCanceledException`.
2. Dopo il completamento di `CloseAsync()` su ogni listener e il completamento di `RunAsync()`, viene chiamato il servizio `StatefulServiceBase.OnChangeRoleAsync()`. Viene eseguito un overridde insolito nel servizio.
    - È necessario attendere il completamento di RunAsync solo se la replica di questo servizio era di tipo Primario.
3. Quando il metodo `StatefulServiceBase.OnChangeRoleAsync()` viene completato, viene chiamato il metodo `StatefulServiceBase.OnCloseAsync()`. Si tratta di un override insolito ma comunque disponibile.
3. Dopo il completamento di `StatefulServiceBase.OnCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-primary-swaps"></a>Scambi primari di un servizio con stato
Durante l'esecuzione di un servizio con stato, solo per le repliche primarie di quei servizi con stato vengono aperti i listener di comunicazione e viene chiamato il metodo RunAsync. Quelli secondari vengono costruiti ma non vedono ulteriori chiamate. Durante l'esecuzione di un servizio con stato, tuttavia, è possibile cambiare la replica che attualmente è primaria. Che cosa significa questo in termini di eventi del ciclo di vita che una replica può vedere? Il comportamento che la replica con stato vede dipende dal fatto che la replica venga abbassata o alzata di livello durante lo scambio.

### <a name="for-the-primary-being-demoted"></a>Se la replica primaria viene abbassata di livello
Service Fabric ha bisogno che questa replica arresti l'elaborazione dei messaggi ed esca da qualsiasi attività in background che sta eseguendo. Di conseguenza questo passaggio è simile a quando il servizio viene arrestato. Una differenza è che il servizio non viene eliminato o chiuso, in quanto rimane come secondario. Vengono chiamate le API seguenti:

1. In parallelo
    - Gli eventuali listener aperti vengono chiusi. Viene chiamato `ICommunicationListener.CloseAsync()` su ciascun listener.
    - Il token di annullamento passato a `RunAsync()` viene annullato. La verifica della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se chiamato, il metodo `ThrowIfCancellationRequested` del token genera `OperationCanceledException`.
2. Dopo il completamento di `CloseAsync()` su ogni listener e il completamento di `RunAsync()`, viene chiamato il servizio `StatefulServiceBase.OnChangeRoleAsync()`. È insolito che venga eseguito l'override nel servizio.

### <a name="for-the-secondary-being-promoted"></a>Se la replica secondaria viene alzata di livello
In modo analogo, Service Fabric ha bisogno che questa replica inizi ad ascoltare i messaggi in transito e avvii le attività necessarie in background. Di conseguenza questo processo è simile a quando viene creato il servizio, ad eccezione del fatto che la replica esiste già. Vengono chiamate le API seguenti:

1. In parallelo
    - Viene richiamato `StatefulServiceBase.CreateServiceReplicaListeners()` e gli eventuali listener restituiti sono aperti. Viene chiamato `ICommunicationListener.OpenAsync()` su ciascun listener.
    - Viene chiamato il metodo `StatefulServiceBase.RunAsync()` del servizio.
2. Una volta che tutte le repliche delle chiamate `OpenAsync()` del listener  vengono completate ed è stato chiamato `RunAsync()`, viene chiamato `StatefulServiceBase.OnChangeRoleAsync()`. È insolito che venga eseguito l'override nel servizio.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemi comuni durante l'arresto del servizio con stato e l'abbassamento di livello primario
Service Fabric modifica lo stato Primario di un servizio con stato per una serie di motivi. I più comuni sono il [ribilanciamento del cluster](service-fabric-cluster-resource-manager-balancing.md) e l'[aggiornamento dell'applicazione](service-fabric-application-upgrade.md). Durante queste operazioni (nonché durante il normale arresto del servizio, come è possibile notare in caso di eliminazione del servizio), è importante che il servizio rispetti `CancellationToken`. I servizi che non gestiscono correttamente l'annullamento sperimenteranno diversi problemi. In particolare, queste operazioni saranno lente poiché Service Fabric attende dell'arresto normale dei servizi. Questo può infine comportare la mancata riuscita degli aggiornamenti che raggiungono timeout ed eseguono il rollback. La mancata accettazione del token di annullamento può provocare cluster sbilanciati, perché i nodi si riscaldano, ma i servizi non possono essere ribilanciati, dal momento che ci vuole troppo tempo per spostarli altrove. 

Poiché si tratta di servizi con stato, è inoltre probabile che usino [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). In Service Fabric, quando un servizio primario viene abbassato di livello, una delle prime cose che accade è che viene revocato l'accesso in scrittura allo stato sottostante. Ciò comporta un secondo set di problemi che possono influire sul ciclo di vita del servizio. Le raccolte restituiscono eccezioni in base alla tempistica e al fatto che la replica sia spostata o arrestata. Queste eccezioni devono essere gestite correttamente. Le eccezioni generate da Service Fabric rientrano nelle categorie permanente [(`FabricException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e temporanea [(`FabricTransientException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet). Le eccezioni permanenti devono essere registrate e generate, mentre le eccezioni temporanee possono essere ripetute in base a una logica di ripetizione.

La gestione delle eccezioni che derivano dall'uso di `ReliableCollections` in combinazione con gli eventi del ciclo di vita del servizio è una parte importante di test e convalida di un servizio Reliable. Si consiglia sempre di eseguire il servizio in condizioni di carico durante l'esecuzione di aggiornamenti e [test di caos](service-fabric-controlled-chaos.md) prima della distribuzione nell'ambiente di produzione. Questi passaggi di base contribuiscono ad assicurare che il servizio sia implementato correttamente e gestisca gli eventi del ciclo di vita nel modo giusto.


## <a name="notes-on-service-lifecycle"></a>Note sul ciclo di vita del servizio
  - Entrambe le chiamate del metodo `RunAsync()` e di `CreateServiceReplicaListeners/CreateServiceInstanceListeners` sono facoltative. Un servizio può averne una, entrambe o nessuna. Ad esempio, se il servizio esegue tutte le attività in risposta alle chiamate dell'utente, non è necessario implementare `RunAsync()`. Sono necessari solo i listener di comunicazione e il codice associato. Analogamente creare e restituire i listener di comunicazione è facoltativo, in quanto il servizio potrebbe avere solo operazioni in background da eseguire e perciò richiedere solo l'implementazione di `RunAsync()`
  - È un comportamento valido per un servizio completare `RunAsync()` correttamente e ritornare. Il completamento non è una condizione di errore. Il completamento di `RunAsync()` indica che le operazioni in background del servizio sono state completate. Per i servizi Reliable con stato viene chiamato nuovamente `RunAsync()` se le repliche sono state abbassate di livello da primarie a secondarie e poi alzate di nuovo al livello primario.
  - Se un servizio esce da `RunAsync()` generando un'eccezione imprevista, questo è un errore. L'oggetto del servizio viene arrestato e viene segnalato un errore di integrità.
  - Mentre non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere nelle raccolte Reliable Collections e quindi non è possibile completare alcuna operazione effettiva. È consigliabile completare al più presto la richiesta di annullamento ricevuta. Se il servizio non risponde a queste chiamate API entro un intervallo di tempo ragionevole, Service Fabric può terminare forzatamente il servizio. In genere ciò accade solo durante gli aggiornamenti delle applicazioni o quando viene eliminato un servizio. Questo timeout è di 15 minuti per impostazione predefinita.
  - Gli errori nel percorso `OnCloseAsync()` generano la chiamata di `OnAbort()` che rappresenta l'ultima e migliore opportunità del servizio di pulire e rilasciare le risorse che sono state richieste.

## <a name="next-steps"></a>Passaggi successivi
- [Introduzione a Reliable Services](service-fabric-reliable-services-introduction.md)
- [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
- [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)

