---
title: Prerequisiti del modulo Azure IoT Edge | Azure Marketplace
description: Prerequisiti per la pubblicazione di un modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a5d1d6fdaf07f8b27820021d4d2ac45ec67c9915
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942124"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prerequisiti per la pubblicazione di un modulo IoT Edge

Questo articolo descrive i prerequisiti per la pubblicazione di un'offerta di modulo IoT Edge.  Se non è già stato fatto, consultare il [Guida alla pubblicazione i moduli di IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Prerequisiti per la pubblicazione

Per pubblicare un modulo IoT Edge in Azure Marketplace, è necessario soddisfare i prerequisiti seguenti:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Accedere al [portale Cloud Partner (CPP)](https://cloudpartner.azure.com/). Per altre informazioni, vedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Contratto per le [Condizioni di Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Ospitare l'asset tecnico del modulo IoT Edge in un'istanza di Registro Azure Container.  Per altre informazioni, vedere [come preparare gli asset tecnici del modulo IoT Edge](./cpp-create-technical-assets.md)
- Preparare i metadati del modulo IoT Edge all'uso. Ad esempio, preparare le risorse seguenti:
    - Un titolo
    - Una descrizione (in formato HTML)
    - Un'immagine del logo (formato PNG e dimensioni delle immagini fisse, tra cui 40x40px, 90x90px, 115x115px, 255x115px)
    - Informativa sulla privacy e delle condizioni per l'utilizzo
    - Configurazione predefinita del modulo inclusiva di route, proprietà dei dispositivi gemelli desiderate, createOptions e variabili di ambiente.
    - Documentazione del modulo
    - Contatti del supporto tecnico


## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato [preparato l'asset tecnico di IoT Edge module](./cpp-create-technical-assets.md), sarà possibile [creare l'offerta di modulo di IoT Edge](./cpp-create-offer.md). 
