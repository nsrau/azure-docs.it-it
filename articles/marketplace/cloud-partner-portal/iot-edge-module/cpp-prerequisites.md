---
title: Prerequisiti del modulo Azure IoT Edge | Microsoft Docs
description: Prerequisiti per la pubblicazione di un modulo IoT Edge.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432923"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prerequisiti per la pubblicazione di un modulo IoT Edge

Questo articolo descrive i prerequisiti per la pubblicazione di un'offerta di modulo IoT Edge.

Per altre informazioni sui moduli IoT Edge e sui vantaggi della pubblicazione di un modulo in Azure Marketplace, vedere la [Guida alla pubblicazione di moduli IoT Edge](https://docs.microsoft.com/azure/marketplace/iot-edge-module).

## <a name="publishing-prerequisites"></a>Prerequisiti per la pubblicazione

Per pubblicare un modulo IoT Edge in Azure Marketplace, è necessario soddisfare i prerequisiti seguenti:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Accedere al [portale Cloud Partner (CPP)](https://cloudpartner.azure.com/). Per altre informazioni, vedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Contratto per le [Condizioni di Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Ospitare l'asset tecnico del modulo IoT Edge in un Registro contenitori di Azure.  Per altre informazioni, vedere [come preparare gli asset tecnici del modulo IoT Edge](./cpp-create-technical-assets.md)
- Preparare i metadati del modulo IoT Edge all'uso. Ad esempio (elenco non completo):
    - Un titolo
    - Una descrizione (in formato HTML)
    - Un'immagine del logo (formato PNG e dimensioni delle immagini fisse, tra cui 40x40px, 90x90px, 115x115px, 255x115px)
    - Informativa sulla privacy e delle condizioni per l'utilizzo
    - Configurazione predefinita del modulo inclusiva di route, proprietà dei dispositivi gemelli desiderate, createOptions e variabili di ambiente.
    - Documentazione
    - Contatti del supporto tecnico

## <a name="next-steps"></a>Passaggi successivi

- [Preparare gli asset tecnici del modulo IoT Edge](./cpp-create-technical-assets.md)
- [Create your IoT Edge module offer](./cpp-create-offer.md) (Creare un'offerta di modulo IoT Edge)
