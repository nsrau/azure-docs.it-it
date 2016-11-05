---
title: Resource Manager Architecture | Microsoft Docs
description: An architectural overview of Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Panoramica dell'architettura di Cluster Resource Manager
Per gestire le risorse del cluster, Cluster Resource Manager di Service Fabric deve disporre di varie informazioni. Deve conoscere i servizi esistenti e la relativa quantità attuale (o predefinita) di risorse che tali servizi stanno usando. Deve conoscere la capacità effettiva dei nodi nel cluster e, di conseguenza, la quantità di risorse disponibili sia nel cluster intero sia in un nodo specifico. L'uso di risorse di un determinato servizio può cambiare nel tempo e che i servizi, in realtà, sono in genere interessati da più di una risorsa. In molti servizi diversi potrebbero essere presenti entrambe le risorse fisiche reali, misurate e segnalate come metriche quali consumo della memoria e del disco, nonché come (e in realtà più comunemente) metriche logiche, ad esempio "WorkQueueDepth" o "TotalRequests". Le metriche logiche e fisiche possono essere usate in molti tipi diversi di servizi oppure essere specifiche di un paio di servizi.

## Altre considerazioni
I proprietari e gli operatori del cluster talvolta non sono anche autori del servizio, o almeno sono le stesse persone ma con ruoli diversi. Quando si sviluppa il servizio, ad esempio, si conoscono solo alcune informazioni su cosa è necessario in termini di risorse e sulla modalità di distribuzione ideale dei diversi componenti, ma nel ruolo della persona che gestisce un evento imprevisto del sito live per tale servizio in produzione è richiesto un lavoro diverso, con strumenti diversi. Né il cluster né i servizi stessi sono configurati staticamente: il numero di nodi nel cluster può aumentare e ridursi, i nodi di dimensioni diverse lasciano e rientrano nel cluster e i servizi possono essere creati, rimossi e modificare la loro allocazione delle risorse in modo istantaneo. Gli aggiornamenti o altre operazioni di gestione possono passare tramite il cluster, con il rischio costante di errore.

## Componenti di Cluster Resource Manager e flusso dei dati
Cluster Resource Manager dovrà conoscere molti aspetti del cluster complessivo, nonché i requisiti dei singoli servizi, le istanze senza stato o le repliche con stato di cui è composto. A tale scopo, gli agenti di Cluster Resource Manager sono eseguiti su nodi individuali per aggregare informazioni sull'uso delle risorse locali. Un servizio Cluster Resource Manager centralizzato a tolleranza di errore aggrega tutte le informazioni sui servizi e il cluster e risponde a seconda della configurazione corrente. La tolleranza di errore per il servizio Cluster Resource Manager e per tutti gli altri servizi del sistema, viene realizzata attraverso gli stessi meccanismi usati per i servizi, vale a dire la replica dello stato del servizio nei quorum di un numero di repliche del cluster (in genere 7).

![Architettura di Resource Balancer][Image1]

Si consideri il diagramma di esempio. In fase di runtime potrebbe verificarsi un'intera serie di modifiche. Si supponga ad esempio che vi siano alcune modifiche della quantità di risorse utilizzate dai servizi, alcuni errori del servizio, che alcuni nodi si uniscano e lascino il cluster e così via. Tutte le modifiche in un nodo specifico vengono aggregate e inviate periodicamente al servizio Cluster Resource Manager (1,2) in cui vengono nuovamente aggregate, analizzate e archiviate. Il servizio esamina tutte le modifiche a intervalli di pochi secondi e determina eventuali azioni necessarie (3). Ad esempio potrebbe notare che sono stati aggiunti dei nodi vuoti al cluster e decidere di spostare alcuni servizi su questi nodi. Potrebbe inoltre notare che un determinato nodo è sovraccarico o che alcuni servizi presentano anomalie (o sono stati eliminati), liberando risorse in altri nodi.

Diamo un'occhiata al diagramma seguente e osserviamo cosa succede. Si supponga che Cluster Resource Manager stabilisca che sono necessarie modifiche. Interagisce con altri servizi di sistema (in particolare Gestione failover) per apportare le modifiche necessarie. Le richieste di modifica vengono quindi inviate ai nodi appropriati (4). In questo caso, si presume che Resource Manager abbia notato che il nodo 5 è sovraccarico e che abbia pertanto deciso di spostare il servizio B da N5 a N4. Alla fine della riconfigurazione (5), il cluster avrà l'aspetto seguente:

![Architettura di Resource Balancer][Image2]

## Passaggi successivi
* Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0824_2016-->