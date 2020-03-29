---
title: Prerequisiti del modulo Azure IoT Edge Azure Marketplace
description: Prerequisiti per la pubblicazione di un modulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b6e021fc452b45edd7b1be9fd5afd77b792b4853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286541"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prerequisiti per la pubblicazione di un modulo IoT Edge

Questo articolo descrive i prerequisiti per la pubblicazione di un'offerta di modulo IoT Edge.  Se non è già stato fatto, consultare la guida alla pubblicazione dei [moduli IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Prerequisiti per la pubblicazione

Per pubblicare un modulo IoT Edge in Azure Marketplace, è necessario soddisfare i prerequisiti seguenti:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Accedere al [portale Cloud Partner (CPP)](https://cloudpartner.azure.com/). Per altre informazioni, vedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Contratto per le [Condizioni di Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Ospitare l'asset tecnico del modulo IoT Edge in un'istanza di Registro Azure Container.  Per altre informazioni, vedere [come preparare gli asset tecnici del modulo IoT Edge](./cpp-create-technical-assets.md)
- Preparare i metadati del modulo IoT Edge all'uso. Ad esempio, preparare le seguenti risorse:
    - Un titolo
    - Una descrizione (in formato HTML)
    - Un'immagine del logo (formato PNG e dimensioni delle immagini fisse, tra cui 40x40px, 90x90px, 115x115px, 255x115px)
    - Informativa sulla privacy e delle condizioni per l'utilizzo
    - Configurazione predefinita del modulo inclusiva di route, proprietà dei dispositivi gemelli desiderate, createOptions e variabili di ambiente.
    - Documentazione del modulo
    - Contatti del supporto tecnico


## <a name="next-steps"></a>Passaggi successivi

Una volta preparato il tuo asset tecnico del [modulo IoT Edge,](./cpp-create-technical-assets.md)sarai pronto per creare la tua offerta del [modulo IoT Edge.](./cpp-create-offer.md) 
