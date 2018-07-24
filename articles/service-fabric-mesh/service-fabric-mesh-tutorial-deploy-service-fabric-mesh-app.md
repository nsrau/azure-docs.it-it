---
title: "Esercitazione: Distribuire un'applicazione Service Fabric Mesh in Service Fabric Mesh | Microsoft Docs"
description: Informazioni su come pubblicare un'applicazione Azure Service Fabric Mesh costituita da un sito Web ASP.NET Core che comunica con un servizio web back-end.
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
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126576"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Esercitazione: Distribuire un'applicazione Web Service Fabric Mesh

Questa esercitazione è la terza parte di una serie e illustra come pubblicare un'applicazione Web Azure Service Fabric Mesh direttamente da Visual Studio.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Pubblicare l'app in Azure.
> * Controllare lo stato di distribuzione applicazione
> * Visualizzare tutte le applicazioni attualmente distribuite nella sottoscrizione
> * Visualizzare i log dell'applicazione
> * Pulire le risorse usate dall'app.

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione Web Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Eseguire il debug dell'app in locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Pubblicare l'app in Azure

Si apprenderà come creare un'app Azure Service Fabric Mesh che include un front-end Web ASP.NET e un servizio back-end API Web ASP.NET Core. Verrà quindi eseguito il debug dell'app nel cluster di sviluppo locale e l'app verrà pubblicata in Azure. Al termine, si disporrà di un'app attività semplice che illustra come effettuare una chiamata da servizio a servizio in un'applicazione Web Service Fabric Mesh.

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

Per pubblicare il progetto Service Fabric Mesh in Azure, fare clic con il pulsante destro del mouse su **ServiceFabricMeshApp** in Visual Studio e selezionare **Pubblica**.

Verrà quindi visualizzata la finestra di dialogo **Pubblica applicazione di Service Fabric**.

![Finestra di dialogo per la pubblicazione del progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selezionare l'account e la sottoscrizione di Azure. Scegliere una **Posizione**. In questo articolo viene usata l'area **Stati Uniti orientali**.

In **Gruppo di risorse** selezionare **\<Crea nuovo gruppo di risorse**. Viene visualizzata una finestra di dialogo in cui creare un nuovo gruppo di risorse. In questo articolo viene usata l'area **Stati Uniti orientali** e viene assegnato il nome **sfmeshTutorial1RG** al gruppo di risorse. Se l'organizzazione include più persone che usano la stessa sottoscrizione, scegliere un nome univoco per il gruppo.  Fare clic su **Crea** per creare il gruppo di risorse e tornare alla finestra di dialogo per la pubblicazione.

![Finestra di dialogo del nuovo gruppo di risorse di Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Nella finestra di dialogo **Pubblica applicazione di Service Fabric** selezionare **Crea nuovo registro contenitori\<** in **Registro contenitori di Azure**. Nella finestra di dialogo **Crea registro contenitori** usare un nome univoco per **Nome del registro contenitori**. Impostare il campo **Località**. In questa esercitazione viene usata l'area **Stati Uniti orientali**. Nell'elenco a discesa selezionare il **gruppo di risorse** creato nel passaggio precedente, ad esempio **sfmeshTutorial1RG**. Impostare **SKU** su **Base** e quindi fare clic su **Crea** per tornare alla finestra di dialogo per la pubblicazione.

![Finestra di dialogo del nuovo gruppo di risorse di Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

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

Quando si pubblica in Azure per la prima volta, viene eseguito il push dell'immagine Docker per il Registro contenitori di Azure. Questa operazione richiede tempo a seconda delle dimensioni dell'immagine. Le successive pubblicazioni dello stesso progetto saranno più rapide. È possibile monitorare lo stato di avanzamento del processo di distribuzione selezionando il riquadro **Strumenti di Service Fabric** nella finestra **Output** di Visual Studio. Al termine della distribuzione, nell'output di **Strumenti di Service Fabric** verranno visualizzati l'indirizzo IP e la porta dell'applicazione sotto forma di URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Aprire un Web browser e passare all'URL visualizzato per visitare il sito Web in esecuzione in Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per i passaggi rimanenti è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.35 o successiva. Eseguire `az --version` per trovare la versione. Per installare o eseguire l'aggiornamento all'ultima versione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure CLI 2.0][azure-cli-install].

## <a name="install-the-az-mesh-cli"></a>Installare l'interfaccia della riga di comando di az mesh
Al prompt dell'interfaccia della riga di comando:

1) Rimuovere eventuali installazioni precedenti del modulo dell'interfaccia della riga di comando di Azure Service Fabric Mesh.

```cli
az extension remove --name mesh
```

2)  Installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh. Per l'anteprima l'interfaccia della riga di comando di Azure Service Fabric Mesh viene scritta come estensione dell'interfaccia della riga di comando di Azure, ma per l'anteprima pubblica verrà inclusa nell'interfaccia della riga di comando di Azure.

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>Controllare lo stato di distribuzione applicazione

A questo punto l'applicazione è stata distribuita. È possibile verificarne lo stato con il comando `app show`. 

Il nome dell'applicazione per l'app dell'esercitazione è `ServiceMeshApp`. Per raccogliere i dettagli sull'applicazione, usare il comando seguente:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Visualizzare tutte le applicazioni attualmente distribuite nella sottoscrizione

È possibile usare il comando "app list" per ottenere un elenco di applicazioni distribuite nella sottoscrizione.

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Visualizzare i log dell'applicazione

Esaminare i log dell'applicazione distribuita:

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando tutte le risorse create non sono più necessarie, eliminarle. Poiché è stato creato un nuovo gruppo di risorse per ospitare le risorse del Registro contenitori di Azure e del servizio Service Fabric Mesh, è possibile eliminare tranquillamente l'intero gruppo, in modo da eliminare tutte le risorse associate.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

In alternativa, è possibile eliminare il gruppo di risorse [dal portale](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso:
> [!div class="checklist"]
> * Pubblicare l'app in Azure.
> * Controllare lo stato di distribuzione applicazione
> * Visualizzare tutte le applicazioni attualmente distribuite nella sottoscrizione
> * Visualizzare i log dell'applicazione
> * Pulire le risorse usate dall'app.

Ora che è stata completata la pubblicazione di un'applicazione Service Fabric Mesh in Azure, procedere come segue:

* Esplorare l'[app di esempio Voting](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) per un altro esempio di comunicazione da servizio a servizio.
* Leggere [Risorse di Service Fabric](service-fabric-mesh-service-fabric-resources.md).
* Leggere l'articolo su [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest