---
title: Visualizzare i log dei contenitori in Azure Service Fabric | Microsoft Docs
description: Viene descritto come visualizzare i log dei contenitori per un servizio contenitore di Service Fabric in esecuzione tramite Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visualizzare i log per un servizio contenitore di Service Fabric
Azure Service Fabric è un agente di orchestrazione dei contenitori e supporta [contenitori Linux e Windows](service-fabric-containers-overview.md).  In questo articolo viene descritto come visualizzare i log dei contenitori di un servizio contenitore in esecuzione, in modo da diagnosticare e risolvere i problemi.

## <a name="access-container-logs"></a>Accedere ai log dei contenitori
I log dei contenitori sono accessibili mediante [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  In un Web browser aprire Service Fabric Explorer dall'endpoint di gestione del cluster, visitando l'indirizzo [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

I log dei contenitori sono disponibili nel nodo del cluster in cui è in esecuzione l'istanza del servizio contenitore. Ad esempio, è possibile visualizzare i log del contenitore front-end Web dell'[applicazione di voto di esempio per Linux](service-fabric-quickstart-containers-linux.md). Nella visualizzazione struttura ad albero espandere **Cluster**>**Applicazioni**>**VotingType**>**fabric:/Voting/azurevotefront**.  Espandere quindi la partizione (d1aa737e-f22a-e347-be16-eec90be24bc1 in questo esempio) e verificare che il contenitore sia in esecuzione nel nodo del cluster *_lnxvm_0*.

Nella visualizzazione struttura ad albero individuare il pacchetto di codice nel nodo *_lnxvm_0* espandendo **Nodi**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Pacchetti di codice**>**codice**.  Selezionare quindi l'opzione **Log contenitori** per visualizzare i log del contenitore.

![Piattaforma Service Fabric][Image1]


## <a name="next-steps"></a>Passaggi successivi
- Esaminare l'esercitazione [Creare un'applicazione contenitore Linux](service-fabric-tutorial-create-container-images.md).
- Altre informazioni su [Service Fabric e contenitori](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
