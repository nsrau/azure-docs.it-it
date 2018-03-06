---
title: Eseguire il failover e ridimensionare un'app contenitore di Azure Service Fabric | Microsoft Docs
description: "In questa esercitazione si apprenderà come viene gestito il failover di un'applicazione contenitore di Azure Service Fabric  e come ridimensionare i contenitori e i servizi in esecuzione in un cluster."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, contenitori, microservizi, Service Fabric, Azure
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 27d1918125b8c2f79f6506470ae43354e402f9af
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Esercitazione: Illustrare il failover e il ridimensionamento dei servizi contenitore con Service Fabric

Questa è la terza di una serie di esercitazioni. In questa esercitazione si apprenderà come viene gestito il failover nelle applicazioni contenitore di Service Fabric e come ridimensionare i contenitori. In questa esercitazione:

> [!div class="checklist"]
> * Informazioni sul failover del contenitore in un cluster di Service Fabric  
> * Ridimensionare i contenitori front-end Web in un'applicazione

## <a name="prerequisites"></a>prerequisiti
L'applicazione della [parte 2](service-fabric-tutorial-package-containers.md) è ora in esecuzione nel cluster attivo di Service Fabric.

## <a name="fail-over-a-container-in-a-cluster"></a>Effettuare il failover di un contenitore in un cluster
Service Fabric garantisce lo spostamento automatico delle istanze di contenitore in altri nodi del cluster in caso di errore. È anche possibile svuotare manualmente un nodo di contenitori e spostarli ad altri nodi del cluster. È possibile ridimensionare i servizi in più modi. In questo esempio si usa Service Fabric Explorer.

Per effettuare il failover del contenitore front-end, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Fare clic sul nodo **fabric:/TestContainer/azurevotefront** nella visualizzazione albero ed espandere il nodo della partizione (rappresentato da un GUID). Si noti il nome del nodo nella visualizzazione albero, che indica i nodi in cui è attualmente in esecuzione il contenitore, ad esempio `_nodetype_1`.
3. Espandere il nodo **Nodi** nella visualizzazione albero. Fare clic sui puntini di sospensione accanto al nodo che esegue il contenitore.
1. Scegliere **Riavvia** per riavviare il nodo e confermare l'azione di riavvio. Il riavvio causa il failover del contenitore in un altro nodo del cluster.

![noderestart][noderestart]

Si noti che il nome del nodo, che indica la posizione in cui vengono eseguiti i contenitori front-end, assume il nome di un altro nodo nel cluster. Dopo alcuni istanti dovrebbe essere possibile esplorare di nuovo l'applicazione e vedere l'applicazione in esecuzione su un altro nodo.

## <a name="scale-containers-and-services-in-a-cluster"></a>Ridimensionare contenitori e servizi in un cluster
I contenitori di Service Fabric possono essere facilmente ridimensionati in un cluster per supportare il carico dei servizi. È possibile ridimensionare un contenitore modificando il numero di istanze in esecuzione nel cluster.

Per scalare il front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/TestContainer/azurevotefront** nella visualizzazione albero e scegliere **Scale Service** (Ridimensiona servizio).

![sfxscale][sfxscale]

Ora è possibile scegliere di modificare il numero di istanze del front-end Web.

3. Impostare il numero su **2** e fare clic su **Scale Service** (Ridimensiona servizio).
4. Fare clic sul nodo **fabric:/TestContainer/azurevotefront** nella visualizzazione albero ed espandere il nodo della partizione (rappresentato da un GUID).

![sfxscaledone][sfxscaledone]

È ora possibile rilevare che esistono due istanze del servizio. Nella visualizzazione albero si può verificare in quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione abbiamo raddoppiato le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati il failover del contenitore e il ridimensionamento di un'applicazione. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Informazioni sul failover del contenitore in un cluster di Service Fabric  
> * Ridimensionare i contenitori front-end Web in un'applicazione

In questa serie di esercitazioni si è appreso come: 
> [!div class="checklist"]
> * Creare immagini del contenitore
> * Eseguire il push delle immagini del contenitore nel registro contenitori di Azure
> * Creare un pacchetto di contenitori per Service Fabric usando Yeoman
> * Creare ed eseguire un'applicazione di Service Fabric con i contenitori
> * Gestire il failover e il ridimensionamento in Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
