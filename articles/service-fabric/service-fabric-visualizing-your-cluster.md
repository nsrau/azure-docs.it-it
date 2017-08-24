---
title: Visualizzazione del cluster con Service Fabric Explorer | Microsoft Docs
description: "Service Fabric Explorer è uno strumento basato sul Web per analizzare e gestire nodi e applicazioni cloud in un cluster di Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: c58de22e29b6403e88bf22bdfe704a25757cdfca
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017


---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizzare il cluster con Service Fabric Explorer
Service Fabric Explorer è uno strumento basato sul web per analizzare e gestire applicazioni e nodi in un cluster di Service Fabric di Azure. Service Fabric Explorer è ospitato direttamente all'interno del cluster, pertanto è sempre disponibile indipendentemente da dove il cluster sia in esecuzione.

## <a name="video-tutorial"></a>Esercitazione video

Per informazioni sull'uso di Service Fabric Explorer, vedere il video seguente di Microsoft Virtual Academy:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Connettersi a Service Fabric Explorer
Se si sono seguite le istruzioni per [preparare l'ambiente di sviluppo](service-fabric-get-started.md), è possibile avviare Service Fabric Explorer nel cluster locale andando su http://localhost:19080/Explorer.

## <a name="understand-the-service-fabric-explorer-layout"></a>Comprendere il layout di Service Fabric Explorer
È possibile spostarsi all'interno di Service Fabric Explorer seguendo la struttura ad albero a sinistra. Nella radice dell'albero, il dashboard del cluster fornisce una panoramica del cluster, inclusi un riepilogo dell'applicazione e l'integrità del nodo.

![Dashboard del cluster di Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Visualizzare il layout del cluster
I nodi in un cluster di Service Fabric sono posizionati in una griglia bidimensionale di domini di errore e domini di aggiornamento. Questa posizione garantisce la disponibilità delle applicazioni in caso di errori hardware e aggiornamenti dell'applicazione. È possibile visualizzare la disposizione del cluster corrente mediante la mappa del cluster.

![Mappa del cluster di Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Visualizzare applicazioni e servizi
Il cluster contiene due sotto-alberi: uno per le applicazioni e un altro per i nodi.

È possibile usare la visualizzazione delle applicazioni per spostarsi nella gerarchia logica di Service Fabric: applicazioni, servizi, partizioni e repliche.

Nell'esempio seguente, l'applicazione **MyApp** è costituita da due servizi, **MyStatefulService** e **WebService**. Poiché **MyStatefulService** è con stato, include una partizione con una replica primaria e due repliche secondarie. Al contrario, il WebSvcService è senza stato e contiene una singola istanza.

![Visualizzazione delle applicazioni di Service Fabric Explorer][sfx-application-tree]

A ogni livello della struttura ad albero, il riquadro principale mostra informazioni pertinenti all'elemento. Ad esempio, è possibile visualizzare lo stato di integrità e la versione di un determinato servizio.

![Riquadro essentials di Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visualizzare i nodi del cluster
La visualizzazione dei nodi mostra il layout fisico del cluster. Per un determinato nodo, è possibile esaminare le applicazioni con il codice distribuito in quel nodo. In particolare, è possibile visualizzare le repliche attualmente in esecuzione.

## <a name="actions"></a>Azioni
Service Fabric Explorer offre un modo rapido per richiamare le azioni su nodi, applicazioni e servizi all'interno del cluster.

Ad esempio, per eliminare un'istanza dell'applicazione, è sufficiente scegliere l'applicazione dall'albero a sinistra e quindi scegliere **Azioni** > **Elimina applicazione**.

![Eliminazione di un'applicazione in Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> È possibile eseguire le stesse azioni facendo clic sui puntini di sospensione accanto a ogni elemento.
>
>

La tabella seguente elenca le azioni disponibili per ogni entità:

| **Entità** | **Azione** | **Descrizione** |
| --- | --- | --- |
| Tipo di applicazione |Annullare il provisioning del tipo |Rimuove il pacchetto dell'applicazione dall'archivio immagini del cluster. È necessario rimuovere prima tutte le applicazioni di quel tipo. |
| Applicazione |Elimina applicazione |Eliminare l'applicazione, inclusi tutti i servizi correlati e il relativo stato, se presente. |
| Service |Eliminare il servizio |Eliminare il servizio e il relativo stato (se presente). |
| Nodo |Activate |Attivare il nodo. |
| Nodo | Disattivare (sospendere) | Sospendere il nodo nello stato corrente. I servizi continueranno a essere eseguiti, tuttavia Service Fabric non sposterà in modo proattivo alcun elemento a meno che non sia necessario per impedire un'interruzione o un caso di incoerenza di dati. Questa azione viene in genere usata per abilitare i servizi di debug in un nodo specifico in modo da garantire che non si spostino durante l'ispezione. | |
| Nodo | Disattivare (riavviare) | Spostare tutti i servizi in memoria all'esterno di un nodo e chiudere i servizi permanenti in modo sicuro. Questa azione viene in genere usata quando i processi host o i computer devono essere riavviati. | |
| Nodo | Disattivare (rimuovere i dati) | Chiudere in modo sicuro tutti i servizi in esecuzione sul nodo dopo la creazione di un numero sufficiente di repliche riserva. Questa azione viene in genere usata quando un nodo (o almeno lo spazio di archiviazione correlato) viene reso improduttivo in modo permanente. | |
| Nodo | Rimuovere lo stato del nodo | Rimuovere le repliche di un nodo dal cluster. Questa azione viene in genere usata quando un nodo che ha già avuto esito negativo viene ritenuto non recuperabile. | |
| Nodo | Riavvia | Riavviare il nodo per simularne un errore. Altre informazioni sono disponibili [qui](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps) | |

Poiché molte azioni sono distruttive, viene richiesto di confermare la finalità prima del completamento dell'azione.

> [!TIP]
> Ogni azione eseguibile con Service Fabric Explorer può essere eseguita anche tramite PowerShell o un'API REST per abilitare l'automazione.
>
>

È inoltre possibile usare Service Fabric Explorer per creare istanze di applicazione per un tipo e una versione dell'applicazione specifici. Scegliere il tipo di applicazione nella visualizzazione albero, quindi fare clic sul collegamento **Create app instance** (Crea un'istanza dell'app).

![Creazione di un'istanza dell'applicazione in Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Non è attualmente possibile impostare parametri per le istanze dell'applicazione create mediante Service Fabric Explorer, per le quali vengono usati valori di parametro predefiniti.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Connettersi a un cluster di Service Fabric remoto
Se si conosce l'endpoint del cluster e si dispone di autorizzazioni sufficienti, è possibile accedere a Service Fabric Explorer da qualsiasi browser. Service Fabric Explorer è infatti un altro servizio in esecuzione nel cluster.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>Scoprire l'endpoint di Service Fabric Explorer per un cluster remoto
Per accedere a Service Fabric Explorer per un determinato cluster, inserire nel browser l'indirizzo seguente:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

Per i cluster di Azure, l'URL completo è disponibile anche nel riquadro Essentials del cluster del portale di Azure.

### <a name="connect-to-a-secure-cluster"></a>Connettersi a un cluster sicuro
È possibile controllare l'accesso al cluster di Service Fabric con certificati oppure usando Azure Active Directory (AAD).

Se si prova a connettersi a Service Fabric Explorer in un cluster sicuro, a seconda della configurazione del cluster, è necessario presentare un certificato client oppure eseguire l'accesso con AAD.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Testabilità](service-fabric-testability-overview.md)
* [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Distribuzione di un'applicazione di Infrastruttura di servizi mediante PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png

