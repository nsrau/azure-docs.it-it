---
title: Panoramica della pubblicazione dell'offerta di modulo Azure IoT Edge | Microsoft Docs
description: Panoramica del processo per la pubblicazione di un'offerta di modulo IoT Edge in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 68c58c16d7083182d412adb6ed0d243a3291cc42
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432827"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Panoramica della pubblicazione dell'offerta di modulo IoT Edge

Questo articolo descrive il processo di pubblicazione e le parti fondamentali di un'offerta di modulo IoT Edge. Usare questo articolo come punto di partenza per la pubblicazione dell'offerta in [Azure Marketplace](https://azuremarketplace.microsoft.com).

## <a name="publishing-process"></a>Processo di pubblicazione

I passaggi generali per la pubblicazione di un'offerta di modulo IoT Edge sono:

1. Creazione dell'offerta<br> Specificare le informazioni dettagliate sull'offerta. Queste informazioni includono la descrizione dell'offerta, il materiale di marketing, le informazioni di supporto e le specifiche degli asset.

2. Creazione degli asset tecnici e aziendali<br> Creare gli asset aziendali (documenti legali e materiali di marketing) e gli asset tecnici per la soluzione associata (immagini del modulo IoT Edge ospitate in un Registro contenitori di Azure).

3. Creazione del codice di riferimento del prodotto (SKU)<br> Creare gli SKU associati all'offerta. È necessario uno SKU per ogni immagine che si intende pubblicare.

4. Certificazione e pubblicazione dell'offerta <br>Dopo aver completato l'offerta e le risorse tecniche, è possibile procedere all'invio dell'offerta. L'invio dell'offerta avvia il processo di pubblicazione. Durante questo processo la soluzione viene sottoposta a test, viene convalidata e certificata, quindi viene "aggiunta" in Azure Marketplace.

## <a name="parts-of-an-offer"></a>Parti di un'offerta

Gli articoli seguenti illustrano le parti fondamentali di un'offerta di modulo IoT Edge.

- [Prerequisiti](./cpp-prerequisites.md) <br>Questo articolo elenca i requisiti aziendali e tecnici prima che sia possibile creare o pubblicare un'offerta di modulo IoT Edge.
- [Preparare gli asset tecnici del modulo IoT Edge](./cpp-create-technical-assets.md) <br>Questo articolo descrive come preparare gli asset tecnici per un modulo IoT Edge. Questi asset devono soddisfare tutti i criteri tecnici necessari prima che il modulo IoT Edge possa essere pubblicato in Azure Marketplace.
- [Creare un'offerta di modulo di IoT Edge](./cpp-create-offer.md) <br>Questo articolo elenca i passaggi necessari per creare una nuova voce di offerta di modulo IoT Edge usando il [portale Cloud Partner](https://cloudpartner.azure.com).
- [Pubblicare l'offerta di modulo IoT Edge](./cpp-publish-offer.md)<br> Questo articolo descrive come inviare l'offerta per la pubblicazione in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

Rivedere i [requisiti tecnici e aziendali](./cpp-prerequisites.md) per la pubblicazione di un modulo IoT Edge in Microsoft Azure Marketplace.
