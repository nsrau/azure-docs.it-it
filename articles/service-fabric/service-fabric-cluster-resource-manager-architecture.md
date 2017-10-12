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
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f0d2202c17bf4d378a625a61e941edf7f3f24636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="cluster-resource-manager-architecture-overview"></a>Panoramica dell'architettura di Cluster Resource Manager
Il Cluster Resource Manager di Service Fabric è un servizio centrale in esecuzione nel cluster. Gestisce lo stato desiderato dei servizi del cluster, in particolare in relazione all'uso delle risorse e alle regole di selezione. 

Per gestire le risorse del cluster, Cluster Resource Manager di Service Fabric deve disporre di varie informazioni:

- I servizi esistenti attualmente
- Ogni uso della risorsa della corrente del servizio (o per impostazione predefinito) 
- La capacità residua del cluster 
- La capacità dei nodi del cluster 
- La quantità di risorse usate in ogni nodo

L'uso di risorse di un determinato servizio può cambiare nel tempo; i servizi sono in genere interessati da più di un tipo di risorse. Potrebbero essere misurate risorse reali e fisiche tra diversi servizi. I servizi possono tracciare metriche fisiche come il consumo di memoria e del disco. Più comunemente, i servizi potrebbero occuparsi anche di metriche logiche, ad esempio "WorkQueueDepth" o "TotalRequests". Nello stesso cluster, è possibile usare le metriche logiche e fisiche. Le metriche possono essere condivise tra molti servizi o essere specifiche per un determinato servizio.

## <a name="other-considerations"></a>Altre considerazioni
I proprietari e gli operatori del cluster possono essere diversi dagli autori del servizio e dell'applicazione, anche solo ufficialmente. Quando si sviluppa l'applicazione si conoscono alcuni aspetti riguardanti cosa è necessario. Si dispone di una stima delle risorse che verranno usate e come devono essere distribuiti servizi diversi. Ad esempio, il livello del web deve essere eseguito nei nodi esposti a Internet, mentre per i servizi di database non è necessario. Come esempio aggiuntivo, i servizi web sono probabilmente limitati dalla CPU e dalla rete, mentre la cura di servizi del livello dei dati si occupa maggiormente del consumo della memoria e del disco. Tuttavia, la persona che gestisce un evento imprevisto in un sito per quel servizio in produzione, o che gestisce un aggiornamento del servizio, ha un lavoro diverso da fare e richiede strumenti diversi. 

I cluster e i servizi sono dinamici:

- Il numero di nodi nel cluster può aumentare e diminuire
- Nodi di diversi tipi e dimensioni possono essere aggiunti o rimossi
- I servizi possono essere creati e rimossi e le allocazioni delle risorse desiderate e le regole di selezione possono essere modificate
- Gli aggiornamenti o altre operazioni di gestione possono passare tramite il cluster nell'applicazione sui livelli dell'infrastruttura
- Gli errori possono verificarsi in qualsiasi momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componenti di Cluster Resource Manager e flusso dei dati
Cluster Resource Manager deve monitorare i requisiti di ogni servizio e il consumo di risorse di ogni oggetto di servizio che li compone. Cluster Resource Manager dispone di due componenti concettuali: agenti in esecuzione su ciascun nodo e un servizio a tolleranza d'errore. Gli agenti in ogni nodo monitorano i report di carico dai servizi, li aggregano e li presentano in modo periodico. Il servizio Cluster Resource Manager aggrega tutte le informazioni dagli agenti locali e agisce in base alla propria configurazione corrente.

Si veda il diagramma seguente:

<center>
![Architettura di Resource Balancer][Image1]
</center>

Durante il runtime possono essere apportate numerose modifiche. Ad esempio, si supponga che la quantità di risorse consumate da determinati servizi venga modificata, che alcuni servizi abbiano esito negativo e che alcuni nodi si aggiungano o lascino il cluster. Tutte le modifiche in un nodo vengono aggregate e inviate periodicamente al servizio Cluster Resource Manager (1,2) in cui vengono nuovamente aggregate, analizzate e archiviate. Il servizio esamina tutte le modifiche a intervalli di pochi secondi e determina eventuali azioni necessarie (3). Ad esempio potrebbe notare che sono stati aggiunti dei nodi vuoti al cluster. Di conseguenza, decide di spostare alcuni servizi in tali nodi. Cluster Resource Manager potrebbe anche notare che un determinato nodo è sovraccarico o che alcuni servizi presentano anomalie (o sono stati eliminati), liberando risorse altrove.

Verrà ora descritto il diagramma seguente. Si supponga che Cluster Resource Manager stabilisca che sono necessarie modifiche. Interagisce con altri servizi di sistema (in particolare Gestione failover) per apportare le modifiche necessarie. I comandi necessari vengono quindi inviati ai nodi appropriati (4). Ad esempio, si supponga che Resource Manager abbia notato che il nodo 5 è sovraccarico e che abbia pertanto deciso di spostare il servizio B da Node5 a Node4. Alla fine della riconfigurazione (5), il cluster avrà l'aspetto seguente:

<center>
![Architettura di Resource Balancer][Image2]
</center>

## <a name="next-steps"></a>Passaggi successivi
- Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- I compiti primari di Cluster Resource Manager sono il ribilanciamento del cluster e l'applicazione delle regole di selezione. Per altre informazioni sulla configurazione di questi comportamenti, vedere [bilanciamento del cluster di Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
