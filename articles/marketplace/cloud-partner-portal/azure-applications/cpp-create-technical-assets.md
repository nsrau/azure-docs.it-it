---
title: Creare gli asset tecnici dell'applicazione di Azure | Microsoft Docs
description: Creare gli asset tecnici per un'applicazione di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6050ad98c87dbe38516a6ee3c4862495ad868031
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414351"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Preparare gli asset tecnici dell'applicazione di Azure

Questo articolo descrive le risorse necessarie per preparare gli asset tecnici per l'applicazione di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Rivedere la documentazione seguente relativa all'applicazione di Azure, che include argomenti di avvio rapido, esercitazioni ed esempi.

- [Comprendere modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Argomenti di avvio rapido:

  - [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/)
  - [Modelli di avvio rapido di Azure in GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Pubblicare una definizione di applicazione](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Distribuire un'app del catalogo di servizi](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Esercitazioni:

  - [Creare file di definizioni](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Pubblicare un'applicazione del Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

 - Esempi:

   - [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
   - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
   - [Soluzioni di applicazioni gestite](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta.

Il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft seguenti:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
- Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conoscenza pratica di [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Strumenti suggeriti

Per la gestione dell'applicazione di Azure scegliere uno o entrambi gli ambienti di scripting seguenti:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

È consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:

  - Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È anche consigliabile esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/) e, se si usa Visual Studio, in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Passaggi successivi

[Creare un'offerta per un'applicazione di Azure](./cpp-create-offer.md)

