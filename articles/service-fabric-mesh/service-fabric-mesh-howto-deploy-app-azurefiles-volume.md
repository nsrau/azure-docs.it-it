---
title: Archiviazione dello stato tramite il montaggio del volume basato su File di Azure all'interno del contenitore nell'applicazione di Service Fabric Mesh | Microsoft Docs
description: Informazioni sull'archiviazione dello stato tramite il montaggio del volume basato su File di Azure all'interno del contenitore nell'applicazione di Service Fabric Mesh tramite l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090633"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Archiviazione dello stato tramite il montaggio del volume basato su File di Azure nell'applicazione di Service Fabric Mesh

Questo articolo illustra come archiviare lo stato di File di Azure tramite il montaggio del volume all'interno del contenitore dell'applicazione di Service Fabric Mesh. In questo esempio, l'applicazione Contatore ha un servizio ASP.NET Core con una pagina Web che mostra il valore del contatore in un browser. 

`counterService` legge periodicamente un valore del contatore da un file, lo incrementa e lo riscrive nel file. Il file è archiviato in una cartella che è montata sul volume basato sulla condivisione File di Azure. 

## <a name="set-up-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di mesh Service Fabric 
Per completare questa attività è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per installare il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere ad Azure e impostare la sottoscrizione.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Creare la condivisione file 
Creare una condivisione dei file di Azure seguendo queste [istruzioni](/azure/storage/files/storage-how-to-create-file-share). Il nome dell'account di archiviazione, la chiave dell'account di archiviazione e il nome della condivisione file vengono indicati come `<storageAccountName>`, `<storageAccountKey>`, e `<fileShareName>` nelle istruzioni seguenti.

## <a name="create-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse in cui distribuire l'applicazione. È possibile usare un gruppo di risorse esistente e ignorare questo passaggio. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Distribuire il modello

Creare l'applicazione e le risorse correlate usando il comando seguente e specificare i valori per `storageAccountName`, `storageAccountKey` e `fileShareName` dal passaggio precedente.

Il parametro `storageAccountKey` nel modello è `securestring`. Non verrà visualizzato nello stato della distribuzione e nei comandi `az mesh service show`. Assicurarsi che sia specificato correttamente nel comando seguente.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Il comando precedente consente di distribuire un'applicazione Linux usando un [modello mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Se si vuole distribuire un'applicazione Windows, usare il [modello mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Le dimensioni delle immagini dei contenitori Windows sono maggiori di quelle delle immagini dei contenitori Linux, di conseguenza la distribuzione potrebbe richiedere più tempo.

In pochi minuti, il comando dovrebbe restituire:

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Aprire l'applicazione
Dopo la corretta distribuzione dell'applicazione, ottenere l'indirizzo IP pubblico relativo all'endpoint servizio e aprirlo in un browser. Viene visualizzata una pagina Web con il valore del contatore aggiornato ogni secondo.

Il comando di distribuzione restituisce l'indirizzo IP pubblico dell'endpoint servizio. Facoltativamente, è inoltre possibile eseguire una query nella risorsa di rete per trovare l'indirizzo IP pubblico dell'endpoint servizio. 
 
Il nome della risorsa di rete di questa applicazione è `counterAppNetwork`, recuperare le relative informazioni con il comando seguente. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verificare che l'applicazione sia in grado di usare il volume
L'applicazione crea un file denominato `counter.txt` nella condivisione dei file all'interno della cartella `counter/counterService`. Il contenuto di questo file è il valore del contatore visualizzato nella pagina Web.

Il file può essere scaricato usando qualsiasi strumento che consenta l'esplorazione di una condivisione di file di File di Azure. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un esempio di tale strumento.

## <a name="delete-the-resources"></a>Eliminare le risorse

Per conservare le risorse limitate assegnate al programma di anteprima, eliminare le risorse di frequente. Per eliminare le risorse correlate a questo esempio, eliminare il gruppo di risorse in cui sono state distribuite.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'applicazione di esempio sul volume di File di Azure in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Per altre informazioni sul modello di risorse di Service Fabric, vedere il [Modello di risorsa di Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Per altre informazioni su Service Fabric Mesh, leggere la [panoramica su Service Fabric Mesh](service-fabric-mesh-overview.md).
