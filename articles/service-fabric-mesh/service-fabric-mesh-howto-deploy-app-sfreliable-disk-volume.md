---
title: Service Fabric volume del disco affidabile con Service Fabric mesh
description: Informazioni sull'archiviazione dello stato in un'applicazione Azure Service Fabric Mesh con il montaggio di un volume basato su Reliable Disk di Service Fabric all'interno del contenitore usando l'interfaccia della riga di comando di Azure.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 79ac8e7868b04a63637e24d6dde651b218ce6a46
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489191"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Montare il volume basato su Reliable Disk di Service Fabric a disponibilità elevata in un'applicazione Service Fabric Mesh 
Il metodo comune per rendere persistente lo stato con le app contenitore è usare una risorsa di archiviazione remota, ad esempio Archiviazione file di Azure o un database come Azure Cosmos DB. Ciò comporta un notevole latenza di rete per la lettura e la scrittura nell'archivio remoto.

Questo articolo illustra come archiviare lo stato in Reliable Disk di Service Fabric a disponibilità elevata montando un volume all'interno del contenitore di un'applicazione Service Fabric Mesh.
Reliable Disk di Service Fabric rende disponibili volumi per le letture locali con scritture replicate all'interno del cluster di Service Fabric per ottimizzare la disponibilità. In questo modo si rimuovono le chiamate di rete per le letture e si riduce la latenza di rete per le scritture. Se il contenitore viene riavviato o spostato in un altro nodo, la nuova istanza di contenitore vedrà lo stesso volume di quella precedente. Questo assicura maggiore efficienza e un'elevata disponibilità.

In questo esempio, l'applicazione Contatore ha un servizio ASP.NET Core con una pagina Web che mostra il valore del contatore in un browser.

`counterService` legge periodicamente un valore del contatore da un file, lo incrementa e lo riscrive nel file. Il file è archiviato in una cartella montata sul volume basato sul Reliable Disk di Service Fabric.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa attività è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Per usare l'interfaccia della riga di comando di Azure con questo articolo, assicurarsi che `az --version` restituisca almeno `azure-cli (2.0.43)`.  Per installare (o aggiornare) il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure e impostare la sottoscrizione.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse in cui distribuire l'applicazione. Il comando seguente crea un gruppo di risorse denominato `myResourceGroup` in una località negli Stati Uniti orientali. Se si modifica il nome del gruppo di risorse nel comando seguente, ricordarsi di modificarlo in tutti i comandi successivi.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Distribuire il modello

>[!NOTE]
> A partire dal 2 novembre 2020, i [limiti della frequenza di download si applicano](https://docs.docker.com/docker-hub/download-rate-limit/) a richieste anonime e autenticate inviate a Docker Hub da account con il piano Docker Free vengono imposti in base all'indirizzo IP. 
> 
> Questo modello usa immagini pubbliche dall'hub docker. Si noti che è possibile che la frequenza sia limitata. Per altre informazioni, vedere [Eseguire l'autenticazione con Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Il comando seguente distribuisce un'applicazione Linux usando il [modello counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Per distribuire un'applicazione Windows, usare il [modello counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Tenere presente che la distribuzione di immagini del contenitore di grandi dimensioni può richiedere più tempo.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

È anche possibile visualizzare lo stato della distribuzione con il comando

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Si noti il nome della risorsa del gateway con tipo di risorsa `Microsoft.ServiceFabricMesh/gateways`: verrà usato per ottenere l'indirizzo IP pubblico dell'app.

## <a name="open-the-application"></a>Aprire l'applicazione

Dopo aver distribuito correttamente l'applicazione, ottenere l'indirizzo IP della risorsa del gateway per l'app. Usare il nome del gateway evidenziato nelle sezione precedente.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

L'output deve avere una proprietà `ipAddress` che è l'indirizzo IP pubblico per l'endpoint di servizio. Aprirlo da un browser. Viene visualizzata una pagina Web con il valore del contatore aggiornato ogni secondo.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verificare che l'applicazione sia in grado di usare il volume

L'applicazione crea un file denominato `counter.txt` nel volume all'interno della cartella `counter/counterService`. Il contenuto di questo file è il valore del contatore visualizzato nella pagina Web.

## <a name="delete-the-resources"></a>Eliminare le risorse

Eliminare con regolarità le risorse che non si usano più in Azure. Per eliminare le risorse correlate a questo esempio, eliminare il gruppo di risorse in cui sono state distribuite con il comando seguente. Questa operazione consente di eliminare tutti gli elementi associati al gruppo di risorse:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare l'applicazione di esempio Reliable Volume Disk di Service Fabric su [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Per altre informazioni sul modello di risorse di Service Fabric, vedere [Service Fabric Mesh Resource Model](service-fabric-mesh-service-fabric-resources.md) (Modello di risorsa mesh Service Fabric).
- Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).