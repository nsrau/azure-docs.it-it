---
title: Panoramica del ciclo di vita di Reliable Services di Azure Service Fabric | Documentazione Microsoft
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
ms.date: 01/05/2017
ms.author: masnider;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1237f467cf993170eaf1345134fb732d4f0b1171
ms.lasthandoff: 04/03/2017

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
    - Viene chiamato `StatelessService.CreateServiceInstanceListeners()` e viene aperto qualsiasi listener restituito (viene chiamato `ICommunicationListener.OpenAsync()` su ogni listener)
    - Viene chiamato il metodo RunAsync del servizio (`StatelessService.RunAsync()`)
3. Se è presente, viene chiamato il metodo OnOpenAsync del servizio (in particolare, viene chiamato `StatelessService.OnOpenAsync()`; si tratta di un override insolito ma comunque disponibile).

È importante notare che non vi è alcun ordine tra le chiamate per creare e aprire i listener e RunAsync. I listener possono essere aperti prima dell'avvio di RunAsync. Analogamente RunAsync potrebbe essere chiamato prima che i listener di comunicazione siano aperti o siano persino stati creati. Se è necessaria una sincronizzazione, la si lascia come esercizio per il responsabile dell'implementazione. Soluzioni comuni:

* Talvolta i listener non possono funzionare fino a quando non vengono create altre informazioni o eseguite determinate operazioni. Per i servizi senza stato queste operazioni solitamente possono essere eseguite nel costruttore del servizio, durante la chiamata di `CreateServiceInstanceListeners()` o come parte della costruzione del listener stesso.
* In alcuni casi il codice in RunAsync non si avvia fino a quando i listener non vengono aperti. In questo caso è necessario un po' di coordinamento in più. Una soluzione comune è un flag interno ai listener che indica quando vengono completati e viene controllato in RunAsync prima di procedere con il lavoro effettivo.

## <a name="stateless-service-shutdown"></a>Arresto di un servizio senza stato
Quando si arresta un servizio senza stato, si segue lo stesso modello, solo in senso inverso:

1. In parallelo
    - Tutti i listener aperti vengono chiusi (viene chiamato `ICommunicationListener.CloseAsync()` su ogni listener)
    - Il token di annullamento passato a `RunAsync()` viene annullato (il controllo della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se viene chiamato, il metodo `ThrowIfCancellationRequested` del token restituisce `OperationCanceledException`)
2. Dopo il completamento di `CloseAsync()` su ogni listener e il completamento di `RunAsync()`, viene chiamato il metodo `StatelessService.OnCloseAsync()` del servizio, se è presente (anche in questo caso si tratta di un override insolito).
3. Dopo il completamento di `StatelessService.OnCloseAsync()`, l'oggetto servizio viene eliminato

## <a name="stateful-service-startup"></a>Avvio di un servizio con stato
I servizi con stato hanno un modello simile ai servizi senza stato, con poche modifiche. Quando si avvia un servizio con stato, l'ordine degli eventi è il seguente:

1. Il servizio viene costruito
2. Viene chiamato `StatefulServiceBase.OnOpenAsync()`. Viene eseguito un overridde insolito nel servizio.
3. Se la replica del servizio in questione è primaria, si verificano in parallelo le seguenti cose, in caso contrario il servizio salta al passaggio 4
    - Viene chiamato `StatefulServiceBase.CreateServiceReplicaListeners()` e viene aperto qualsiasi listener restituito (viene chiamato `ICommunicationListener.OpenAsync()` su ogni listener)
    - Viene chiamato il metodo RunAsync del servizio (`StatefulServiceBase.RunAsync()`)
4. Dopo il completamento di tutte le chiamate `OpenAsync()` del listener di replica e l'avvio di `RunAsync()` (o se questi passaggi sono stati ignorati perché la replica è attualmente secondaria), viene chiamato `StatefulServiceBase.OnChangeRoleAsync()`. Viene eseguito un overridde insolito nel servizio.

Analogamente ai servizi senza stato, non esiste alcun coordinamento tra l'ordine in cui viene completata la creazione e l'apertura dei listener e viene chiamato RunAsync. Le soluzioni sono in gran parte le stesse, con un caso in più: si supponga che le chiamate in arrivo ai listener di comunicazione, per funzionare, richiedano informazioni mantenute all'interno di alcune [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Poiché i listener di comunicazione potrebbero essere aperti prima che le raccolte Reliable Collections siano leggibili o scrivibili e prima che RunAsync si avvii, è necessario un po' di coordinamento aggiuntivo. La soluzione più semplice e più comune è che i listener di comunicazione restituiscano un codice di errore che il client usa per ripetere la richiesta.

## <a name="stateful-service-shutdown"></a>Arresto di un servizio con stato
Analogamente ai servizi senza stato, gli eventi del ciclo di vita durante l'arresto sono gli stesse dell'avvio, ma invertiti. Quando viene arrestato un servizio con stato, si verificano gli eventi seguenti:

1. In parallelo
    - Tutti i listener aperti vengono chiusi (viene chiamato `ICommunicationListener.CloseAsync()` su ogni listener)
    - Il token di annullamento passato a `RunAsync()` viene annullato (il controllo della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se viene chiamato, il metodo `ThrowIfCancellationRequested` del token restituisce `OperationCanceledException`)
2. Dopo il completamento di `CloseAsync()` su ogni listener e il completamento di `RunAsync()` (che sarà stato necessario solo nel caso di replica del servizio primaria), viene chiamato il servizio `StatefulServiceBase.OnChangeRoleAsync()`. Viene eseguito un overridde insolito nel servizio.
3. Dopo il completamento del metodo `StatefulServiceBase.OnChangeRoleAsync()`, viene chiamato il metodo `StatefulServiceBase.OnCloseAsync()` (anche in questo caso l'override è insolito ma comunque disponibile).
3. Dopo il completamento di `StatefulServiceBase.OnCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-primary-swaps"></a>Scambi primari di un servizio con stato
Durante l'esecuzione di un servizio con stato, solo per le repliche primarie di quei servizi con stato vengono aperti i listener di comunicazione e viene chiamato il metodo RunAsync. Quelli secondari vengono costruiti ma non vedono ulteriori chiamate. Durante l'esecuzione di un servizio con stato, tuttavia, è possibile cambiare la replica che attualmente è primaria. Che cosa significa questo in termini di eventi del ciclo di vita che una replica può vedere? Il comportamento che la replica con stato vede dipende dal fatto che la replica venga abbassata o alzata di livello durante lo scambio.

### <a name="for-the-primary-being-demoted"></a>Se la replica primaria viene abbassata di livello
Service Fabric ha bisogno che questa replica arresti l'elaborazione dei messaggi ed esca da qualsiasi attività in background che sta eseguendo. Di conseguenza questo passaggio è simile a quando il servizio viene arrestato. Una differenza è che il servizio non viene eliminato o chiuso, in quanto rimane come secondario. Vengono chiamate le API seguenti:

1. In parallelo
    - Tutti i listener aperti vengono chiusi (viene chiamato `ICommunicationListener.CloseAsync()` su ogni listener)
    - Il token di annullamento passato a `RunAsync()` viene annullato (il controllo della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se viene chiamato, il metodo `ThrowIfCancellationRequested` del token restituisce `OperationCanceledException`)
2. Dopo il completamento di `CloseAsync()` su ogni listener e il completamento di `RunAsync()`, viene chiamato il servizio `StatefulServiceBase.OnChangeRoleAsync()`. Viene eseguito un overridde insolito nel servizio.

### <a name="for-the-secondary-being-promoted"></a>Se la replica secondaria viene alzata di livello
Service Fabric ha bisogno analogamente che questa replica inizi ad ascoltare i messaggi in transito (se lo fa) e avvii le attività necessarie in background. Di conseguenza questo processo è simile a quando viene creato il servizio, ad eccezione del fatto che la replica esiste già. Vengono chiamate le API seguenti:

1. In parallelo
    - Viene chiamato `StatefulServiceBase.CreateServiceReplicaListeners()` e viene aperto qualsiasi listener restituito (viene chiamato `ICommunicationListener.OpenAsync()` su ogni listener)
    - Viene chiamato il metodo RunAsync del servizio (`StatefulServiceBase.RunAsync()`)
4. Dopo il completamento di tutte le chiamate di `OpenAsync()` del listener di replica e l'avvio di `RunAsync()` (o se questi passaggi sono stati ignorati perché la replica è secondaria), viene chiamato `StatefulServiceBase.OnChangeRoleAsync()`. Viene eseguito un overridde insolito nel servizio.

## <a name="notes-on-service-lifecycle"></a>Note sul ciclo di vita del servizio
* Entrambe le chiamate del metodo `RunAsync()` e di `CreateServiceReplicaListeners/CreateServiceInstanceListeners` sono facoltative. Un servizio può averne una, entrambe o nessuna. Ad esempio, se il servizio esegue tutte le attività in risposta alle chiamate dell'utente, non è necessario implementare `RunAsync()`. Sono necessari solo i listener di comunicazione e il codice associato. Analogamente creare e restituire i listener di comunicazione è facoltativo, in quanto il servizio potrebbe avere solo operazioni in background da eseguire e perciò richiedere solo l'implementazione di `RunAsync()`
* È un comportamento valido per un servizio completare `RunAsync()` correttamente e ritornare. Questa non è considerata una condizione di errore e rappresenterebbe il completamento dell'attività in background del servizio. Per i servizi Reliable Services con stato viene chiamato nuovamente `RunAsync()` se il servizio è stato abbassato dal livello primario e poi alzato di nuovo al livello primario.
* Se un servizio esce da `RunAsync()` generando un'eccezione imprevista, questo è un errore: l'oggetto servizio viene arrestato e viene segnalato un errore di integrità.
* Mentre non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere nelle raccolte Reliable Collections e quindi non è possibile completare alcuna operazione effettiva. È consigliabile completare al più presto la richiesta di annullamento ricevuta. Se il servizio non risponde a queste chiamate API entro un intervallo di tempo ragionevole, Service Fabric può terminare forzatamente il servizio. In genere ciò accade solo durante gli aggiornamenti delle applicazioni o quando viene eliminato un servizio. Questo timeout è di 15 minuti per impostazione predefinita.
* Per i servizi con stato esiste un'altra opzione per ServiceReplicaListeners che consente il loro avvio sulle repliche secondarie. Questa situazione è poco frequente e l'unica modifica nel ciclo di vita è che viene chiamato `CreateServiceReplicaListeners()` (e vengono aperti i listener risultanti) anche se la replica è secondaria. Analogamente, se la replica viene convertita in seguito in primaria, i listener vengono chiusi ed eliminati e ne vengono creati e aperti di nuovi come parte della conversione in replica primaria.
* Gli errori nel percorso `OnCloseAsync()` generano la chiamata di `OnAbort()` che rappresenta l'ultima e migliore opportunità del servizio di pulire e rilasciare le risorse che sono state richieste.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)

