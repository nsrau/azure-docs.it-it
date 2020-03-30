---
title: Creare asset tecnici per le immagini di Azure Containers Azure Marketplace
description: Creare gli asset tecnici per un contenitore di Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280066"
---
# <a name="prepare-your-container-technical-assets"></a>Preparare gli asset tecnici del contenitore

Questo articolo descrive i passaggi e i requisiti per configurare un'offerta per un contenitore in Azure Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Rivedere la documentazione su [Istanze di Azure Container](https://docs.microsoft.com/azure/container-instances) che include guide introduttive, esercitazioni ed esempi.

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta.
 
Oltre alle conoscenze relative al dominio della soluzione, il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft indicato di seguito:

-   Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/) 
-   Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
-   Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conoscenza pratica di [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Strumenti suggeriti

Per la gestione dell'immagine del contenitore scegliere uno o entrambi gli ambienti di script seguenti:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure)

È anche consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo in uso:

-   [Esplora archivi di AzureAzure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È anche consigliabile esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/) e, se si usa Visual Studio, in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Creare l'immagine del contenitore

Per altre informazioni, vedere quanto segue:

* [Esercitazione: Creare un'immagine del contenitore per la distribuzione in istanze del contenitore di AzureTutorial: Create a container image for deployment to Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Esercitazione: Creare e distribuire immagini del contenitore nel cloud con le attività del Registro di sistema contenitori di AzureTutorial: Build and deploy container images in the cloud with Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Passaggi successivi

[Creare l'offerta per il contenitore](./cpp-create-offer.md)
