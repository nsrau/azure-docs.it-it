---
title: Panoramica della pubblicazione dell'offerta del modulo Azure IoT Edge | Azure Marketplace
description: Panoramica del processo per la pubblicazione di un'offerta di modulo IoT Edge in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e9116e5cdb3bd9ed61205ceabd4d51c96c6aadc2
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144660"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Panoramica della pubblicazione dell'offerta di modulo IoT Edge

>[!Important]
>A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del modulo IoT Edge al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare un'offerta di modulo IOT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) .

<table> <tr> <td>Questa sezione illustra come pubblicare una nuova offerta del modulo Azure IoT Edge per <a href="https://azuremarketplace.microsoft.com">Microsoft Azure Marketplace</a>. Un modulo IoT Edge è un contenitore compatibile con Docker che viene eseguito in un dispositivo IoT Edge. I moduli Azure IoT Edge sono l'unità di calcolo più piccola gestita da IoT Edge e possono contenere i servizi di Azure o il codice personalizzato per la soluzione. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Processo di pubblicazione

I passaggi generali per la pubblicazione di un'offerta di modulo IoT Edge sono:

1. Creazione dell'offerta<br> Specificare le informazioni dettagliate sull'offerta. Queste informazioni includono la descrizione dell'offerta, il materiale di marketing, le informazioni di supporto e le specifiche degli asset.

2. Creazione degli asset tecnici e aziendali<br> Creare gli asset aziendali (documenti legali e materiali di marketing) e gli asset tecnici per la soluzione associata (immagini del modulo IoT Edge ospitate in un'istanza di Registro Azure Container).

3. Creazione del codice di riferimento del prodotto (SKU)<br> Creare gli SKU associati all'offerta. È necessario uno SKU per ogni immagine che si intende pubblicare.

4. Certificazione e pubblicazione dell'offerta <br>Dopo aver completato l'offerta e le risorse tecniche, è possibile procedere all'invio dell'offerta. L'invio dell'offerta avvia il processo di pubblicazione. Durante questo processo la soluzione viene sottoposta a test, viene convalidata e certificata, quindi viene "aggiunta" in Azure Marketplace.

## <a name="parts-of-an-offer"></a>Parti di un'offerta

Gli articoli seguenti illustrano le parti fondamentali di un'offerta di modulo IoT Edge.

- [Prerequisiti](./cpp-prerequisites.md) <br>Questo articolo elenca i requisiti aziendali e tecnici prima che sia possibile creare o pubblicare un'offerta di modulo IoT Edge.
- [Preparare gli asset tecnici dei moduli IoT Edge](./cpp-create-technical-assets.md) <br>Questo articolo descrive come preparare gli asset tecnici per un modulo IoT Edge. Questi asset devono soddisfare tutti i criteri tecnici necessari prima che il modulo IoT Edge possa essere pubblicato in Azure Marketplace.
- [Creare un'offerta di moduli IoT Edge](./cpp-create-offer.md) <br>Questo articolo elenca i passaggi necessari per creare una nuova voce di offerta di modulo IoT Edge usando il [portale Cloud Partner](https://cloudpartner.azure.com).
- [Pubblicare l'offerta di modulo IoT Edge](./cpp-publish-offer.md)<br> Questo articolo descrive come inviare l'offerta per la pubblicazione in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

Rivedere i [requisiti tecnici e aziendali](./cpp-prerequisites.md) per la pubblicazione di un modulo IoT Edge in Microsoft Azure Marketplace.
