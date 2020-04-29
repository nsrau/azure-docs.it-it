---
title: Prerequisiti del modulo Azure IoT Edge | Azure Marketplace
description: Prerequisiti per la pubblicazione di un modulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142376"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prerequisiti per la pubblicazione di un modulo IoT Edge

>[!Important]
>A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del modulo IoT Edge al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare un'offerta di modulo IOT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) .

Questo articolo descrive i prerequisiti per la pubblicazione di un'offerta di modulo IoT Edge.  Se non è già stato fatto, consultare la [Guida alla pubblicazione dei moduli IOT Edge](../..//iot-edge-module.md).


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

Dopo aver [preparato l'asset tecnico del modulo IOT Edge](./cpp-create-technical-assets.md), sarà possibile [creare l'offerta del modulo di IOT Edge](./cpp-create-offer.md). 
