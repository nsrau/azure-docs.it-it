---
title: Creare un asset tecnico per macchine virtuali di Azure
description: Questo articolo descrive i passaggi e i requisiti per la configurazione di un'offerta di contenitore in Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730631"
---
# <a name="create-an-azure-vm-technical-asset"></a>Creare un asset tecnico per macchine virtuali di Azure

> [!IMPORTANT]
> La gestione delle offerte del contenitore di Azure viene trasferita dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni riportate in [preparare le risorse tecniche del contenitore](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) per portale cloud partner per gestire le offerte.

Questo articolo descrive i passaggi e i requisiti per la configurazione di un'offerta di contenitore in Azure Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Per le guide introduttive, le esercitazioni e gli esempi, vedere [istanze di contenitore di Azure](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sulla piattaforma Azure e sulle tecnologie usate per creare l'offerta.

Oltre al dominio della soluzione, il team di progettazione deve conoscere le tecnologie Microsoft seguenti:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza del funzionamento di [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage)e rete di [Azure](https://azure.microsoft.com/services/?filter=networking)
- Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conoscenza dell'utilizzo di [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Strumenti suggeriti

Scegliere uno o entrambi gli ambienti di scripting seguenti per semplificare la gestione dell'immagine del contenitore:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)della riga di comando di Azure.

È consigliabile aggiungere questi strumenti all'ambiente di sviluppo:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Estensione: [EDULCORARE JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Esaminare gli strumenti disponibili nella pagina [strumenti di sviluppo di Azure](https://azure.microsoft.com/) . Se si usa Visual Studio, esaminare gli strumenti disponibili nella [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Creare l'immagine del contenitore

Per ulteriori informazioni, vedere le esercitazioni seguenti:

- [Esercitazione: creare un'immagine del contenitore per la distribuzione in istanze di contenitore di Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Esercitazione: creare e distribuire immagini del contenitore nel cloud con le attività di container Registry di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-step"></a>Passaggio successivo

- [Creare l'offerta del contenitore](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
