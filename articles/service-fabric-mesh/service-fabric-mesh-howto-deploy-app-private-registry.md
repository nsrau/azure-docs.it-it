---
title: Distribuire applicazioni da un registro privato in Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su come distribuire un'app che usa un registro contenitori privato di mesh Service Fabric con l'interfaccia della riga di comando di Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089487"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Distribuire un'applicazione di mesh Service Fabric da un registro privato di immagini del contenitore

Questo articolo illustra come distribuire un'applicazione Azure Service Fabric Mesh che usa un registro privato di immagini del contenitore.

## <a name="prerequisites"></a>Prerequisiti

### <a name="set-up-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di mesh Service Fabric 
Per completare questa attività è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>Installare Docker

Installare Docker per supportare le applicazioni di Service Fabric in contenitori usate da mesh Service Fabric.

### <a name="windows-10"></a>Windows 10

Scaricare e installare la versione più recente di [Docker Community Edition per Windows][download-docker]. 

Durante l'installazione, selezionare **Use Windows containers instead of Linux containers** (Usa contenitori Windows invece di contenitori Linux) quando viene richiesto. Sarà necessario disconnettersi e accedere nuovamente. Dopo il nuovo accesso, potrebbe venire richiesto di abilitare Hyper-V se non è stato precedentemente abilitato. Abilitare Hyper-V e quindi riavviare il computer.

Dopo il riavvio del computer, Docker richiederà di abilitare la funzionalità **Contenitori**; abilitarla e riavviare il computer.

### <a name="windows-server-2016"></a>Windows Server 2016

Usare i comandi di PowerShell seguenti per installare Docker. Per altre informazioni, vedere[Docker Enterprise Edition for Windows Server][download-docker-server] (Docker Enterprise Edition per Windows Server).

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Riavviare il computer.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure e impostare la sottoscrizione attiva:

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Creare un registro contenitori ed eseguire il push di un'immagine in esso

Creare un Registro contenitori di Azure seguendo le istruzioni riportate in [Creare un registro contenitori con l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md). Eseguire i passaggi fino ad [Accedere al record di controllo di accesso](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr). 

### <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se non sono ancora disponibili immagini del contenitore locale, eseguire il comando seguente per eseguire il pull di un'immagine esistente da Hub Docker.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Prima di poter eseguire il push di un'immagine nel registro, è necessario contrassegnarla con il nome completo del server di accesso del record di controllo di accesso. Eseguire il comando seguente per ottenere il nome completo del server di accesso dell'istanza del record di controllo di accesso.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Contrassegnare l'immagine usando il comando [docker tag][docker-tag]. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Elencare le immagini del contenitore

L'esempio seguente elenca i repository in un registro:

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```bash
Result
----------------
azure-mesh-helloworld
```

L'esempio seguente elenca i tag nel repository **azure-mesh-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Output:

```bash
Result
--------
1.1-alpine
```
L'output precedente conferma la presenza di `azure-mesh-helloworld:1.1-alpine` nel registro contenitori privato.

## <a name="retrieve-credentials-for-the-registry"></a>Recuperare le credenziali per il registro

Per distribuire un'istanza del contenitore dal registro creato, è necessario specificare le credenziali durante la distribuzione. Abilitare l'utente amministratore nel registro con il seguente comando:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Ottenere il nome del server del registro, il nome utente e la password, usando i comandi seguenti:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Viene fatto riferimento ai valori forniti dai comandi precedenti come a `<acrLoginServer>`, `<acrUserName>` e `<acrPassword>` nel comando seguente.


## <a name="deploy-the-template"></a>Distribuire il modello

Creare l'applicazione e le risorse correlate usando il comando seguente e specificare le credenziali dal passaggio precedente.

Il parametro `registry-password` nel modello è `securestring`. Non verrà visualizzato nello stato della distribuzione e nei comandi `az mesh service show`. Assicurarsi che sia specificato correttamente nel comando seguente.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

In pochi minuti, il comando dovrebbe restituire:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Aprire l'applicazione
Dopo la corretta distribuzione dell'applicazione, ottenere l'indirizzo IP pubblico relativo all'endpoint servizio e aprirlo in un browser. Viene visualizzata una pagina Web con il logo di mesh Service Fabric.

Il comando di distribuzione restituisce l'indirizzo IP pubblico dell'endpoint servizio. Facoltativamente, è inoltre possibile eseguire una query nella risorsa di rete per trovare l'indirizzo IP pubblico dell'endpoint servizio. 
 
Il nome della risorsa di rete di questa applicazione è `helloWorldPrivateRegistryNetwork`, recuperare le relative informazioni con il comando seguente. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Eliminare le risorse

Per conservare le risorse limitate assegnate al programma di anteprima, eliminare le risorse di frequente. Per eliminare le risorse correlate a questo esempio, eliminare il gruppo di risorse in cui sono state distribuite.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare l'applicazione di esempio Hello World in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Per altre informazioni sul modello di risorse di Service Fabric, vedere [Service Fabric Mesh Resource Model](service-fabric-mesh-service-fabric-resources.md) (Modello di risorsa mesh Service Fabric).
- Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/