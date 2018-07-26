---
title: Distribuire un'app in Azure Service Fabric Mesh usando un modello | Microsoft Docs
description: Informazioni su come distribuire un'applicazione .NET Core in mesh Service Fabric da un modello tramite l'interfaccia della riga di comando di Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceputal
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 8d813669f2f44fd64db669e9750e3bc064c7f916
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090331"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Distribuire un'applicazione mesh Service Fabric in mesh Service Fabric usando un modello
Questo articolo illustra come distribuire un'applicazione .NET Core in mesh Service Fabric usando un modello. Al termine sarà disponibile un'applicazione di voto con un front-end Web ASP.NET Core che salva i risultati delle votazioni in un servizio back-end nel cluster. Il front-end usa il DNS per risolvere l'indirizzo del servizio back-end.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di mesh Service Fabric 
Per completare questa attività è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

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
Creare l'applicazione nel gruppo di risorse usando il comando `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Il comando precedente consente di distribuire un'applicazione Windows usando un [modello mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Se si vuole distribuire un'applicazione Linux, usare il [modello mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Le dimensioni delle immagini dei contenitori Windows sono maggiori rispetto a quelle delle immagini dei contenitori Linux, di conseguenza la distribuzione potrebbe richiedere più tempo.

In pochi minuti, il comando dovrebbe restituire:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Aprire l'applicazione
Dopo la corretta distribuzione dell'applicazione, ottenere l'indirizzo IP pubblico relativo all'endpoint servizio e aprirlo in un browser. Viene visualizzata la pagina Web seguente. 

![Applicazione di voto](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Ora è possibile aggiungere opzioni di voto all'applicazione e votare su di essa, oppure eliminare le opzioni di voto.

Il comando di distribuzione restituisce l'indirizzo IP pubblico dell'endpoint servizio. Facoltativamente, è inoltre possibile eseguire una query nella risorsa di rete per trovare l'indirizzo IP pubblico dell'endpoint servizio. 

Il nome della risorsa di rete di questa applicazione è `VotingAppNetwork`, recuperare le relative informazioni con il comando seguente. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Controllare i dettagli dell'applicazione
Per controllare lo stato dell'applicazione, è possibile usare il comando `app show`. Il nome dell'applicazione distribuita è "VotingApp", quindi recuperare i suoi dettagli. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Elencare le applicazioni distribuite
È possibile usare il comando "app list" per ottenere un elenco di applicazioni distribuite nella sottoscrizione. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Pulire le risorse
Quando l'applicazione e le relative risorse non sono più necessarie, eliminare il gruppo di risorse che le contiene. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare l'applicazione di esempio di voto in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).


