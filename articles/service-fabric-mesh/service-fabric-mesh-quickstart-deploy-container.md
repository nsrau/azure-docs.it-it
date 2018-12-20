---
title: 'Guida introduttiva: Distribuire Hello World in Azure Service Fabric Mesh | Microsoft Docs'
description: Questa guida introduttiva spiega come distribuire un'applicazione Service Fabric Mesh in Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: Non aggiungere o modificare parole chiave senza consultare l'esperto SEO.
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 4be24b00c3ac4ffadf7eafdc7397f59113ec03b2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088364"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Guida introduttiva: Distribuire Hello World in mesh Service Fabric

[Service Fabric Mesh](service-fabric-mesh-overview.md) semplifica la creazione e la gestione di applicazioni di microservizi in Azure senza dover eseguire il provisioning di macchine virtuali. In questa guida introduttiva si creerà un'applicazione Hello World in Azure che verrà esposta a Internet. Per completare questa operazione, è sufficiente un solo comando. Dopo un paio di secondi nel browser verrà visualizzato quanto segue:

![App Hello World nel browser][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Se non si ha già un account Azure, [crearne uno gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di mesh Service Fabric 
Per completare questa guida introduttiva è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere ad Azure e impostare la sottoscrizione.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse in cui distribuire l'applicazione. È possibile usare un gruppo di risorse esistente e ignorare questo passaggio. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Creare l'applicazione nel gruppo di risorse usando il comando `az mesh deployment create`.  Eseguire questo comando:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Il comando precedente consente di distribuire un'applicazione Linux usando un [modello linux.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Se si vuole distribuire un'applicazione Windows, usare il [modello windows.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). Le dimensioni delle immagini dei contenitori Windows sono maggiori rispetto a quelle delle immagini dei contenitori Linux, di conseguenza la distribuzione potrebbe richiedere più tempo.

Questo comando genera un frammento di codice JSON, visualizzato di seguito. Nella sezione ```outputs``` dell'output JSON copiare la proprietà ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Queste informazioni provengono dalla sezione ```outputs``` del modello ARM. Come mostrato di seguito, questa sezione fa riferimento alla risorsa Gateway per recuperare l'indirizzo IP pubblico. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Aprire l'applicazione
Dopo la corretta distribuzione dell'applicazione, copiare l'indirizzo IP pubblico relativo all'endpoint servizio dall'output dell'interfaccia della riga di comando. Aprire l'indirizzo IP in un Web browser. Viene visualizzata una pagina web con il logo di Azure Service Fabric Mesh.

## <a name="check-the-application-details"></a>Controllare i dettagli dell'applicazione
Per controllare lo stato dell'applicazione, è possibile usare il comando `az mesh app show`. Questo comando fornisce informazioni utili per il completamento.

Il nome dell'applicazione per questa guida introduttiva è `helloWorldApp`. Per raccogliere i dettagli relativi all'applicazione, eseguire il comando seguente:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Visualizzare i log dell'applicazione

Per esaminare i log dell'applicazione distribuita, usare il comando `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si è pronti per eliminare l'applicazione, eseguire il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, nonché l'applicazione e le risorse di rete che contiene.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla creazione e la distribuzione di applicazioni Service Fabric Mesh, proseguire con l'esercitazione.
> [!div class="nextstepaction"]
> [Creare e distribuire un'applicazione Web multiservizio](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
