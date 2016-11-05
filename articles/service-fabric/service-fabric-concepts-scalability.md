---
title: Scalabilità dei servizi di Service Fabric | Microsoft Docs
description: Illustra come scalare i servizi di Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr

---
# Scalabilità delle applicazioni di Service Fabric
Service Fabric di Azure semplifica la creazione di applicazioni scalabili bilanciando il carico tra servizi, partizioni e repliche in tutti i nodi di un cluster. Questo consente un utilizzo ottimale delle risorse.

Per ottenere un'elevata scalabilità per le applicazioni di Service Fabric, sono possibili due soluzioni:

1. Implementazione della scalabilità a livello di partizione
2. Implementazione della scalabilità a livello di nome del servizio

## Implementazione della scalabilità a livello di partizione
Service Fabric supporta il partizionamento di un singolo servizio in più partizioni più piccole. L'articolo relativo alla [panoramica del partizionamento](service-fabric-concepts-partitioning.md) include informazioni relative ai tipi di schemi di partizionamento supportati. Le repliche di ogni partizione vengono distribuite tra i nodi in un cluster. Si consideri un servizio che usa lo schema di partizionamento con intervallo con una chiave inferiore uguale a 0, una chiave superiore uguale a 99 e quattro partizioni. In un cluster a tre nodi il servizio potrebbe avere un layout con quattro repliche che condividono le risorse in ogni nodo, come illustrato di seguito:

![Layout delle partizioni con tre nodi](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Se si aumenta il numero dei nodi, Service Fabric potrà usare le risorse nei nuovi nodi spostando alcune repliche in nodi vuoti. Se il numero dei nodi passa a quattro, vengono eseguite tre repliche in ogni nodo (di partizioni diverse), con un conseguente miglioramento delle prestazioni e dell'utilizzo delle risorse.

![Layout delle partizioni con quattro nodi](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## Implementazione della scalabilità a livello di nome del servizio
Un'istanza del servizio è un'istanza specifica di un nome di applicazione e di un nome di tipo di servizio (vedere l'articolo relativo al [ciclo di vita delle applicazioni di Service Fabric](service-fabric-application-lifecycle.md)). Lo schema di partizione da usare viene specificato durante la creazione del servizio (vedere l'articolo relativo al [partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)).

Il primo livello di scalabilità si basa sul nome dei servizi. È possibile creare nuove istanze di un servizio, con un livello di partizionamento diverso, man mano che le istanze meno recenti risultano occupate. In questo modo, i nuovi consumer del servizio potranno avvalersi delle istanze meno occupate, invece che di quelle più impegnate.

Per aumentare la capacità, nonché per aumentare o ridurre il numero delle partizioni, è possibile creare una nuova istanza del servizio con un nuovo schema di partizione. Ciò tuttavia incrementa la complessità, perché tutti i client consumer devono sapere quando e come usare il servizio con un nome diverso.

### Scenario di esempio: date incorporate
Uno scenario possibile sarebbe quello di usare le informazioni relative alla data come parte del nome del servizio. Ad esempio, si potrebbe usare un'istanza del servizio con un nome specifico per tutti i clienti che si sono aggiunti nel 2013 e un altro nome per i clienti che si sono aggiunti nel 2014. Questo schema di denominazione consente l'incremento dei nomi in base alla data a livello di codice, quindi con l'avvicinarsi del 2014 l'istanza del servizio per tale anno può essere creata su richiesta.

Tale approccio tuttavia si basa su client che usano informazioni di denominazione specifiche dell'applicazione, un aspetto che non rientra nei dati noti a Service Fabric.

* *Uso di una convenzione di denominazione*: nel 2013, quando l'applicazione viene pubblicata, si crea un servizio denominato fabric:/app/service2013. Verso il secondo trimestre del 2013 si crea un altro servizio denominato fabric:/app/service2014. Entrambi questi servizi sono dello stesso tipo. In questo approccio il client dovrà usare la logica per creare il nome appropriato per il servizio in base all'anno.
* *Uso di un servizio di ricerca*: un altro approccio consiste nel rendere disponibile un servizio di ricerca secondario in grado di fornire il nome del servizio per la chiave desiderata. Il servizio di ricerca può quindi creare nuove istanze del servizio, tuttavia non mantiene dati dell'applicazione, ma solo i dati relativi ai nomi che crea per il servizio. Nell’esempio precedente basato sull'anno il client perciò prima contatterebbe il servizio di ricerca per individuare il nome del servizio che gestisce i dati per un determinato anno e quindi userebbe tale nome per eseguire l'operazione effettiva. Il risultato della prima ricerca può essere memorizzato nella cache.

## Passaggi successivi
Per ulteriori informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

* [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)
* [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
* [Definizione e gestione dello stato](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0810_2016-->