---
title: "Esercitazione: Eseguire il debug di un'applicazione Web Azure Service Fabric Mesh | Microsoft Docs"
description: In questa esercitazione viene eseguito il debug di un'applicazione Azure Service Fabric Mesh in esecuzione nel cluster locale.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: ad6812f25ee33bf723ed86d4ec32ca6898d01774
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186739"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Esercitazione: Eseguire il debug di un'applicazione Web Service Fabric Mesh

Questa esercitazione è la seconda parte di una serie e illustra come eseguire il debug di un'applicazione Web Azure Service Fabric Mesh nel cluster di sviluppo locale.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Cosa accade quando si crea un'applicazione Azure Service Fabric Mesh
> * Come impostare un punto di interruzione per osservare una chiamata da servizio a servizio

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Eseguire il debug dell'app in locale
> * [Pubblicare l'app in Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Assicurarsi di [configurare l'ambiente di sviluppo](service-fabric-mesh-howto-setup-developer-environment-sdk.md), installando tra l'altro il runtime di Service Fabric, l'SDK, Docker e Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Scaricare l'applicazione attività di esempio

Se non si è creata l'applicazione attività di esempio nella [prima parte di questa serie di esercitazioni](service-fabric-mesh-tutorial-create-dotnetcore.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

L'applicazione si trova nella directory `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Compilare ed eseguire il debug nel cluster locale

Subito dopo il caricamento del progetto nel cluster locale viene compilata e distribuita automaticamente un'immagine Docker. Il processo potrebbe richiedere un po' di tempo. Per monitorare lo stato nel riquadro **Output** di Visual Studio, impostare l'elenco a discesa **Mostra output di** del riquadro Output su **Strumenti di Service Fabric**.

Premere **F5** per compilare ed eseguire il servizio in locale. Durante l'esecuzione e il debug del progetto in locale, Visual Studio:

* Si assicurerà che Docker per Windows sia in esecuzione e che sia impostato in modo da usare Windows come sistema operativo del contenitore.
* Scaricherà eventuali immagini di base mancanti di Docker. Questa parte potrebbe richiedere tempo.
* Compilerà o ricompilerà l'immagine di Docker usata per ospitare il progetto di codice.
* Distribuirà ed eseguirà il contenitore nel cluster di sviluppo locale di Service Fabric.
* Eseguirà i servizi e raggiungerà gli eventuali punti di interruzione impostati.

Quando la distribuzione locale è completata e l'app è in esecuzione in Visual Studio, viene aperta una finestra del browser con una pagina Web di esempio predefinita.

**Suggerimenti per il debug**

* Se viene visualizzato l'errore **No Service Fabric local cluster is running** (Nessun cluster locale di Service Fabric è in esecuzione), assicurarsi che Service Local Cluster Manager (SLCM) sia in esecuzione, fare clic con il pulsante destro del mouse sull'icona SLCM sulla barra delle applicazioni e quindi fare clic su **Start Local Cluster** (Avvia cluster locale). Dopo l'avvio, tornare a Visual Studio e premere **F5**.
* Se viene visualizzato un errore **404** all'avvio dell'app, è probabile che le variabili di ambiente in **service.yaml** non siano corrette. Assicurarsi che `ApiHostPort` e `ServiceName` siano impostati conformemente alle istruzioni della sezione [Creare le variabili di ambiente](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables).
* Se sono presenti errori di compilazione in **service.yaml**, assicurarsi che per impostare i rientri delle righe vengano usati gli spazi e non le tabulazioni. Inoltre, per il momento, è necessario compilare l'app usando le impostazioni locali inglese.

### <a name="debug-in-visual-studio"></a>Eseguire il debug in Visual Studio

Per il debug di un'applicazione Service Fabric Mesh in Visual Studio viene usato un cluster di sviluppo locale di Service Fabric. Per scoprire in che modo gli elementi attività vengono recuperati dal servizio back-end, eseguire il debug nel metodo OnGet().
1. Nel progetto **WebFrontEnd** aprire **Pages** > **Index.cshtml** > **Index.cshtml.cs** e impostare un punto di interruzione nel metodo **Get** (riga 17).
2. Nel progetto **ToDoService** aprire **TodoController.cs** e impostare un punto di interruzione nel metodo **OnGet** (riga 15).
3. Tornare al browser e aggiornare la pagina. È stato raggiunto il punto di interruzione nel metodo `OnGet()` del front-end Web. È possibile esaminare la variabile `backendUrl` per conoscere la combinazione delle variabili di ambiente definite nel file **service.yaml** nell'URL usato per contattare il servizio back-end.
4. Eseguire l'istruzione/routine (F10) della chiamata `client.GetAsync(backendUrl).GetAwaiter().GetResult())` per raggiungere il punto di interruzione `Get()` del controller. In questo metodo è possibile scoprire in che modo l'elenco degli elementi attività viene recuperato dall'elenco in memoria.
5. Al termine, premere **MAIUSC+F5** per arrestare il debug del progetto in Visual Studio.
 
## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso:

> [!div class="checklist"]
> * Cosa accade quando si crea un'applicazione Azure Service Fabric Mesh
> * Come impostare un punto di interruzione per osservare una chiamata da servizio a servizio

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Pubblicare un'applicazione Web Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)