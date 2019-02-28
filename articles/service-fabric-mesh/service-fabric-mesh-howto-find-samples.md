---
title: Trovare esempi di Azure Service Fabric Mesh | Microsoft Docs
description: Come trovare diverse applicazioni di esempio di Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 12fbc0df8df56426679faef02b2b3fd285d391dc
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805189"
---
# <a name="find-service-fabric-mesh-samples"></a>Trovare esempi di Service Fabric Mesh

Questa tabella descrive le applicazioni di esempio di Service Fabric Mesh disponibili. Il codice sorgente di questi esempi mostra come ottenere un particolare scenario usando il modello di risorsa di Service Fabric.

Per altre informazioni sulla distribuzione dei modelli direttamente in Azure, vedere la [pagina dei modelli di esempio di GitHub](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).


|Modello di esempio|Descrizione dello scenario|Codice sorgente|Strumenti di sviluppo|
|------------|--------------------|----------|----------------------|
| [App Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Pagina Web statica ospitata in un contenitore. Per Linux usa nginx, per Windows IIS. | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Nessun requisito |
| [App contatore per volumi di file di Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Archiviazione dello stato tramite il montaggio del volume basato su File di Azure all'interno del contenitore. <br><br> **Nota:** Questo modello richiede che sia già stato effettuato il provisioning di una condivisione file di File di Azure [Istruzioni](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Strumenti di Visual Studio Mesh |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Creazione di un'applicazione con un servizio front-end e back-end che usa la risoluzione DNS. Usata come esercitazione [qui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore). | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Strumenti di Visual Studio Mesh |
| [App di oggetti visivi](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Ridimensionamento e aggiornamento di microservizi all'interno di un'applicazione. | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Strumenti di Visual Studio Mesh |
| [App di voto](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Creazione di un'applicazione con un servizio front-end e back-end che usa la risoluzione DNS. | [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Strumenti di Visual Studio Mesh per la versione di Windows, VS Code/interfaccia della riga di comando di .NET per la versione di Linux |
| [App contatore per Reliable Volumes di Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Archiviazione dello stato tramite il montaggio del volume basato su Reliable Disk di Service Fabric all'interno del contenitore.| [Codice sorgente](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Strumenti di Visual Studio Mesh |
