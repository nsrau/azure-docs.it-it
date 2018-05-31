---
title: Esercitazione sull'installazione di un'applicazione nel cluster autonomo di Service Fabric - Azure Service Fabric | Microsoft Docs
description: In questa esercitazione si apprenderà come installare un'applicazione in un cluster autonomo di Service Fabric.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 65c0ca98393fbb4f54eee2f4864218f231765904
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208574"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Esercitazione: Distribuire un'applicazione in un cluster autonomo di Service Fabric

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in AWS e viene distribuita un'applicazione al suo interno.

Questa è la terza di una serie di esercitazioni.  I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" di Service Fabric. Questa esercitazione illustra come creare un'infrastruttura AWS necessaria per ospitare questo cluster autonomo.

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Scaricare l'app di esempio
> * Distribuirla nel cluster

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare questa esercitazione:

* [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
* [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio

In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Distribuire l'app nel cluster di Service Fabric

Ora che l'applicazione è stata scaricata, è possibile distribuirla in un cluster direttamente da Visual Studio.

1. Aprire Visual Studio.

2. Selezionare **File** > **Apri**

3. Passare alla cartella in cui è stato clonato il repository git e selezionare Voting.sln

4. Fare clic con il pulsante destro del mouse sul progetto di applicazione `Voting` in Esplora soluzioni e scegliere **Pubblica**

5. Selezionare l'elenco a discesa per **Endpoint connessione** e immettere il nome DNS pubblico di uno dei nodi del cluster.  Ad esempio: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`

6. Aprire il browser preferito e digitare l'indirizzo del cluster (l'endpoint della connessione; l'app viene distribuita nella porta 8080, ad esempio ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Risposta API dal cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Passaggi successivi

Nella terza parte della serie si è appreso come distribuire un'applicazione nel cluster:

> [!div class="checklist"]
> * Scaricare l'app di esempio
> * Distribuirla nel cluster

Per pulire il cluster, passare alla quarta parte della serie.

> [!div class="nextstepaction"]
> [Pulire le risorse](service-fabric-tutorial-standalone-clean-up.md)