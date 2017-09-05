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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: b0d4615a9b8ab566f69b27e4879b6e2d597b4990
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---

# <a name="availability-of-service-fabric-services"></a>Disponibilità dei servizi di Service Fabric
Questo articolo contiene una panoramica sul modo in cui Service Fabric gestisce la disponibilità di un servizio.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilità dei servizi di Service Fabric senza stato
I servizi Service Fabric di Azure possono essere con o senza stato. Un servizio senza stato è un servizio dell'applicazione che non dispone di uno [stato locale](service-fabric-concepts-state.md) che necessita di essere altamente disponibile o affidabile.

La creazione di un servizio senza stato richiede la definizione di un `InstanceCount`. per il numero di istanze di logica dell'applicazione del servizio senza stato da eseguire nel cluster. L'aumento del numero di istanze è la soluzione consigliata per implementare la scalabilità orizzontale di un servizio senza stato.

Quando un'istanza di un servizio denominato senza stato ha esito negativo, viene creata una nuova istanza in un nodo idoneo del cluster. Ad esempio, un'istanza del servizio senza stato potrebbe non riuscire in Node1 ed essere ricreata in Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilità dei servizi di Service Fabric con stato
Un servizio con stato dispone di uno stato associato. In Service Fabric un servizio con stato è modellato come set di repliche. Ogni replica è un'istanza in esecuzione del codice del servizio che ha anche una copia dello stato per tale servizio. Le operazioni di lettura e scrittura vengono eseguite in una replica (denominata replica primaria). I cambiamenti di stato dovuti a operazioni di scrittura vengono *replicati* in altre repliche nel set di repliche, denominate repliche secondarie attive, e applicati. 

Può esistere solo una replica primaria, ma possono esistere più repliche secondarie attive. Il numero di repliche secondarie attive è configurabile. Più alto è il numero di repliche, maggiore è il numero di errori hardware e software simultanei che è possibile tollerare.

Se la replica primaria diventa inattiva, Service Fabric crea come nuova replica primaria una delle repliche secondarie attive. La replica secondaria attiva ha già la versione aggiornata dello stato (mediante il *processo di replica*) e può continuare a elaborare altre operazioni di lettura e scrittura.

Il concetto di replica primaria o replica secondaria attiva è noto come ruolo di replica.

### <a name="replica-roles"></a>Ruoli di replica
Il ruolo di una replica viene usato per gestire il ciclo di vita dello stato gestito da tale replica. Una replica con ruolo primario gestisce le richieste di lettura. La replica primaria gestisce anche tutte le richieste di scrittura aggiornandone lo stato e replicando le modifiche che vengono applicate alle repliche secondarie attive nel set di repliche. Il compito di una replica secondaria attiva è quello di ricevere i cambiamenti di stato che la primaria ha replicato e di aggiornarne la visualizzazione dello stato.

> [!NOTE]
> Modelli di programmazione di livello superiore, come [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md) nascondono il concetto di ruolo di replica allo sviluppatore. In Reliable Actors la nozione di ruolo non è necessaria, mentre in Reliable Services è estremamente semplificata per la maggior parte degli scenari.
>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
- [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
- [Definizione e gestione dello stato](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

