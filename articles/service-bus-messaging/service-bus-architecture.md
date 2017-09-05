---
title: Panoramica dell'architettura di elaborazione dei messaggi del bus di servizio di Azure | Documentazione Microsoft
description: Descrive l'architettura di elaborazione dei messaggi del bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 83456d775c5ff2a2476ba46e9c78a8dc1bb482e8
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="service-bus-architecture"></a>Architettura del bus di servizio
Questo articolo descrive l'architettura di elaborazione dei messaggi del bus di servizio di Azure.

## <a name="service-bus-scale-units"></a>Unità di scala del bus di servizio
Il bus di servizio è organizzato in base a *unità di scala*. Un'unità di scala è un'unità di distribuzione e contiene tutti i componenti necessari per eseguire il servizio. Ogni area distribuisce una o più unità di scala del bus di servizio.

Uno spazio dei nomi del bus di servizio è mappato a un'unità di scala. L'unità di scala gestisce tutti i tipi di entità del bus di servizio (code, argomenti e sottoscrizioni). Un’unità di scala del bus di servizio include i seguenti componenti:

* **Un set di nodi del gateway.** I nodi del gateway autenticano le richieste in ingresso. Ogni nodo del gateway ha un indirizzo IP pubblico.
* **Un set di nodi del broker di messaggistica.** I nodi del broker di messaggistica elaborano le richieste relative alle entità di messaggistica.
* **Un archivio del gateway.** Questo archivio contiene i dati per ogni entità definita nell'unità di scala. L'archivio del gateway viene implementato in un database SQL Azure.
* **Più archivi di messaggistica.** Gli archivi di messaggistica contengono i messaggi di tutte le code, gli argomenti e le sottoscrizioni definiti nell'unità di scala. Contiene anche tutti i dati di sottoscrizione. A meno che non sia abilitato il [partizionamento delle entità di messaggistica](service-bus-partitioning.md), per una coda o un argomento viene eseguito il mapping a un archivio di messaggistica. Le sottoscrizioni vengono archiviate nello stesso archivio di messaggistica del relativo argomento padre. A eccezione del [livello di messaggistica Premium](service-bus-premium-messaging.md)del bus di servizio, gli archivi di messaggistica vengono implementati nei database SQL Azure.

## <a name="containers"></a>Contenitori
A ciascuna entità di messaggistica viene assegnato un contenitore specifico. Un contenitore è un costrutto logico che utilizza esattamente un archivio di messaggistica per archiviare tutti i dati pertinenti per questo contenitore. Ciascun contenitore viene assegnato a un nodo del broker di messaggistica. In genere, esistono più contenitori che nodi del broker di messaggistica. Pertanto, ogni nodo del broker di messaggistica carica più contenitori. La distribuzione dei contenitori in un nodo del broker di messaggistica è organizzata in modo che tutti i nodi del broker di messaggistica vengano caricati in modo uniforme. Se il modello di caricamento viene modificato (ad esempio, uno dei contenitori diventa troppo occupato) o se un nodo del broker di messaggistica diventa temporaneamente non disponibile, i contenitori vengono ridistribuiti tra i nodi dei broker di messaggistica.

## <a name="processing-of-incoming-messaging-requests"></a>Elaborazione delle richieste di messaggistica in ingresso
Quando un client invia una richiesta al bus di servizio, il servizio di bilanciamento del carico di Azure instrada la richiesta ai nodi del gateway. Il nodo del gateway autorizza la richiesta. Se la richiesta riguarda un'entità di messaggistica (coda, argomento, sottoscrizione), il nodo del gateway ricerca l'entità nell'archivio del gateway e determina in quale archivio di messaggistica si trova l'entità. Cerca quindi il nodo del broker di messaggistica che sta attualmente utilizzando questo contenitore e invia la richiesta a quel nodo del broker di messaggistica. Il nodo del broker di messaggistica elabora la richiesta e aggiorna lo stato dell'entità nell'archivio del contenitore. Il nodo del broker di messaggistica invia quindi la risposta al nodo del gateway, che invia una risposta appropriata al client che ha emesso la richiesta originale.

![Elaborazione delle richieste di messaggistica in ingresso](./media/service-bus-architecture/ic690644.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata letta una panoramica dell'architettura del bus di servizio, vedere i collegamenti seguenti per altre informazioni:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Una soluzione di messaggistica accodata che usa le code del bus di servizio](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



