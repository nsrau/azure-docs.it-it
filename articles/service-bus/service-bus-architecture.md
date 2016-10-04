<properties 
    pageTitle="Architettura del bus di servizio | Microsoft Azure"
    description="Descrive l'architettura di elaborazione e inoltro dei messaggi del bus di servizio di Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />  
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# Architettura del bus di servizio

Questo articolo descrive l'architettura di elaborazione e inoltro dei messaggi del bus di servizio di Azure.

## Unità di scala del bus di servizio

Il bus di servizio è organizzato in base a *unità di scala*. Un'unità di scala è un'unità di distribuzione e contiene tutti i componenti necessari per eseguire il servizio. Ogni area distribuisce una o più unità di scala del bus di servizio.

Uno spazio dei nomi del bus di servizio è mappato a un'unità di scala. L'unità di scala gestisce tutti i tipi di entità del bus di servizio, ovvero inoltri ed entità di messaggistica negoziata come code, argomenti e sottoscrizioni. Un’unità di scala del bus di servizio include i seguenti componenti:

- **Un set di nodi del gateway.** I nodi del gateway autenticano le richieste in ingresso e gestiscono le richieste di inoltro. Ogni nodo del gateway ha un indirizzo IP pubblico.

- **Un set di nodi del broker di messaggistica.** I nodi del broker di messaggistica elaborano le richieste relative alle entità di messaggistica.

- **Un archivio del gateway.** Questo archivio contiene i dati per ogni entità definita nell'unità di scala. L'archivio del gateway viene implementato in un database SQL Azure.

- **Più archivi di messaggistica.** Gli archivi di messaggistica contengono i messaggi di tutte le code, gli argomenti e le sottoscrizioni definiti nell'unità di scala. Contiene anche tutti i dati di sottoscrizione. A meno che non siano abilitate le [entità di messaggistica partizionate](../service-bus-messaging/service-bus-partitioning.md), una coda o un argomento viene mappato a un archivio di messaggistica. Le sottoscrizioni vengono archiviate nello stesso archivio di messaggistica del relativo argomento padre. A eccezione del [livello di messaggistica Premium](../service-bus-messaging/service-bus-premium-messaging.md) del bus di servizio, gli archivi di messaggistica vengono implementati nei database SQL Azure.

## Contenitori

A ciascuna entità di messaggistica viene assegnato un contenitore specifico. Un contenitore è un costrutto logico che utilizza esattamente un archivio di messaggistica per archiviare tutti i dati pertinenti per questo contenitore. Ciascun contenitore viene assegnato a un nodo del broker di messaggistica. In genere, esistono più contenitori che nodi del broker di messaggistica. Pertanto, ogni nodo del broker di messaggistica carica più contenitori. La distribuzione dei contenitori in un nodo del broker di messaggistica è organizzata in modo che tutti i nodi del broker di messaggistica vengano caricati in modo uniforme. Se il modello di caricamento viene modificato (ad esempio, uno dei contenitori diventa troppo occupato) o se un nodo del broker di messaggistica diventa temporaneamente non disponibile, i contenitori vengono ridistribuiti tra i nodi dei broker di messaggistica.

## Elaborazione delle richieste di messaggistica in ingresso

Quando un client invia una richiesta al bus di servizio, il servizio di bilanciamento del carico di Azure instrada la richiesta ai nodi del gateway. Il nodo del gateway autorizza la richiesta. Se la richiesta riguarda un'entità di messaggistica (coda, argomento, sottoscrizione), il nodo del gateway ricerca l'entità nell'archivio del gateway e determina in quale archivio di messaggistica si trova l'entità. Cerca quindi il nodo del broker di messaggistica che sta attualmente utilizzando questo contenitore e invia la richiesta a quel nodo del broker di messaggistica. Il nodo del broker di messaggistica elabora la richiesta e aggiorna lo stato dell'entità nell'archivio del contenitore. Il nodo del broker di messaggistica invia quindi la risposta al nodo del gateway, che invia una risposta appropriata al client che ha emesso la richiesta originale.

![Elaborazione delle richieste di messaggistica in ingresso](./media/service-bus-architecture/IC690644.png)

## Elaborazione delle richieste di inoltro in ingresso

Quando un client invia una richiesta al bus di servizio, il servizio di bilanciamento del carico di Azure instrada la richiesta ai nodi del gateway. Se la richiesta è una richiesta di ascolto, il nodo del gateway crea un nuovo inoltro. Se la richiesta è una richiesta di connessione a un inoltro specifico, il nodo del gateway invia la richiesta di connessione al nodo del gateway che possiede l'inoltro. Il nodo del gateway che possiede l'inoltro invia una richiesta di rendezvous al client di ascolto, chiedendo al listener di creare un canale temporaneo sul nodo del gateway che ha ricevuto la richiesta di connessione.

Quando viene stabilita la connessione di inoltro, i client possono scambiare messaggi tramite il nodo del gateway utilizzato per il rendezvous.

![Elaborazione delle richieste di inoltro in ingresso](./media/service-bus-architecture/IC690645.png)

## Passaggi successivi

Ora che è stata esaminata una panoramica dell'architettura del bus di servizio, visitare i collegamenti seguenti per iniziare:

- [Panoramica della messaggistica del bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)
- [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
- [Una soluzione di messaggistica accodata che usa le code del bus di servizio](../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

<!---HONumber=AcomDC_0928_2016-->