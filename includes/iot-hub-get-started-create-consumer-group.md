---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "66248790"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I [gruppi di consumer](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) offrono visualizzazioni indipendenti nel flusso di eventi che consentono alle app e ai servizi di Azure di usare i dati in modo indipendente dallo stesso endpoint dell'hub eventi. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub Internet che verrà usato più avanti in questa esercitazione per eseguire il pull dei dati dall'endpoint.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.

2. Nel riquadro sinistro selezionare **endpoint predefiniti**, selezionare **eventi** nel riquadro di destra e immettere un nome in **gruppi di consumer**. Selezionare **Salva**.

   ![Creare un gruppo di consumer nell'hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
