---
title: Ridimensionare i servizi in un'applicazione Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su come ridimensionare in modo indipendente i servizi all'interno di un'applicazione in esecuzione su mesh Service Fabric tramite l'interfaccia della riga di comando di Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a4260fd808643971036ad87c01bd2fdec299ccc6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089744"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Ridimensionare i servizi all'interno di un'applicazione in esecuzione su Service Fabric Mesh

Questo articolo illustra come ridimensionare in modo indipendente i microservizi all'interno di un'applicazione. In questo esempio, l'applicazione Oggetti visivi è costituita da due microservizi; `web` e `worker`. 

Il servizio `web` è un'applicazione ASP.NET Core con una pagina Web che mostra i triangoli nel browser. Nel browser viene visualizzato un triangolo per ogni istanza del servizio `worker`. 

Il servizio `worker` sposta il triangolo in un intervallo predefinito nello spazio e invia il percorso del triangolo al servizio `web`. Usa il DNS per risolvere l'indirizzo del servizio `web`.

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

## <a name="deploy-the-application-with-one-worker-service"></a>Distribuire l'applicazione con un servizio del ruolo di lavoro
Creare l'applicazione nel gruppo di risorse usando il comando `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Il comando precedente consente di distribuire un'applicazione Linux usando un [modello mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Se si vuole distribuire un'applicazione Windows, usare il [modello mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Le dimensioni delle immagini dei contenitori Windows sono maggiori rispetto a quelle delle immagini dei contenitori Linux, di conseguenza la distribuzione potrebbe richiedere più tempo.

In pochi minuti, il comando dovrebbe restituire:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Aprire l'applicazione
Dopo la corretta distribuzione dell'applicazione, ottenere l'indirizzo IP pubblico relativo all'endpoint servizio e aprirlo in un browser. Visualizza una pagina Web con un triangolo che si muove attraverso lo spazio.

Il comando di distribuzione restituisce l'indirizzo IP pubblico dell'endpoint servizio. Facoltativamente, è inoltre possibile eseguire una query nella risorsa di rete per trovare l'indirizzo IP pubblico dell'endpoint servizio. 
 
Il nome della risorsa di rete di questa applicazione è `visualObjectsNetwork`, recuperare le relative informazioni con il comando seguente. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Ridimensionare il servizio `worker`

Ridimensionare il servizio `worker` in tre istanze usando il comando seguente. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Il comando precedente consente di distribuire un'applicazione Linux usando un [modello mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Se si vuole distribuire un'applicazione Windows, usare il [modello mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Le dimensioni delle immagini dei contenitori Windows sono maggiori rispetto a quelle delle immagini dei contenitori Linux, di conseguenza la distribuzione potrebbe richiedere più tempo.

Dopo aver distribuito correttamente l'applicazione, il browser dovrebbe visualizzare una pagina Web con tre triangoli che si spostano nello spazio.

## <a name="delete-the-resources"></a>Eliminare le risorse

Per conservare le risorse limitate assegnate al programma di anteprima, eliminare le risorse di frequente. Per eliminare le risorse correlate a questo esempio, eliminare il gruppo di risorse in cui sono state distribuite.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare l'applicazione di esempio Oggetti visivi in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Per altre informazioni sul modello di risorse di Service Fabric, vedere [Service Fabric Mesh Resource Model](service-fabric-mesh-service-fabric-resources.md) (Modello di risorsa mesh Service Fabric).
- Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).