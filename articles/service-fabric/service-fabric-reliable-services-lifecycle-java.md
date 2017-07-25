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
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ddec69e57e84f33c37831a0da2c21955d78fff98
ms.contentlocale: it-it
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

## <a name="notes-on-service-lifecycle"></a>Note sul ciclo di vita del servizio
* Entrambe le chiamate del metodo `runAsync()` e di `createServiceInstanceListeners` sono facoltative. Un servizio può averne una, entrambe o nessuna. Ad esempio, se il servizio esegue tutte le attività in risposta alle chiamate dell'utente, non è necessario implementare `runAsync()`. Sono necessari solo i listener di comunicazione e il codice associato. Analogamente creare e restituire i listener di comunicazione è facoltativo, in quanto il servizio potrebbe avere solo operazioni in background da eseguire e perciò richiedere solo l'implementazione di `runAsync()`
* È un comportamento valido per un servizio completare `runAsync()` correttamente e ritornare. Questa non è considerata una condizione di errore e rappresenterebbe il completamento dell'attività in background del servizio. Per i servizi Reliable Services con stato viene chiamato nuovamente `runAsync()` se il servizio è stato abbassato dal livello primario e poi alzato di nuovo al livello primario.
* Se un servizio esce da `runAsync()` generando un'eccezione imprevista, questo è un errore: l'oggetto servizio viene arrestato e viene segnalato un errore di integrità.
* Mentre non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere e quindi non è possibile completare alcuna operazione effettiva. È consigliabile completare al più presto la richiesta di annullamento ricevuta. Se il servizio non risponde a queste chiamate API entro un intervallo di tempo ragionevole, Service Fabric può terminare forzatamente il servizio. In genere ciò accade solo durante gli aggiornamenti delle applicazioni o quando viene eliminato un servizio. Questo timeout è di 15 minuti per impostazione predefinita.
* Gli errori nel percorso `onCloseAsync()` generano la chiamata di `onAbort()` che rappresenta l'ultima e migliore opportunità del servizio di pulire e rilasciare le risorse che sono state richieste.

> [!NOTE]
> Reliable Services con stato non è ancora supportato in Java.
>
>

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)

