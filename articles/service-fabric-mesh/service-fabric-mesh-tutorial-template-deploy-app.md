---
title: Esercitazione - Distribuire un'app in Azure Service Fabric Mesh | Microsoft Docs
description: In questa esercitazione si apprenderà come distribuire un'applicazione in Service Fabric Mesh usando un modello.
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
ms.openlocfilehash: 773a466f9619f162fe3f7cbeeab0b766d3ca32ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864865"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Esercitazione: Distribuire un'applicazione in Service Fabric Mesh usando un modello

Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un'applicazione Azure Service Fabric Mesh usando un modello.  L'applicazione è composta da un servizio front-end Web ASP.NET e un servizio back-end API Web ASP.NET Core, disponibili nell'hub Docker.  Si eseguirà il pull delle immagini dei due contenitori dall'hub Docker e quindi si eseguirà il push nel registro privato. Si creerà quindi un modello di Azure Resource Manager per l'applicazione e l'applicazione verrà distribuita dal registro contenitori in Service Fabric Mesh. Al termine, sarà disponibile una semplice applicazione To Do List, in esecuzione in Service Fabric Mesh.

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un'istanza privata di Registro Azure Container
> * Effettuare il push dell'immagine del contenitore nel registro
> * Creare i file del modello di Resource Manager e dei parametri
> * Distribuire un'applicazione in Service Fabric Mesh

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Distribuire un'applicazione in Service Fabric Mesh usando un modello
> * [Ridimensionare i servizi in un'applicazione in esecuzione in Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Aggiornare un'applicazione in esecuzione in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Rimuovere un'applicazione](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* [Installare Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Installare l'interfaccia della riga di comando di Azure e l'interfaccia della riga di comando di Service Fabric Mesh in locale](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Le immagini del contenitore associate ai servizi nell'applicazione Service Fabric Mesh devono essere archiviate in un registro contenitori.  Questa esercitazione usa un'istanza privata di Registro Azure Container. 

Seguire questa procedura per creare un'istanza di Registro Azure Container.  Se è già disponibile un'istanza di Registro Azure Container, è possibile procedere oltre.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure e impostare la sottoscrizione attiva.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Usare il comando seguente per creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Creare il registro contenitori

Creare un'istanza di Registro Azure Container usando il comando `az acr create`. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Nell'esempio seguente viene usato il nome *myContainerRegistry*. Se un errore segnala che il nome del registro è già usato, scegliere un nome diverso.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Quando viene creato il registro, l'output generato sarà simile al seguente:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Eseguire il push delle immagini in Registro Azure Container

Questa esercitazione usa l'applicazione di esempio To Do List come esempio.  Le immagini del contenitore per i servizi [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) e [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) sono disponibili nell'hub Docker. Vedere [come creare un'app Web di Azure Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md) per informazioni su come compilare l'applicazione in Visual Studio. Service Fabric Mesh supporta l'esecuzione di contenitori Docker Windows o Linux.  Se si utilizzano contenitori Linux, selezionare **Switch to Linux containers** (Passa a contenitori Linux) in Docker.  Se si utilizzano contenitori Windows, selezionare **Switch to Windows containers** (Passa a contenitori Windows) in Docker.

Per eseguire il push di un'immagine in un'istanza di Registro Azure Container, è prima necessario avere un'immagine del contenitore. Se non sono ancora disponibili immagini del contenitore locale, usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per eseguire il pull delle immagini [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) e [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) dall'hub Docker.

Eseguire il pull di immagini di Windows:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Prima di poter eseguire il push di un'immagine nel registro, è necessario contrassegnarla con il nome completo del server di accesso del record di controllo di accesso.

Eseguire il comando seguente per ottenere il nome del server di accesso completo dell'istanza di Registro Azure Container.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Contrassegnare ora l'immagine Docker con il comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). L'esempio seguente contrassegna le immagini seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 e seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709. Se si usano immagini diverse, sostituire questi nomi di immagine nel comando seguente.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Accedere al Registro Azure Container.

```azurecli
az acr login -n myContainerRegistry
```

Eseguire il push dell'immagine nell'istanza di Registro Azure Container con il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Elencare le immagini del contenitore

Per visualizzare i repository nell'istanza di Registro Azure Container, eseguire il comando seguente:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

L'esempio seguente elenca i tag nel repository **azure-mesh-todo-service**.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

L'output precedente conferma la presenza di `azure-mesh-todo-service:1.0-nanoserver-1709` nel registro contenitori privato.  Verificare anche la presenza di `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.

## <a name="retrieve-credentials-for-the-registry"></a>Recuperare le credenziali per il registro

> [!IMPORTANT]
> Non è consigliabile abilitare l'utente amministratore in un'istanza di Registro Azure Container per scenari di produzione. In questo esempio ciò avviene per comodità. Per gli scenari di produzione, usare un'[entità servizio](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal) per l'autenticazione di utenti e del sistema negli scenari di produzione.

Per distribuire un'istanza del contenitore dal registro creato usando un modello, è necessario specificare le credenziali del registro durante la distribuzione. Abilitare prima di tutto l'utente amministratore nel registro con il comando seguente:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Ottenere poi il nome del server del registro, il nome utente e la password, usando i comandi seguenti:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Usare il nome del server di accesso di Registro Azure Container, il nome utente e la password restituiti durante la creazione dei file del modello di Resource Manager e dei parametri nella sezione seguente.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Scaricare ed esplorare i file del modello e dei parametri

Un'applicazione Service Fabric Mesh è una risorsa di Azure che è possibile distribuire e gestire usando i modelli di Azure Resource Manager (RM). Per comprendere i concetti di distribuzione e gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) e [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates).

Questa esercitazione usa l'applicazione di esempio To Do List come esempio.  Invece di creare nuovi file per il modello e i parametri, scaricare il file del [modello di distribuzione mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e il file dei [parametri mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

### <a name="parameters"></a>Parametri
Quando sono presenti valori nel modello che si prevede di dover cambiare una volta che l'applicazione viene distribuita, oppure si vuole avere la possibilità di cambiare in base alla distribuzione (se si prevede di riusare questo modello per altre distribuzioni), la procedura consigliata consiste nel parametrizzare i valori. Il modo migliore per eseguire questa operazione consiste nel creare una sezione "parameters" all'inizio del modello di distribuzione, in cui è possibile specificare i nomi dei parametri e le proprietà, a cui si farà poi riferimento in seguito nel modello di distribuzione. Ogni definizione di parametro include *type*, *defaultValue* e una sezione *metadata* facoltativa con un valore *description*.

La sezione dei parametri è definita all'inizio del modello di distribuzione, subito prima della sezione *resources*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

Nel [modello mesh_rp.windows.json deployment](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) vengono dichiarati i parametri seguenti: location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  Le descrizioni per ogni parametro sono disponibili nel file del modello di distribuzione.

Questi parametri vengono usati nel file di [parametri mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  L'uso di un file di parametri separato offre la possibilità di modificare i valori dei parametri da una distribuzione a un'altra senza aggiornare il modello di distribuzione stesso.

### <a name="overview-of-the-application-and-services"></a>Panoramica dell'applicazione e dei servizi

I servizi sono specificati nel modello come proprietà della risorsa applicazione.  Le applicazioni vengono distribuite in una rete privata, dichiarata come risorsa nel modello.  I servizi possono usare i volumi per salvare i dati, dichiarati come risorse nel modello.  Per ogni servizio, il tipo del sistema operativo, i pacchetti di codice, il numero di repliche e la rete vengono specificati come proprietà del servizio.  Per ogni pacchetto di codice, specificare l'immagine del contenitore, gli endpoint, le risorse di memoria e CPU e le credenziali del repository di immagini. A livello generale, il modello per un'applicazione Service Fabric Mesh con più servizi è simile al seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 20001
                    }
                  ],
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
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Vedere il file del [modello di distribuzione mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) per le specifiche dell'applicazione To Do List.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Distribuire l'applicazione in Service Fabric Mesh
Creare l'applicazione e le risorse correlate usando il comando seguente e specificare le credenziali dal passaggio precedente [Recuperare le credenziali per il registro](#retrieve-credentials-for-the-registry).

Nel file di parametri aggiornare i valori dei parametri seguenti:

|Parametro|Valore|
|---|---|
|location|L'area in cui distribuire l'applicazione.  Ad esempio, "eastus".|
|registryPassword|La password ottenuta in precedenza in [Recuperare le credenziali per il registro](#retrieve-credentials-for-the-registry). Questo parametro nel modello è una stringa sicura e non verrà visualizzata nello stato di distribuzione o nei comandi `az mesh service show`.|
|registryUserName|Il nome utente ottenuto in [Recuperare le credenziali per il registro](#retrieve-credentials-for-the-registry).|
|registryServer|Il nome del server del registro ottenuto in [Recuperare le credenziali per il registro](#retrieve-credentials-for-the-registry).|
|frontEndImage|L'immagine del contenitore per il servizio front-end.  Ad esempio, "<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709".|
|serviceImage|L'immagine del contenitore per il servizio back-end.  Ad esempio, "<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709".|

Per distribuire l'applicazione, eseguire il comando seguente:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

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
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Aprire l'applicazione

Dopo la corretta distribuzione dell'applicazione, ottenere l'indirizzo IP pubblico per l'endpoint di servizio. Il comando di distribuzione restituisce l'indirizzo IP pubblico dell'endpoint servizio. Facoltativamente, è anche possibile eseguire una query nella risorsa di rete per trovare l'indirizzo IP pubblico dell'endpoint di servizio. Il nome della risorsa di rete di questa applicazione è `todolistappNetwork`, recuperare le relative informazioni con il comando seguente. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Passare all'indirizzo IP in un Web browser.

## <a name="check-application-status"></a>Controllare lo stato dell'applicazione

Per controllare lo stato dell'applicazione, è possibile usare il comando app show. Il nome dell'applicazione distribuita è "todolistapp", quindi recuperarne i dettagli.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Per esaminare i log dell'applicazione distribuita, usare il comando `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un registro contenitori privato
> * Effettuare il push dell'immagine del contenitore nel registro
> * Creare i file del modello e dei parametri
> * Distribuire un'applicazione in Service Fabric Mesh

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Ridimensionare un'applicazione in esecuzione in Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
