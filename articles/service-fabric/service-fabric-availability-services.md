---
title: "Disponibilità dei servizi di Service Fabric | Microsoft Docs"
description: Descrive il rilevamento degli errori, il failover e il ripristino dei servizi
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 1db7b4bcfa4b7c474a4b0eb4ef469a6cb1fe54a0


---
# <a name="availability-of-service-fabric-services"></a>Disponibilità dei servizi di Service Fabric
Questo articolo contiene una panoramica sul modo in cui Service Fabric gestisce la disponibilità di un servizio.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilità dei servizi di Service Fabric senza stato
I servizi Service Fabric di Azure possono essere con o senza stato. Un servizio senza stato è un servizio dell'applicazione che non dispone di alcuno [stato persistente locale](service-fabric-concepts-state.md).

La creazione di un servizio senza stato richiede la definizione di un valore per il numero di istanze di logica dell'applicazione del servizio senza stato da eseguire nel cluster. L'aumento del numero di istanze è la soluzione consigliata per implementare la scalabilità orizzontale di un servizio senza stato.

Quando viene rilevato un errore in un'istanza di un servizio senza stato, viene creata una nuova istanza in un nodo idoneo del cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilità dei servizi di Service Fabric con stato
Un servizio con stato dispone di uno stato associato. In Service Fabric un servizio con stato è modellato come set di repliche. Ogni replica è un'istanza del codice del servizio con una copia dello stato. Le operazioni di lettura e scrittura vengono eseguite in una replica (denominata replica primaria). I cambiamenti di stato dovuti a operazioni di scrittura vengono *replicati* in altre repliche (denominate repliche secondarie attive). La combinazione di repliche primarie e secondarie attive costituiscono il set di repliche del servizio.

Può essere presente solo una replica primaria per la gestione delle richieste di lettura e scrittura, ma possono esistere più repliche secondarie attive. Il numero di repliche secondarie attive è configurabile. Più alto è il numero di repliche, maggiore è il numero di errori hardware e software simultanei che è possibile tollerare.

Se la replica primaria diventa inattiva, Service Fabric crea come nuova replica primaria una delle repliche secondarie attive. La replica secondaria attiva ha già la versione aggiornata dello stato (mediante il *processo di replica*) e può continuare a elaborare altre operazioni di lettura e scrittura.

Il concetto di replica primaria o replica secondaria attiva è noto come ruolo di replica.

### <a name="replica-roles"></a>Ruoli di replica
Il ruolo di una replica viene usato per gestire il ciclo di vita dello stato gestito da tale replica. Una replica con ruolo primario gestisce le richieste di lettura. La replica primaria gestisce anche tutte le richieste di scrittura aggiornandone lo stato e replicando le modifiche che vengono applicate alle repliche secondarie attive nel set di repliche. Il compito di una replica secondaria attiva è quello di ricevere i cambiamenti di stato che la primaria ha replicato e di aggiornarne la visualizzazione dello stato.

> [!NOTE]
> Modelli di programmazione di livello superiore, come il [framework Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md) sottraggono il concetto di ruolo di replica alla consapevolezza dello sviluppatore. In Reliable Actors la nozione di ruolo non è necessaria, mentre in Reliable Services è visibile, se necessaria, ma estremamente semplificata.
>
>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

* [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
* [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
* [Definizione e gestione dello stato](service-fabric-concepts-state.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO1-->


