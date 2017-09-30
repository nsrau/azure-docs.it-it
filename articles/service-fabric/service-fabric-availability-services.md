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
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3e46b4bdcf7b55c31afe5e7bc84a1fb95ad98701
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

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

Se la replica primaria diventa inattiva, Service Fabric crea come nuova replica primaria una delle repliche secondarie attive. La replica secondaria attiva ha già la versione aggiornata dello stato (mediante il *processo di replica*) e può continuare a elaborare altre operazioni di lettura e scrittura. Questo processo è noto come riconfigurazione e viene descritto più dettagliatamente in [Riconfigurazione](service-fabric-concepts-reconfiguration.md).

Il concetto di replica primaria o replica secondaria attiva è noto come ruolo di replica. Altre informazioni in [Istanze e repliche](service-fabric-concepts-replica-lifecycle.md). 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
- [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
- [Definizione e gestione dello stato](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

