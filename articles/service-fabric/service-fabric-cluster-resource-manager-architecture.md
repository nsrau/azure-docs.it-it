---
title: Architettura di Resource Manager | Microsoft Docs
description: An architectural overview of Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 67f07bad8f6f89d9e5e68326f0cc194d920e841b


---
# <a name="cluster-resource-manager-architecture-overview"></a>Panoramica dell'architettura di Cluster Resource Manager
Per gestire le risorse del cluster, Cluster Resource Manager di Service Fabric deve disporre di varie informazioni. Deve conoscere i servizi esistenti e la relativa quantità attuale (o predefinita) di risorse che tali servizi stanno usando. Per rilevare le risorse disponibili nel cluster, deve conoscere la capacità dei nodi nel cluster e la quantità di risorse usata in ciascuno di essi. L'uso di risorse di un determinato servizio può cambiare nel tempo; i servizi sono in genere interessati da più di un tipo di risorse. Potrebbero essere misurate risorse reali e fisiche tra diversi servizi. I servizi possono tracciare metriche fisiche come il consumo di memoria e del disco. Più comunemente, i servizi potrebbero occuparsi anche di metriche logiche, ad esempio "WorkQueueDepth" o "TotalRequests". Le metriche logiche e fisiche possono essere usate in molti tipi diversi di servizi oppure essere specifiche di un paio di servizi.

## <a name="other-considerations"></a>Altre considerazioni
I proprietari e gli operatori del cluster possono essere diversi dagli autori del servizio, anche solo ufficialmente. Ad esempio, quando si sviluppa il servizio, si conoscono vari aspetti dei suoi requisiti di risorse e si ha in mente una distribuzione ideale dei vari componenti. Tuttavia, la persona che gestisce un evento imprevisto in un sito per quel servizio in produzione ha un lavoro diverso da fare e richiede strumenti diversi. Inoltre, né il cluster né i servizi sono configurati in maniera statica:

* Il numero di nodi nel cluster può aumentare e diminuire
* Nodi di diversi tipi e dimensioni possono essere aggiunti o rimossi
* I servizi possono essere creati e rimossi e le allocazioni delle risorse desiderate possono essere modificate
* Gli aggiornamenti o altre operazioni di gestione possono passare tramite il cluster, con il rischio costante di errore.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componenti di Cluster Resource Manager e flusso dei dati
Cluster Resource Manager deve monitorare i requisiti dei singoli servizi e il consumo di risorse degli oggetti di servizio singoli che li compongono. A tale scopo, Cluster Resource Manager dispone di due componenti concettuali: agenti in esecuzione su ciascun nodo e un servizio a tolleranza d'errore. Gli agenti in ogni nodo monitorano i report di carico dai servizi, li aggregano e li presentano in modo periodico. Il servizio Cluster Resource Manager aggrega tutte le informazioni dagli agenti locali e agisce in base alla propria configurazione corrente.

Si veda il diagramma seguente:

<center>
![Architettura di Resource Balancer][Image1]
</center>

Durante il runtime possono essere apportate numerose modifiche. Ad esempio, si supponga che la quantità di risorse consumate da determinati servizi venga modificata, che alcuni servizi abbiano esito negativo e che alcuni nodi si aggiungano o lascino il cluster. Tutte le modifiche in un nodo vengono aggregate e inviate periodicamente al servizio Cluster Resource Manager (1,2) in cui vengono nuovamente aggregate, analizzate e archiviate. Il servizio esamina tutte le modifiche a intervalli di pochi secondi e determina eventuali azioni necessarie (3). Ad esempio potrebbe notare che sono stati aggiunti dei nodi vuoti al cluster e decidere di spostare alcuni servizi su questi nodi. Cluster Resource Manager potrebbe inoltre notare che un determinato nodo è sovraccarico o che alcuni servizi presentano anomalie (o sono stati eliminati), liberando risorse altrove.

Verrà ora descritto il diagramma seguente. Si supponga che Cluster Resource Manager stabilisca che sono necessarie modifiche. Interagisce con altri servizi di sistema (in particolare Gestione failover) per apportare le modifiche necessarie. I comandi necessari vengono quindi inviati ai nodi appropriati (4). In questo caso, si presume che Resource Manager abbia notato che il nodo 5 è sovraccarico e che abbia pertanto deciso di spostare il servizio B da N5 a N4. Alla fine della riconfigurazione (5), il cluster avrà l'aspetto seguente:

<center>
![Architettura di Resource Balancer][Image2]
</center>

## <a name="next-steps"></a>Passaggi successivi
* Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png



<!--HONumber=Jan17_HO1-->


