<properties
   pageTitle="Partizionamento dei servizi di Service Fabric"
	description="Illustra come partizionare i servizi di Service Fabric"
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
	ms.date="08/26/2015"
	ms.author="aprameyr"/>

# Partizionamento dei servizi di Service Fabric
Service Fabric consente di creare facilmente servizi con stato scalabili grazie al supporto del partizionamento dello stato dei servizi e alla possibilità di far funzionare ogni partizione in un subset dello stato totale. Ogni partizione diventa un'unità a [disponibilità elevata](service-fabric-availability-services.md). Le repliche delle partizioni sono distribuite nei nodi del cluster e bilanciate.

> [AZURE.NOTE]Benché sia possibile partizionare anche i servizi senza stato, questo scenario è poco frequente e non necessario per i servizi di Service Fabric.

Sono disponibili tre diversi schemi di partizionamento.

## Schema di partizionamento singleton
Viene usato per specificare che il servizio non necessita di partizionamento.

## Schema di partizionamento denominato
Viene usato per specificare un set fisso di nomi per ogni partizione del servizio. Le singole partizioni possono essere ricercate in base al nome.

## Schema di partizionamento con intervallo
Viene usato per specificare un intervallo di numeri interi (identificato da una chiave minima e una chiave massima) e un numero di partizioni (n). Crea n partizioni, ognuna responsabile di un intervallo secondario non sovrapposto. Esempio: uno schema di partizionamento con intervallo (per un servizio con tre repliche) con una chiave minima 0, una chiave massima 99 e un conteggio pari a 4 creerà 4 partizioni come mostrato di seguito.

![Partizionamento con intervallo](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Il caso comune consiste nel creare un hash per una chiave univoca all'interno di un set di dati. Sono esempi comuni di chiavi un Vehicle Identification Number (VIN), l'ID di un dipendente o una stringa univoca. Usando tale chiave univoca è possibile quindi creare un codice hash lungo, modulo dell'intervallo di chiavi, da usare come chiave. È possibile specificare limiti massimi e minimi dell'intervallo di chiavi consentite.

È anche consigliabile stimare un numero di partizioni sufficientemente elevato per gestire il caso peggiore di carico delle risorse (ad esempio limitazioni di memoria o larghezza di banda di rete), ma non tale da rendere le partizioni troppo frammentate.

### Selezione di un algoritmo di hash
Una parte importante della generazione di un hash è rappresentata dalla selezione dell'algoritmo di hash. È necessario valutare se l'obiettivo sia raggruppare chiavi simili una accanto all'altra (hash sensibile alla località) o se l'attività debba essere distribuita su larga scala su tutte le partizioni (hash di distribuzione).

Un'ottima risorsa per le scelte di algoritmi di codice di hash generali è rappresentata dalla [pagina Wikipedia sulle funzioni hash](http://en.wikipedia.org/wiki/Hash_function).

> [AZURE.NOTE]Non è possibile modificare il numero di partizioni o il tipo di schema di partizionamento usato per un servizio dopo che è stato creato.

## Passaggi successivi

Per informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)

- [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
 

<!---HONumber=August15_HO9-->