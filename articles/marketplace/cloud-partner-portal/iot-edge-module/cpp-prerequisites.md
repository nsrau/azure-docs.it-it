---
title: Prerequisiti del modulo Azure IoT Edge Azure Marketplace
description: Prerequisiti per la pubblicazione di un modulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 93e0418004ea65f93ad436eec1ce523424a194ba
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744976"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prerequisiti per la pubblicazione di un modulo IoT Edge

>[!Important]
>A partire dal 30 marzo 2020, inizieremo a spostare la gestione delle offerte del modulo IoT Edge al Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare un'offerta del modulo IoT Edge](https://aka.ms/AzureCreateIoT) per gestire le offerte migrate.

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
