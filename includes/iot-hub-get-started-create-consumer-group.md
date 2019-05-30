---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248790"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

[Gruppi di consumer](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) forniscono visualizzazioni indipendenti nel flusso di eventi che consentono le App e servizi di Azure per utilizzare in modo indipendente i dati dall'endpoint di Hub eventi stesso. In questa sezione si aggiunge un gruppo di consumer all'endpoint predefinito dell'hub IoT che viene usato più avanti in questa esercitazione per estrarre i dati dall'endpoint.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.

2. Nel riquadro sinistro, selezionare **endpoint predefiniti**, selezionare **eventi** nel riquadro di destra e immettere un nome in **gruppi di Consumer**. Selezionare **Salva**.

   ![Creare un gruppo di consumer nell'hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
