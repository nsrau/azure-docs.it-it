---
title: Preparare le risorse tecniche del contenitore di Azure
description: Risorse tecniche e linee guida per la configurazione di un'offerta di contenitori in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459332"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Preparare le risorse tecniche del contenitore di Azure

Questo articolo fornisce risorse tecniche e consigli per la creazione di un'offerta di contenitori in Azure Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Per Avvio rapido, esercitazioni ed esempi, vedere la [documentazione su Istanze di Azure Container](../container-instances/index.yml).

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

- [Azure PowerShell](/powershell/azure/)
- [Interfaccia della riga di comando di Azure](/cli/azure/).

È consigliabile aggiungere questi strumenti all'ambiente di sviluppo:

- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/). Se si usa Visual Studio, esaminare gli strumenti disponibili in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Creare l'immagine del contenitore

Non è possibile distribuire un'immagine in istanze di contenitore di Azure da un registro locale.

- Se si dispone già di un contenitore funzionante nel registro di sistema locale, creare un registro di sistema di Azure e caricare l'immagine del contenitore nel Container Registry di Azure. Per altre informazioni, vedere [esercitazione: compilare e distribuire immagini del contenitore nel cloud con le attività di container Registry di Azure](../container-registry/container-registry-tutorial-quick-task.md).

- Se non si ha ancora un'immagine del contenitore ed è necessario distribuire l'applicazione esistente o creare una nuova applicazione basata su contenitori, clonare il codice sorgente dell'applicazione da GitHub, creare un'immagine del contenitore dall'origine dell'applicazione e testare l'immagine in un ambiente Docker locale. Per altre informazioni, vedere [esercitazione: creare un'immagine del contenitore per la distribuzione in istanze di contenitore di Azure](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare l'offerta del contenitore](create-azure-container-offer.md)