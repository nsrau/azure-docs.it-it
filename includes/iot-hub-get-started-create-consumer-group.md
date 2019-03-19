---
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 242f601ced4838a1b4e559774c25d05de04ddb77
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57016391"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati dall'hub IoT di Azure. In questa esercitazione si crea un gruppo di consumer che verrà usato da un prossimo servizio di Azure per leggere i dati dall'hub IoT.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.

2. Fare clic su **Endpoint predefiniti** nel riquadro a sinistra, selezionare **Eventi** nel riquadro superiore e immettere un nome in **Gruppi di consumer** nel riquadro a destra. Fare clic su **Salva** dopo aver modificato il valore **TTL predefinito** e averlo riportato al valore originale.

   ![Creare un gruppo di consumer nell'hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
