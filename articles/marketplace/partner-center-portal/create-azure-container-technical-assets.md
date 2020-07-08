---
title: Concetti tecnici per le offerte di contenitori di Azure - Marketplace commerciale di Microsoft
description: Risorse tecniche e linee guida per la configurazione di un'offerta di contenitori in Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 8f32313529da965573a6c9884daee678e3bc64cc
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958600"
---
# <a name="create-an-azure-container-offer"></a>Creare un'offerta per il contenitore di Azure

Questo articolo fornisce risorse tecniche e consigli per la creazione di un'offerta di contenitori in Azure Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Per Avvio rapido, esercitazioni ed esempi, vedere la [documentazione su Istanze di Azure Container](../../container-instances/index.yml).

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta.

Oltre alle conoscenze relative al dominio della soluzione, il team tecnico deve conoscere le seguenti tecnologie Microsoft:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
- Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conoscenza pratica di [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Strumenti suggeriti

Per la gestione dell'immagine del contenitore scegliere uno o entrambi gli ambienti di script seguenti:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

È consigliabile aggiungere questi strumenti all'ambiente di sviluppo:

- [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/). Se si usa Visual Studio, esaminare gli strumenti disponibili in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Creare l'immagine del contenitore

Per altre informazioni, vedere le esercitazioni seguenti:

- [Esercitazione: Creare un'immagine del contenitore per la distribuzione in Istanze di Azure Container](../../container-instances/container-instances-tutorial-prepare-app.md)
- [Esercitazione: Compilare e distribuire immagini del contenitore nel cloud con Attività del Registro Azure Container](../../container-registry/container-registry-tutorial-quick-task.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare l'offerta per il contenitore](create-azure-container-offer.md).
