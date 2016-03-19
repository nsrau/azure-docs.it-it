<properties
   pageTitle="Disponibilità dei servizi di Service Fabric | Microsoft Azure"
   description="Descrive il rilevamento degli errori, il failover e il ripristino dei servizi"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="aprameyr"/>

# Disponibilità dei servizi di Service Fabric
I servizi Service Fabric di Azure possono essere con o senza stato. Questo articolo fornisce una panoramica della gestione della disponibilità di un servizio in Service Fabric in caso di errori.

## Disponibilità dei servizi di Service Fabric senza stato
Un servizio senza stato è un servizio dell'applicazione che non dispone di alcuno [stato persistente locale](service-fabric-concepts-state.md).

La creazione di un servizio senza stato richiede la definizione di un numero di istanze del servizio senza stato che devono essere eseguite nel cluster. Queste corrispondono al numero di copie della logica dell'applicazione di cui verrà creata un'istanza nel cluster. L'aumento del numero di istanze è la soluzione consigliata per icrementare la scalabilità dei servizi senza stato.

Quando viene rilevato un errore in un'istanza del servizio senza stato, viene creata una nuova istanza in un altro nodo idoneo del cluster.

## Disponibilità dei servizi di Service Fabric con stato
Un servizio con stato dispone di uno stato associato. In Service Fabric un servizio con stato è modellato come set di repliche. Ogni replica è un'istanza del codice del servizio con una copia dello stato. Le operazioni di lettura e scrittura vengono eseguite in una replica (denominata replica primaria). I cambiamenti di stato dovuti a operazioni di scrittura vengono *replicati* in altre repliche (denominate repliche secondarie attive). La combinazione di repliche primarie e secondarie attive è il set di repliche del servizio.

Può essere presente solo una replica primaria per la manutenzione delle richieste di lettura e scrittura, ma possono esistere più repliche secondarie attive. Il numero di repliche secondarie attive è configurabile. Più è alto il numero di repliche, maggiore sarà il numero di errori hardware e software simultanei che sarà possibile tollerare.

In caso di errore (inattività della replica primaria), Service Fabric imposta come primaria una delle repliche secondarie attive. La replica secondaria attiva ha già la versione aggiornata dello stato (mediante il *processo di replica*) e può continuare a elaborare altre operazioni di lettura e scrittura.

Il concetto di replica primaria o replica secondaria attiva è noto come ruolo di replica.

### Ruoli di replica
Il ruolo di una replica viene usato per gestire il ciclo di vita dello stato gestito da tale replica. Una replica con ruolo primario gestisce le richieste di lettura. Essa gestisce anche le richieste di scrittura aggiornando il proprio stato e replicando le modifiche nelle repliche secondarie attive del relativo set di repliche. Il ruolo di una replica secondaria attiva è quello di ricevere i cambiamenti di stato che la replica primaria ha replicato e di aggiornare la visualizzazione dello stato.

>[AZURE.NOTE] Modelli di programmazione di livello superiore come il [framework Reliable Actors](service-fabric-reliable-actors-introduction.md) sottraggono il concetto di ruolo di replica alla consapevolezza dello sviluppatore.

## Passaggi successivi

Per ulteriori informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)

- [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)

- [Definizione e gestione dello stato](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0128_2016-->