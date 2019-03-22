---
title: Visualizzazione del cluster con Azure Service Fabric Explorer | Microsoft Docs
description: Service Fabric Explorer è un'applicazione per analizzare e gestire nodi e applicazioni cloud in un cluster di Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 47b5e89164173869d6964e2c5e392bb99e0c8555
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317392"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizzare il cluster con Service Fabric Explorer

Service Fabric Explorer (SFX) è uno strumento open source per analizzare e gestire i cluster di Azure Service Fabric. Service Fabric Explorer è un'applicazione desktop per Windows, macOS e Linux.

## <a name="service-fabric-explorer-download"></a>Download di Service Fabric Explorer

Usare i collegamenti seguenti per scaricare Service Fabric Explorer come applicazione desktop:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> La versione desktop di Service Fabric Explorer può presentare più o meno funzionalità rispetto alla versione con supporto per cluster. È possibile eseguire il fallback alla versione di Service Fabric Explorer distribuita al cluster per la massima compatibilità delle funzionalità.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Esecuzione di Service Fabric Explorer dal cluster

Service Fabric Explorer è inoltre ospitato in un endpoint di gestione HTTP del cluster di Service Fabric. Per avviare SFX in un web browser, passare all'endpoint di gestione HTTP del cluster da qualsiasi browser, ad esempio https:\//clusterFQDN:19080.

Per la configurazione della workstation di sviluppo, è possibile avviare Service Fabric Explorer sul cluster locale passando a https://localhost:19080/Explorer. Vedere l'articolo [Preparare l'ambiente di sviluppo](service-fabric-get-started.md).

> [!NOTE]
> Se il cluster è protetto da un certificato autofirmato, si riceve dal Web browser il messaggio di errore "This site is not secure" (Questo sito non è sicuro). Per proseguire attraverso i Web browser più moderni è sufficiente ignorare l'avviso. In un ambiente di produzione è consigliabile proteggere il cluster tramite nome comune e un certificato rilasciato da un'autorità di certificazione. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Connettersi a un cluster di Service Fabric
Per connettersi a un cluster di Service Fabric, è necessario specificare l'endpoint di gestione dei cluster (FQDN/IP) e la porta dell'endpoint di gestione HTTP (19080 per impostazione predefinita). Ad esempio https\:/ / mysfcluster.westus.cloudapp.azure.com:19080. Usare la casella di controllo "Connetti a localhost" per connettersi a un cluster locale sulla propria workstation.

### <a name="connect-to-a-secure-cluster"></a>Connettersi a un cluster sicuro
È possibile controllare l'accesso al cluster di Service Fabric con certificati oppure usando Azure Active Directory (AAD).

Se si prova a connettersi a un cluster sicuro, a seconda della configurazione del cluster è necessario presentare un certificato client oppure eseguire l'accesso con AAD.

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
> Ogni azione eseguibile con Service Fabric Explorer può essere eseguita anche tramite PowerShell o un'API REST per abilitare l'automazione.
>
>

È inoltre possibile usare Service Fabric Explorer per creare istanze di applicazione per un tipo e una versione dell'applicazione specifici. Scegliere il tipo di applicazione nella visualizzazione albero, quindi fare clic sul collegamento **Create app instance** (Crea un'istanza dell'app).

![Creazione di un'istanza dell'applicazione in Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer non consente di specificare parametri durante la creazione di istanze dell'applicazione. Per queste istanze vengono usati valori di parametro predefiniti.
>
>

## <a name="event-store"></a>EventStore
EventStore è una funzionalità offerta dalla piattaforma che rende disponibili gli eventi della piattaforma Service Fabric in Service Fabric Explorer e tramite l'API REST. È possibile visualizzare uno snapshot di cosa sta succedendo nel cluster per ogni entità, ad esempio nodo, servizio, applicazione e query in base all'ora dell'evento. Altre informazioni sull'archivio eventi sono disponibili in [Panoramica del servizio EventStore](service-fabric-diagnostics-eventstore.md).   

![EventStore][sfx-eventstore]

>[!NOTE]
>A partire da Service Fabric versione 6.4. Il servizio EventStore non è abilitato per impostazione predefinita e deve essere abilitato nel modello di Resource Manager

>[!NOTE]
>A partire da Service Fabric versione 6.4. le API di EventStore sono disponibili solo per i cluster Windows eseguiti in Azure. Questa funzionalità sarà presto disponibile anche per Linux e per i cluster autonomi.


## <a name="next-steps"></a>Passaggi successivi
* [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Distribuzione di un'applicazione di Infrastruttura di servizi mediante PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
