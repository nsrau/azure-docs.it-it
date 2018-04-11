---
title: Aggiornare l'hub IoT di Azure | Microsoft Docs
description: Modificare il piano tariffario e il livello di scalabilità dell'hub IoT per ottenere funzionalità aggiuntive di gestione del dispositivo e di messaggistica.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: d383d26b406c012b6b76225faf89f4b5dbd6bb9c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-upgrade-your-iot-hub"></a>Come aggiornare l'hub IoT

Con l'aumentare della soluzione IoT, l'hub IoT di Azure offre opzioni di scalabilità. L'hub IoT di Azure offre due livelli, base (B) e standard (S), per supportare i clienti che vogliono usare diverse funzionalità. All'interno di ogni livello sono disponibili tre dimensioni (1, 2 e 3) che determinano il numero di messaggi che è possibile inviare ogni giorno. 

Quando si dispone di più dispositivi e si necessita di altre funzionalità, esistono tre modi per regolare l'hub IoT in base alle proprie esigenze:

* Aggiungere unità nell'hub IoT. Ad esempio, ogni unità aggiuntiva in un hub IoT B1 offre 400.000 messaggi aggiuntivi al giorno. 
* Modificare le dimensioni dell'hub IoT. Ad esempio, eseguire la migrazione dal livello B1 al livello B2 per aumentare la quantità di messaggi supportata da ciascuna unità al giorno.
* Eseguire l'aggiornamento a un livello superiore. Ad esempio, eseguire l'aggiornamento dal livello B1 al livello S1 per la stessa capacità di messaggistica, ma con funzionalità avanzate fornite nel livello standard.

Queste modifiche possono essere apportate senza interrompere le operazioni esistenti.

Se si vuole effettuare il downgrade dell'hub IoT, è possibile rimuovere le unità e ridurre le dimensioni dell'hub IoT. Non è tuttavia possibile effettuare il downgrade a un livello inferiore. Ad esempio, è possibile passare dal livello S2 al livello S1, ma non dal livello S2 al livello B1. 

Questi esempi consentono di comprendere come regolare l'hub IoT in base ai cambiamenti della soluzione. Per informazioni specifiche sulle funzionalità di ogni livello, fare sempre riferimento a [Prezzi di Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Aggiornare l'hub IoT esistente 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'hub IoT. 
2. Selezionare **Piano tariffario e scalabilità**. 

   ![Piano tariffario e scalabilità](./media/iot-hub-upgrade/pricing-scale.png)

3. Per modificare il livello dell'hub, selezionare **Piano tariffario e livello di scalabilità**. Scegliere il nuovo livello e quindi fare clic su **Seleziona**.

   ![Piano tariffario e scalabilità](./media/iot-hub-upgrade/select-tier.png)

4. Per modificare il numero di unità nell'hub, immettere un nuovo valore in **Unità di hub IoT**. 
5. Fare clic su **Salva** per salvare le modifiche. 

L'hub IoT viene ora regolato e le configurazioni rimangono invariate. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come scegliere il livello dell'hub IoT corretto](iot-hub-scaling.md). 

