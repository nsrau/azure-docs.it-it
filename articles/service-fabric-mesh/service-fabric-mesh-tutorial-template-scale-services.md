---
title: Esercitazione - Ridimensionare un'app in esecuzione in Azure Service Fabric Mesh | Microsoft Docs
description: In questa esercitazione si apprenderà come ridimensionare i servizi in un'applicazione in esecuzione in Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 70620b5ec57f6bf4403ac959c4c69026ae80b887
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261401"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Esercitazione: Ridimensionare un'applicazione in esecuzione in Service Fabric Mesh

Questa è la seconda di una serie di esercitazioni. Informazioni su come ridimensionare manualmente il numero di istanze dei servizi di un'applicazione [precedentemente distribuita in Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). Al termine, saranno in esecuzione tre istanze di un servizio front-end e due istanze di un servizio dati.

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Configurare il numero desiderato di istanze del servizio
> * Eseguire un aggiornamento

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Distribuire un'applicazione in Service Fabric Mesh usando un modello](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Ridimensionare un'applicazione in esecuzione in Service Fabric Mesh
> * [Aggiornare un'applicazione in esecuzione in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Rimuovere un'applicazione](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* [Installare l'interfaccia della riga di comando di Azure e l'interfaccia della riga di comando di Service Fabric Mesh in locale](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>Ridurre o aumentare manualmente il numero di istanze dei servizi

Uno dei principali vantaggi della distribuzione di applicazioni in Service Fabric Mesh è la possibilità di ridurre o aumentare facilmente il numero di istanze dei servizi. Questo consente di gestire diverse quantità di carico nei servizi o migliorare la disponibilità.

Questa esercitazione usa l'esempio di elenco di attività come esempio, che è stata [distribuita in precedenza](service-fabric-mesh-tutorial-template-deploy-app.md) e dovrebbe ora essere in esecuzione. L'applicazione ha due servizi: WebFrontEnd e ToDoService. Ogni servizio è stato distribuito inizialmente con un numero di repliche pari a 1.  Per vedere il numero di repliche in esecuzione per il servizio WebFrontEnd, eseguire le operazioni seguenti:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Per vedere il numero di repliche in esecuzione per il servizio ToDoService, eseguire le operazioni seguenti:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

Nel modello di distribuzione per la risorsa dell'applicazione, ogni servizio ha una proprietà *replicaCount* che può essere usata per impostare il numero di volte in cui si vuole distribuire il servizio. Un'applicazione può essere costituita da più servizi, ognuno dei quali con un valore *replicaCount* univoco, che vengono distribuiti e gestiti insieme. Per ridimensionare il numero di repliche del servizio, modificare il valore *replicaCount* per ogni servizio ridimensionare nel modello o di distribuzione o nel file dei parametri.  Aggiornare quindi l'applicazione.

### <a name="modify-the-deployment-template-parameters"></a>Modificare i parametri del modello di distribuzione

Quando sono presenti valori nel modello che si prevede di dover cambiare una volta che l'applicazione viene distribuita, oppure si vuole avere la possibilità di cambiare in base alla distribuzione (se si prevede di riusare questo modello per altre distribuzioni), la procedura consigliata consiste nel parametrizzare i valori.

In precedenza, l'applicazione è stata distribuita con i file del [modello di distribuzione mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [ del file dei parametri mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Aprire file dei parametri [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) in locale e impostare il valore *frontEndReplicaCount* su 3 e il valore *serviceReplicaCount* su 2:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Salvare le modifiche al file di parametri.  I parametri *frontEndReplicaCount* e *serviceReplicaCount* vengono dichiarati nella sezione *parameters* sezione del [modello di distribuzione mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

La proprietà *replicaCount* del servizio WebFrontEnd referenzia il parametro *frontEndReplicaCount* e la proprietà *replicaCount* del servizio ToDoService referenzia il parametro *serviceReplicaCount*:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Dopo aver modificato il modello, aggiornare l'applicazione.

### <a name="upgrade-your-application"></a>Aggiornare l'applicazione

Mentre l'applicazione è in esecuzione, è possibile aggiornarla ridistribuendo il modello e il file dei parametri aggiornati:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Verrà avviato un aggiornamento in sequenza dell'applicazione e in pochi minuti si potrà osservare l'aumento del numero di istanze dei servizi.  Per vedere il numero di repliche in esecuzione per il servizio WebFrontEnd, eseguire le operazioni seguenti:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Per vedere il numero di repliche in esecuzione per il servizio ToDoService, eseguire le operazioni seguenti:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Configurare il numero desiderato di istanze del servizio
> * Eseguire un aggiornamento

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Aggiornare un'applicazione in esecuzione in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
