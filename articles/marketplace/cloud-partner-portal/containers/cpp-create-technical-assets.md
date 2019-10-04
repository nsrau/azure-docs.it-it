---
title: Creare immagini di contenitori di Azure gli asset tecnici | Azure Marketplace
description: Creare gli asset tecnici per un contenitore di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794333"
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
-   [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

È anche consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo in uso:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È anche consigliabile esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/) e, se si usa Visual Studio, in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Creare l'immagine del contenitore

Per altre informazioni, vedere quanto segue:

* [Esercitazione: Creare un'immagine del contenitore per la distribuzione in istanze di contenitore di Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Esercitazione: Compilare e distribuire immagini del contenitore nel cloud con le attività del Registro di sistema di contenitore di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Passaggi successivi

[Creare l'offerta per il contenitore](./cpp-create-offer.md)
