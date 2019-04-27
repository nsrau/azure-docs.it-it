---
title: Creare gli asset tecnici dell'immagine dei contenitori di Azure | Microsoft Docs
description: Creare gli asset tecnici per un contenitore di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 5a7531be73a872d9c088a0bf02a8686f947c220a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472941"
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

- Creare e configurare il disco rigido virtuale (VHD) per la macchina virtuale contenitore. Questo disco rigido virtuale contiene il sistema operativo per il contenitore che può essere Windows, Linux o Ubuntu. Potrebbero essere necessari altri dischi dati.
- Configurare il sistema operativo della macchina virtuale, la dimensione di questa, le porte da aprire e i dischi dati collegati.
- Installare l'applicazione e l'altro software necessario per l'offerta. Ad esempio software di database, software di terze parti o un'applicazione personalizzata.

## <a name="next-steps"></a>Passaggi successivi

[Creare l'offerta per il contenitore](./cpp-create-offer.md)
