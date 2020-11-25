---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028108"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I [gruppi di consumer](../articles/event-hubs/event-hubs-features.md#event-consumers) offrono visualizzazioni indipendenti nel flusso di eventi che consentono alle app e ai servizi di Azure di usare i dati in modo indipendente dallo stesso endpoint dell'hub eventi. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub Internet che verrà usato più avanti in questa esercitazione per eseguire il pull dei dati dall'endpoint.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.

2. Nel riquadro sinistro selezionare **endpoint predefiniti**, selezionare **eventi** nel riquadro di destra e immettere un nome in **gruppi di consumer**. Selezionare **Salva**.

   ![Creare un gruppo di consumer nell'hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)