---
title: Aggiornare l'hub IoT di Azure | Microsoft Docs
description: Modificare il piano tariffario e il livello di scalabilità dell'hub IoT per ottenere funzionalità aggiuntive di gestione del dispositivo e di messaggistica.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440234"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Come aggiornare l'hub IoT

Con l'aumentare della soluzione IoT, l'hub IoT di Azure offre opzioni di scalabilità. L'hub IoT di Azure offre due livelli, base (B) e standard (S), per supportare i clienti che vogliono usare diverse funzionalità. All'interno di ogni livello sono disponibili tre dimensioni (1, 2 e 3) che determinano il numero di messaggi che è possibile inviare ogni giorno.

Quando si dispone di più dispositivi e si necessita di altre funzionalità, esistono tre modi per regolare l'hub IoT in base alle proprie esigenze:

* Aggiungere unità nell'hub IoT. Ad esempio, ogni unità aggiuntiva in un hub IoT B1 offre 400.000 messaggi aggiuntivi al giorno.

* Modificare le dimensioni dell'hub IoT. Ad esempio, eseguire la migrazione dal livello B1 al livello B2 per aumentare il numero di messaggi in grado di supportare ogni unità al giorno.

* Eseguire l'aggiornamento a un livello superiore. Ad esempio, eseguire l'aggiornamento dal livello B1 al livello S1 per l'accesso a funzionalità avanzate con la stessa capacità di messaggistica.

Queste modifiche possono essere apportate senza interrompere le operazioni esistenti.

Se si vuole effettuare il downgrade dell'hub IoT, è possibile rimuovere le unità e ridurre le dimensioni dell'hub IoT, ma è possibile effettuare il downgrade a un livello inferiore. Ad esempio, è possibile passare dal livello S2 al livello S1, ma non dal livello S2 al livello B1. Un solo tipo di [edizione dell'Iot Hub](https://azure.microsoft.com/pricing/details/iot-hub/) all'interno di un livello può essere scelto per ogni IoT Hub. Ad esempio, è possibile creare un hub IoT con più unità di S1, ma non con una combinazione di unità appartenenti a edizioni diverse, ad esempio, S1, B3 o S1 e S2.

Questi esempi consentono di comprendere come regolare l'hub IoT in base ai cambiamenti della soluzione. Per informazioni specifiche sulle funzionalità di ogni livello, è consigliabile vedere sempre [prezzi dell'IoT Hub di Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Aggiornare l'hub IoT esistente

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'hub IoT.

2. Selezionare **Piano tariffario e scalabilità**.

   ![Prezzi e scalabilità](./media/iot-hub-upgrade/pricing-scale.png)

3. Per modificare il livello dell'hub, selezionare **Piano tariffario e livello di scalabilità**. Scegliere il nuovo livello e quindi fare clic su **Seleziona**.

   ![Piano tariffario e livello di scalabilità](./media/iot-hub-upgrade/select-tier.png)

4. Per modificare il numero di unità nell'hub, immettere un nuovo valore in **Unità di hub IoT**.

5. Fare clic su **Salva** per salvare le modifiche.

L'hub IoT viene ora regolato e le configurazioni rimangono invariate.

Il limite massimo di partizioni per IoT Hub di livello basic e livello standard dell'IoT Hub è 32. La maggior parte degli hub IoT richiede solo 4 partizioni. Il limite partizioni mette viene scelto quando l'hub IoT viene creato e associa i messaggi da dispositivo a cloud al numero di lettori simultanei di tali messaggi. Questo valore rimarrà invariato con la migrazione dal livello di base al livello standard.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come scegliere il livello dell'hub IoT corretto](iot-hub-scaling.md).