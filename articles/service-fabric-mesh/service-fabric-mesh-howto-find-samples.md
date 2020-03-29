---
title: Trovare esempi di Azure Service Fabric MeshFind Azure Service Fabric Mesh samples
description: Ecco un indice delle applicazioni di esempio Service Fabric Mesh disponibili. Il codice sorgente di questi esempi mostra come ottenere un particolare scenario usando il modello di risorsa di Service Fabric.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461900"
---
# <a name="find-service-fabric-mesh-samples"></a>Trovare esempi di Service Fabric Mesh

Questa tabella descrive le applicazioni di esempio di Service Fabric Mesh disponibili. Il codice sorgente di questi esempi mostra come ottenere un particolare scenario usando il modello di risorsa di Service Fabric.

Per altre informazioni sulla distribuzione dei modelli direttamente in Azure, vedere la [pagina dei modelli di esempio di GitHub](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).

|Modello di esempio|Descrizione dello scenario|Codice sorgente|Strumenti di sviluppo|
|------------|--------------------|----------|----------------------|
| [App Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Pagina Web statica ospitata in un contenitore. Per Linux usa nginx, per Windows IIS. | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Nessun requisito |
| [App contatore per volumi di file di Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Archiviazione dello stato tramite il montaggio del volume basato su File di Azure all'interno del contenitore. <br><br> **Nota:** questo modello richiede che sia già stato effettuato il provisioning di una condivisione file di File di Azure [Istruzioni](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Strumenti di Visual Studio Mesh |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Creazione di un'applicazione con un servizio front-end e back-end che usa la risoluzione DNS. Usata come esercitazione [qui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore). | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Strumenti di Visual Studio Mesh |
| [App di oggetti visivi](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Ridimensionamento e aggiornamento di microservizi all'interno di un'applicazione. | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Strumenti di Visual Studio Mesh |
| [App per il voto](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Creazione di un'applicazione con un servizio front-end e back-end che usa la risoluzione DNS. | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio Mesh Tooling per la versione Windows, VS Code / dotnet CLI può essere utilizzato per la versione Linux |
| [App contatore per Reliable Volumes di Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Archiviazione dello stato tramite il montaggio del volume basato su Reliable Disk di Service Fabric all'interno del contenitore.| [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Strumenti di Visual Studio Mesh |
