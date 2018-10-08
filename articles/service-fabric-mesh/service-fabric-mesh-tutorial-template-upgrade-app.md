---
title: Esercitazione - Aggiornare un'app in esecuzione in Azure Service Fabric Mesh | Microsoft Docs
description: In questa esercitazione si apprenderà come aggiornare un'applicazione di Service Fabric in esecuzione in Service Fabric Mesh.
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
ms.date: 09/18/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: c82e0cd5bd6a15ff33f51b4e88f68c13080f595d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967966"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Esercitazione: Aggiornare un'applicazione di Service Fabric in esecuzione in Service Fabric Mesh

Questa è la terza di una serie di esercitazioni. Si apprenderà come eseguire l'aggiornamento di un'applicazione di Service Fabric che è stata [distribuita in precedenza in Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md) aumentando le risorse della CPU allocate.  Una volta completato, è necessario avere un servizio front-end Web in esecuzione con le maggiori risorse della CPU.

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Cambiare le configurazioni dell'applicazione
> * Aggiornare un'applicazione in esecuzione in Service Fabric Mesh

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Distribuire un'applicazione in Service Fabric Mesh usando un modello](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Ridimensionare un'applicazione in esecuzione in Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * Aggiornare un'applicazione in esecuzione in Service Fabric Mesh
> * [Rimuovere un'applicazione](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Aprire [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) o [installare l'interfaccia della riga di comando di Azure e il Service Fabric Mesh in locale](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally).

## <a name="upgrade-application-configurations"></a>Aggiornare le configurazioni dell'applicazione

Uno dei principali vantaggi della distribuzione delle applicazioni su Service Fabric Mesh è la possibilità di aggiornare facilmente la configurazione dell'applicazione.  Ad esempio, la CPU o le risorse di memoria per i servizi.

Questa esercitazione usa l'esempio di elenco di attività come esempio, che è stata [distribuita in precedenza](service-fabric-mesh-tutorial-template-deploy-app.md) e dovrebbe ora essere in esecuzione. L'applicazione ha due servizi: WebFrontEnd e ToDoService. Ogni servizio è stato distribuito inizialmente con le risorse della CPU pari a 0,5.  Per visualizzare le risorse della CPU per il servizio WebFrontEnd, eseguire le operazioni seguenti:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

Nel modello di distribuzione per la risorsa dell'applicazione, ogni servizio dispone di una proprietà *cpu* che può essere usata per impostare le risorse della CPU richieste. Un'applicazione può essere costituita da più servizi, ognuno dei quali con un'impostazione *cpu* univoca, che vengono distribuiti e gestiti insieme. Per aumentare le risorse della CPU del servizio front-end Web, modificare il valore *cpue* nel file de modello di distribuzione o dei parametri.  Aggiornare quindi l'applicazione.

### <a name="modify-the-deployment-template-parameters"></a>Modificare i parametri del modello di distribuzione

Quando sono presenti valori nel modello che si prevede di dover cambiare una volta che l'applicazione viene distribuita, oppure si vuole avere la possibilità di cambiare in base alla distribuzione (se si prevede di riusare questo modello per altre distribuzioni), la procedura consigliata consiste nel parametrizzare i valori.

In precedenza, l'applicazione è stata distribuita con i file del [modello di distribuzione mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [ del file dei parametri mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Aprire il file dei parametri [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) in locale e impostare il valore *frontEndCpu* su 1:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Salvare le modifiche al file di parametri.  

Il parametro *frontEndCpu* viene dichiarato nella sezione *parametri* del [modello di distribuzione mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

La proprietà del servizio WebFrontEnd *codePackages->resources->requests->cpu* fa riferimento al parametro *frontEndCpu*:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Aggiornare l'applicazione

Mentre l'applicazione è in esecuzione, è possibile aggiornarla ridistribuendo il modello e il file dei parametri aggiornati:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Verrà avviato un aggiornamento in sequenza dell'applicazione e in pochi minuti dovrebbe essere visibile l'aumento delle risorse della CPU.  Per visualizzare le risorse della CPU per il servizio WebFrontEnd, eseguire le operazioni seguenti:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Cambiare le configurazioni dell'applicazione
> * Aggiornare un'applicazione in esecuzione in Service Fabric Mesh

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Rimuovere un'applicazione di Service Fabric Mesh](service-fabric-mesh-tutorial-template-remove-app.md)
