---
title: Preparare gli asset tecnici di Azure ContainerPrepare your Azure Container technical assets
description: Questo articolo descrive i passaggi e i requisiti per la configurazione di un'offerta di contenitori in Azure Marketplace.This article describes the steps and requirements for configuring a container offer on Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 62c77681accb19183b5e8aa3ae8d80c225b2d284
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266019"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Preparare gli asset tecnici di Azure ContainerPrepare your Azure Container technical assets

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di Azure Container dal portale per i partner cloud al Centro per i partner. Fino alla migrazione delle offerte, seguire le istruzioni in [Preparare le risorse tecniche del contenitore](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) per il portale Cloud Partner per gestire le offerte.

Questo articolo descrive i passaggi e i requisiti per la configurazione di un'offerta Contenitore in Azure Marketplace.This article describes the steps and requirements for configuring a Container offer on Azure Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Per le guide introduttive, le esercitazioni e gli esempi, vedere [Istanze del contenitore](https://docs.microsoft.com/azure/container-instances)di Azure.For Quickstarts, Tutorials, and Samples, see Azure Container Instances .

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la creazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sia della piattaforma Azure che delle tecnologie usate per creare l'offerta.

Oltre al dominio della soluzione, il team di progettazione deve disporre delle seguenti tecnologie Microsoft:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza delle macchine virtuali di [Azure,](https://azure.microsoft.com/services/virtual-machines/)Archiviazione di Azure e [rete di AzureWorking](https://azure.microsoft.com/services/?filter=networking) knowledge of Azure Virtual Machines , [Azure Storage,](https://azure.microsoft.com/services/?filter=storage)and Azure Networking
- Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conoscenza di lavoro di [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Strumenti suggeriti

Scegliere uno o entrambi gli ambienti di scripting seguenti per semplificare la gestione dell'immagine Contenitore:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [L'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

È consigliabile aggiungere questi strumenti all'ambiente di sviluppo:We recommend adding these tools to your development environment:

- [Esplora archivi Azure](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Esaminare gli strumenti disponibili nella pagina Strumenti di sviluppo di [Azure.Review](https://azure.microsoft.com/) the available tools on the Azure Developer Tools page. Se si utilizza Visual Studio, esaminare gli strumenti disponibili in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Creare l'immagine del contenitore

Per altre informazioni, vedere le esercitazioni seguenti:For more information, see the following tutorials:

- [Esercitazione: Creare un'immagine del contenitore per la distribuzione in istanze del contenitore di AzureTutorial: Create a container image for deployment to Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Esercitazione: Creare e distribuire immagini del contenitore nel cloud con le attività del Registro](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)di sistema contenitori di Azure.Tutorial: Build and deploy container images in the cloud with Azure Container Registry Tasks .

## <a name="next-step"></a>Passaggio successivo

- [Creare l'offerta Contenitore](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
