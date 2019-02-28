---
title: "Esercitazione: Distribuire un'applicazione Azure Service Fabric Mesh  | Microsoft Docs"
description: Informazioni su come usare Visual Studio per pubblicare un'applicazione Azure Service Fabric Mesh costituita da un sito Web ASP.NET Core che comunica con un servizio web back-end.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: eef4cfaff38a96597794354cc991f5d3eeae9404
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806624"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Esercitazione: Distribuire un'applicazione Azure Service Fabric Mesh

Questa esercitazione è la terza parte di una serie e illustra come pubblicare un'applicazione Web Azure Service Fabric Mesh direttamente da Visual Studio.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Pubblicare l'applicazione in Azure usando Visual Studio.
> * Controllare lo stato di distribuzione applicazione
> * Visualizzare tutte le applicazioni attualmente distribuite nella sottoscrizione

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'app Service Fabric Mesh in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Eseguire il debug di un'app Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Distribuire un'app Service Fabric Mesh
> * [Aggiornare un'app Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Pulire le risorse di Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Assicurarsi di [configurare l'ambiente di sviluppo](service-fabric-mesh-howto-setup-developer-environment-sdk.md), installando tra l'altro il runtime di Service Fabric, l'SDK, Docker e Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Scaricare l'applicazione attività di esempio

Se non si è creata l'applicazione attività di esempio nella [seconda parte di questa serie di esercitazioni](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

L'applicazione si trova nella directory `src\todolistapp`.

## <a name="publish-to-azure"></a>Pubblicazione in Azure

Per pubblicare il progetto Service Fabric Mesh in Azure, fare clic con il pulsante destro del mouse su **todolistapp** in Visual Studio e selezionare **Pubblica**.

Verrà quindi visualizzata la finestra di dialogo **Pubblica applicazione di Service Fabric**.

![Finestra di dialogo per la pubblicazione del progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selezionare l'account e la sottoscrizione di Azure. Scegliere una **Posizione**. In questo articolo viene usata l'area **Stati Uniti orientali**.

In **Gruppo di risorse** selezionare **\<Crea nuovo gruppo di risorse**. Viene visualizzata una finestra di dialogo in cui creare un nuovo gruppo di risorse. In questo articolo viene usata l'area **Stati Uniti orientali** e viene assegnato il nome **sfmeshTutorial1RG** al gruppo di risorse. Se l'organizzazione include più persone che usano la stessa sottoscrizione, scegliere un nome univoco per il gruppo.  Fare clic su **Crea** per creare il gruppo di risorse e tornare alla finestra di dialogo per la pubblicazione.

![Finestra di dialogo del nuovo gruppo di risorse di Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Nella finestra di dialogo **Pubblica applicazione di Service Fabric**, in **Registro Azure Container**, selezionare **\<Crea nuovo registro contenitori**. Nella finestra di dialogo **Crea registro contenitori** usare un nome univoco per **Nome del registro contenitori**. Impostare il campo **Località**. In questa esercitazione viene usata l'area **Stati Uniti orientali**. Nell'elenco a discesa selezionare il **gruppo di risorse** creato nel passaggio precedente, ad esempio **sfmeshTutorial1RG**. Impostare lo **SKU** su **Basic** e premere **Crea** per creare il registro contenitori privato di Azure e tornare alla finestra di dialogo pubblica.

![Finestra di dialogo del nuovo registro contenitori di Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Se viene visualizzato un errore per segnalare che un provider di risorse non è stato registrato per la sottoscrizione, è possibile eseguire la registrazione. Verificare prima di tutto se il provider di risorse è disponibile per la sottoscrizione:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Se il provider del registro contenitori (`Microsoft.ContainerRegistry`) è disponibile, registrarlo da PowerShell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Nella finestra di dialogo per la pubblicazione fare clic sul pulsante **Pubblica** per distribuire l'applicazione Service Fabric in Azure.

Quando si pubblica in Azure per la prima volta, viene eseguito il push dell'immagine Docker per il Registro Azure Container. Questa operazione richiede tempo a seconda delle dimensioni dell'immagine. Le successive pubblicazioni dello stesso progetto saranno più rapide. È possibile monitorare lo stato di avanzamento del processo di distribuzione selezionando il riquadro **Strumenti di Service Fabric** nella finestra **Output** di Visual Studio. Al termine della distribuzione, nell'output di **Strumenti di Service Fabric** verranno visualizzati l'indirizzo IP e la porta dell'applicazione sotto forma di URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Aprire un Web browser e passare all'URL visualizzato per visitare il sito Web in esecuzione in Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di mesh Service Fabric

Per i passaggi rimanenti è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Controllare lo stato di distribuzione applicazione

A questo punto l'applicazione è stata distribuita. È possibile verificarne lo stato con il comando `app show`. 

Il nome dell'applicazione per l'app dell'esercitazione è `todolistapp`. Per raccogliere i dettagli sull'applicazione, usare il comando seguente:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>Ottenere l'indirizzo IP della distribuzione

Per ottenere l'indirizzo IP per l'applicazione, usare il comando seguente:
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Visualizzare tutte le applicazioni attualmente distribuite nella sottoscrizione

È possibile usare il comando "app list" per ottenere un elenco di applicazioni distribuite nella sottoscrizione.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:
> [!div class="checklist"]
> * Pubblicare l'app in Azure.
> * Controllare lo stato di distribuzione applicazione
> * Visualizzare tutte le applicazioni attualmente distribuite nella sottoscrizione

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Aggiornare un'app Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
