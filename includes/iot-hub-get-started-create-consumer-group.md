---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66248790"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

[I gruppi di](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) consumer forniscono visualizzazioni indipendenti nel flusso di eventi che consentono alle app e ai servizi di Azure di utilizzare in modo indipendente i dati dello stesso endpoint dell'hub eventi. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub IoT usato più avanti in questa esercitazione per eseguire il pull dei dati dall'endpoint.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.

2. Nel riquadro sinistro selezionare **Endpoint predefiniti**, **eventi** nel riquadro destro e immettere un nome in Gruppi **di consumer.** Selezionare **Salva**.

   ![Creare un gruppo di consumer nell'hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
