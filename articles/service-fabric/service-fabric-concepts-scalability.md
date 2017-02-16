---
title: "Scalabilità dei servizi di Service Fabric | Documentazione Microsoft"
description: Illustra come scalare i servizi di Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/30/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 52dcf341a34478bf4e800d8f1b9d44867b5feaf6


---
# <a name="scaling-service-fabric-applications"></a>Scalabilità delle applicazioni di Service Fabric
Service Fabric di Azure semplifica la creazione di applicazioni scalabili gestendo servizi, partizioni e repliche in tutti i nodi di un cluster. Questo consente un utilizzo ottimale delle risorse.

Per ottenere un'elevata scalabilità per le applicazioni di Service Fabric, sono possibili due soluzioni:

1. Implementazione della scalabilità a livello di partizione del servizio
2. Implementazione della scalabilità a livello di istanza del servizio denominato

## <a name="scaling-at-the-partition-level"></a>Implementazione della scalabilità a livello di partizione
Service Fabric supporta il partizionamento. Il partizionamento consente di suddividere un singolo servizio in più partizioni indipendenti, ciascuna contenente una parte dello stato generale del servizio. L'articolo relativo alla [panoramica del partizionamento](service-fabric-concepts-partitioning.md) include informazioni relative ai tipi di schemi di partizionamento supportati. Le repliche di ogni partizione vengono distribuite tra i nodi in un cluster. Si consideri un servizio che usa lo schema di partizionamento con intervallo con una chiave inferiore uguale a 0, una chiave superiore uguale a 99 e quattro partizioni. In un cluster a tre nodi il servizio potrebbe avere un layout con quattro repliche che condividono le risorse in ogni nodo, come illustrato di seguito:

<center>
![Layout delle partizioni con tre nodi](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se si aumenta il numero dei nodi, Service Fabric userà le risorse nei nuovi nodi spostando lì alcune repliche esistenti. Se il numero dei nodi passa a quattro, vengono eseguite tre repliche in ogni nodo (ciascuno appartenente a partizioni diverse), con un conseguente miglioramento delle prestazioni e dell'utilizzo delle risorse.

<center>
![Layout delle partizioni con quattro nodi](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-at-the-service-name-level"></a>Implementazione della scalabilità a livello di nome del servizio
Un'istanza del servizio è un'istanza specifica di un nome di applicazione e di un nome di tipo di servizio (vedere l'articolo relativo al [ciclo di vita delle applicazioni di Service Fabric](service-fabric-application-lifecycle.md)). Lo schema di partizione da usare viene specificato durante la creazione del servizio (vedere l'articolo relativo al [partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)).

Il primo livello di scalabilità si basa sul nome dei servizi. È possibile creare istanze di un servizio, eventualmente con un livello di partizionamento diverso, man mano che le istanze meno recenti risultano occupate. In questo modo, i nuovi consumer del servizio potranno avvalersi delle istanze meno occupate, invece che di quelle più impegnate.

Per aumentare la capacità è possibile creare una nuova istanza del servizio con un nuovo schema di partizione. Ciò tuttavia incrementa la complessità, perché tutti i client consumer devono sapere quando e come usare il servizio con un nome diverso. In alternativa, un servizio di gestione o intermediario dovrebbe determinare quale servizio e quale partizioni dovrebbero gestire ciascuna richiesta.

### <a name="example-scenario-embedded-dates"></a>Scenario di esempio: date incorporate
Uno scenario possibile sarebbe quello di usare le informazioni relative alla data come parte del nome del servizio. Ad esempio, si potrebbe usare un'istanza del servizio con un nome specifico per tutti i clienti che si sono aggiunti nel 2013 e un altro nome per i clienti che si sono aggiunti nel 2014. Questo schema di denominazione consente l'incremento dei nomi in base alla data a livello di codice, quindi con l'avvicinarsi del 2014 l'istanza del servizio per tale anno può essere creata su richiesta.

Tale approccio tuttavia si basa su client che usano informazioni di denominazione specifiche dell'applicazione, un aspetto che non rientra nei dati noti a Service Fabric.

* *Uso di una convenzione di denominazione*: nel 2013, quando l'applicazione viene pubblicata, si crea un servizio denominato fabric:/app/service2013. Verso il secondo trimestre del 2013 si crea un altro servizio denominato fabric:/app/service2014. Entrambi questi servizi sono dello stesso tipo. In questo approccio il client dovrà usare la logica per creare il nome appropriato per il servizio in base all'anno.
* *Uso di un servizio di ricerca*: un altro approccio consiste nel rendere disponibile un servizio di ricerca secondario in grado di fornire il nome del servizio per la chiave desiderata. Il servizio di ricerca può quindi creare nuove istanze del servizio, tuttavia non mantiene dati dell'applicazione, ma solo i dati relativi ai nomi che crea per il servizio. Nell’esempio precedente basato sull'anno il client perciò prima contatterebbe il servizio di ricerca per individuare il nome del servizio che gestisce i dati per un determinato anno e quindi userebbe tale nome per eseguire l'operazione effettiva. Il risultato della prima ricerca può essere memorizzato nella cache.

## <a name="putting-it-all-together"></a>Riassumendo
Le idee discusse in precedenza verranno ora messe in pratica per un altro scenario.

Si consideri l'esempio seguente: si sta tentando di compilare un servizio che funga da rubrica, contenente nomi e informazioni di contatto. Da quanti utenti sarà idealmente composta? Quanti contatti archivierà ogni singolo utente? La prima volta che si crea un servizio è molto difficile prendere simili decisioni. Scegliere la partizione sbagliata potrebbe portare a problemi di scalabilità futuri. Tuttavia, perché mai scegliere uno schema a partizione singola per tutti gli utenti?

In questi tipi di situazioni, è invece consigliabile prendere in considerazione il modello seguente:
1. Invece di tentare di selezionare in anticipo uno schema di partizionamento per tutti gli utenti, creare un "servizio gestione".
2. Il compito del servizio gestione è analizzare le informazioni del cliente quando quest'ultimo si iscrive al servizio. Quindi si basa su tali informazioni per creare un'istanza del servizio contact-storage _effettivo_ _solo per quel cliente_. Questo modello di creazione dinamica del servizio offre numerosi vantaggi:

    * Non è necessario indovinare in anticipo il numero di partizione corretto per tutti gli utenti
    * Segmentazione dei dati, poiché ogni cliente dispone della propria copia del servizio
    * Ogni servizio del cliente può essere configurato in maniera diversa, con più o meno partizioni o repliche in base alla necessità e alla scalabilità prevista.
      * Ad esempio, il cliente paga per il livello "Gold" e può ottenere così più repliche o partizioni
      * Come ulteriore esempio, un cliente può indicare come "Small" il numero di contatti necessari per ottenere solo alcune partizioni.
    * Non si esegue una serie di istanze del servizio o repliche mentre si attende l'arrivo dei clienti
    * Se un cliente se ne va, rimuovere le sue informazioni dal servizio è un'operazione assolutamente semplice: il servizio gestione eliminerà in tutta tranquillità il servizio creato

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

* [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)
* [Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)
* [Definizione e gestione dello stato](service-fabric-concepts-state.md)



<!--HONumber=Jan17_HO1-->


