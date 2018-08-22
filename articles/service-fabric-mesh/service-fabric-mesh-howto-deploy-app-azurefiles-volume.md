---
title: Archiviazione dello stato in un'applicazione di Azure Service Fabric Mesh tramite il montaggio di un volume basato su File di Azure all'interno del contenitore | Microsoft Docs
description: Informazioni sull'archiviazione dello stato in un'applicazione di Azure Service Fabric Mesh tramite il montaggio di un volume basato su File di Azure all'interno del contenitore con l'interfaccia della riga di comando di Azure.
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038096"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Archiviazione dello stato in un'applicazione di Azure Service Fabric Mesh tramite il montaggio di un volume basato su File di Azure all'interno del contenitore

Questo articolo illustra come archiviare lo stato di File di Azure tramite il montaggio di un volume all'interno del contenitore di un'applicazione di Service Fabric Mesh. In questo esempio, l'applicazione Contatore ha un servizio ASP.NET Core con una pagina Web che mostra il valore del contatore in un browser. 

`counterService` legge periodicamente un valore del contatore da un file, lo incrementa e lo riscrive nel file. Il file è archiviato in una cartella che è montata sul volume basato sulla condivisione File di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa attività è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per usare l'interfaccia della riga di comando di Azure con questo articolo, assicurarsi che `az --version` restituisca almeno `azure-cli (2.0.43)`.  Per installare (o aggiornare) il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure e impostare la sottoscrizione.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Creare una condivisione file

Creare una condivisione dei file di Azure seguendo queste [istruzioni](/azure/storage/files/storage-how-to-create-file-share). Il nome dell'account di archiviazione, la chiave dell'account di archiviazione e il nome della condivisione file vengono indicati come `<storageAccountName>`, `<storageAccountKey>` e `<fileShareName>` nelle istruzioni seguenti. Questi valori sono disponibili nel portale di Azure:
* <storageAccountName> - In **Account di archiviazione** si trova il nome dell'account di archiviazione usato durante la creazione della condivisione file.
* <storageAccountKey> - Selezionare il proprio account di archiviazione in **Account di archiviazione**, quindi selezionare **Chiavi di accesso** e usare il valore in **key1**.
* <fileShareName> - Selezionare il proprio account di archiviazione in **Account di archiviazione** e quindi selezionare **File**. Il nome da usare è il nome della condivisione file appena creata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse in cui distribuire l'applicazione. Il comando seguente crea un gruppo di risorse denominato `myResourceGroup` in una località negli Stati Uniti orientali.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Distribuire il modello

Creare l'applicazione e le risorse correlate usando il comando seguente e specificare i valori per `storageAccountName`, `storageAccountKey` e `fileShareName` dal passaggio precedente [Creare una condivisione file](#create-a-file-share).

Il parametro `storageAccountKey` nel modello è una stringa sicura. Non verrà visualizzato nello stato della distribuzione e nei comandi `az mesh service show`. Assicurarsi che sia specificato correttamente nel comando seguente.

Il comando seguente distribuisce un'applicazione Linux usando il [modello mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Per distribuire un'applicazione Windows, usare il [modello mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Tenere presente che la distribuzione di immagini del contenitore di grandi dimensioni può richiedere più tempo.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Entro pochi minuti il comando dovrebbe restituire `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Aprire l'applicazione

Il comando di distribuzione restituisce l'indirizzo IP pubblico dell'endpoint di servizio. Dopo la corretta distribuzione dell'applicazione, ottenere l'indirizzo IP pubblico relativo all'endpoint di servizio e aprirlo in un browser. Viene visualizzata una pagina Web con il valore del contatore aggiornato ogni secondo.

Il nome della risorsa di rete per questa applicazione è `counterAppNetwork`. È possibile visualizzare informazioni sull'app, ad esempio descrizione, posizione, gruppo di risorse e così via, usando il comando seguente:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verificare che l'applicazione sia in grado di usare il volume

L'applicazione crea un file denominato `counter.txt` nella condivisione dei file all'interno della cartella `counter/counterService`. Il contenuto di questo file è il valore del contatore visualizzato nella pagina Web.

Il file può essere scaricato usando qualsiasi strumento che consenta l'esplorazione di una condivisione file di File di Azure, ad esempio [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Eliminare le risorse

Eliminare con regolarità le risorse che non si usano più in Azure. Per eliminare le risorse correlate a questo esempio, eliminare il gruppo di risorse in cui sono state distribuite con il comando seguente. Questa operazione consente di eliminare tutti gli elementi associati al gruppo di risorse.

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'applicazione di esempio sul volume di File di Azure in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Per altre informazioni sul modello di risorse di Service Fabric, vedere il [Modello di risorsa di Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Per altre informazioni su Service Fabric Mesh, leggere la [panoramica di Service Fabric Mesh](service-fabric-mesh-overview.md).