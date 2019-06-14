---
title: Ridimensionamento di cluster autonomi di Azure Service Fabric | Microsoft Docs
description: Informazioni sul ridimensionamento orizzontale o verticale dei cluster autonomi di Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 05049b9b08b4630c4299a6d3054c7815b082af52
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60516043"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Ridimensionamento dei cluster di Service Fabric
Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I cluster possono contenere migliaia di nodi. Dopo aver creato un cluster di Service Fabric, è possibile scalare il cluster in orizzontale (modificare il numero di nodi) o in verticale (modificare le risorse dei nodi).  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

Perché ridimensionare il cluster? Le richieste delle applicazioni cambiano nel tempo.  Potrebbe essere necessario aumentare le risorse del cluster per supportare l'aumento del carico di lavoro delle applicazioni o del traffico di rete oppure ridurre le risorse del cluster quando le richieste si riducono.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Ridimensionamento orizzontale
Modifica il numero di nodi nel cluster.  Dopo che i nuovi nodi sono stati aggiunti al cluster, [Gestione risorse cluster](service-fabric-cluster-resource-manager-introduction.md) sposta i servizi nei nuovi nodi, riducendo il carico totale sui nodi esistenti.  È anche possibile ridurre il numero di nodi del cluster, se le risorse del cluster non vengono usate in modo efficiente.  Quando i nodi vengono rimossi dal cluster, i servizi vengono spostati da tali nodi e il carico nei nodi rimanenti aumenta.  La riduzione del numero di nodi in un cluster in esecuzione in Azure consente un risparmio, poiché il pagamento è basato sul numero di macchine virtuali usate, non sul carico di lavoro in tali macchine virtuali.  

- Vantaggi: scalabilità infinita, in teoria.  Se l'applicazione è progettata per la scalabilità, è possibile abilitare una crescita illimitata aggiungendo più nodi.  Gli strumenti disponibili negli ambienti cloud consentono di aggiungere o rimuovere facilmente i nodi, in modo da regolare la capacità e pagare solo per le risorse usate.  
- Svantaggi: le applicazioni devono essere [progettate per la scalabilità](service-fabric-concepts-scalability.md).  Anche la persistenza e i database delle applicazioni possono richiedere attività aggiuntive a livello di architettura per la scalabilità.  Le raccolte [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) nei servizi con stato di Service Fabric, comunque, rendono molto più semplice ridimensionare i dati delle applicazioni.

I cluster autonomi consentono di distribuire un cluster Service Fabric locale o nel provider di servizi cloud che si preferisce.  I tipi di nodi sono costituiti da computer fisici o macchine virtuali, a seconda della distribuzione. Rispetto ai cluster in esecuzione in Azure, il processo di ridimensionamento di un cluster autonomo è leggermente più complesso.  È necessario modificare manualmente il numero di nodi nel cluster e quindi eseguire un aggiornamento della configurazione del cluster.

È possibile che con la rimozione di nodi vengano avviati più aggiornamenti in sequenza. Alcuni nodi sono contrassegnati con il tag `IsSeedNode=”true”` e possono essere identificati mediante query nel manifesto del cluster usando [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). La rimozione di tali nodi può richiedere più tempo perché comporta lo spostamento dei nodi di inizializzazione. Il cluster deve mantenere almeno tre nodi di tipo primario.

> [!WARNING]
> È opportuno evitare di ridurre il numero di nodi al di sotto della [dimensione del cluster del livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster). Ciò interferisce con la replica dei servizi di sistema di Service Fabric all'interno del cluster e può destabilizzare o addirittura causare l'eliminazione definitiva del cluster.
>

Quando si ridimensiona un cluster autonomo, tenere presenti le linee guida seguenti:
- La sostituzione dei nodi primari deve essere eseguita un nodo alla volta, anziché eseguire la rimozione e l'aggiunta in batch.
- Prima di rimuovere un tipo di nodo, verificare se sono presenti nodi che fanno riferimento al tipo di nodo stesso. Rimuovere tali nodi prima di rimuovere il tipo di nodo corrispondente. Dopo aver rimosso tutti i nodi corrispondenti, è possibile rimuovere il tipo di nodo (NodeType) dalla configurazione del cluster e avviare un aggiornamento della configurazione mediante [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Per altre informazioni, vedere [Ridimensionare un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Ridimensionamento verticale 
Modifica le risorse (CPU, memoria o archiviazione) dei nodi nel cluster.
- Vantaggi: il software e l'architettura dell'applicazione rimangono inalterati.
- Svantaggi: scalabilità finita, dal momento che la possibilità di aumento delle risorse nei singoli nodi è limitata. Tempo di inattività, perché sarà necessario portare offline i computer fisici o le macchine virtuali per aggiungere o rimuovere le risorse.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

