---
title: Usare i gruppi di dispositivi nell'applicazione Azure IoT Central. Documenti Microsoft
description: Come operatore, informazioni su come usare i gruppi di dispositivi per analizzare i dati di telemetria dai dispositivi nell'applicazione Azure IoT Central.As an operator, learn how to use device groups to analyze telemetry from devices in your Azure IoT Central application.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167296"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Esercitazione: Usare i gruppi di dispositivi per analizzare i dati di telemetria dei dispositiviTutorial: Use device groups to analyze device telemetry

Questo articolo descrive come, come operatore, usare i gruppi di dispositivi per analizzare i dati di telemetria dei dispositivi nell'applicazione Azure IoT Central.This article describes how, as an operator, to use device groups to analyze device telemetry in your Azure IoT Central application.

Un gruppo di dispositivi è un elenco di dispositivi raggruppati perché corrispondono ad alcuni criteri specificati. I gruppi di dispositivi consentono di gestire, visualizzare e analizzare i dispositivi su larga scala raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio, è possibile creare un gruppo di dispositivi per elencare tutti i dispositivi di condizionatore d'aria a Seattle per consentire a un tecnico di trovare i dispositivi di cui è responsabile.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di dispositivi
> * Usare un gruppo di dispositivi per analizzare i dati di telemetria dei dispositiviUse a device group to analyze device telemetry

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare gli argomenti di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) e [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-pnp-device.md) per creare il modello di dispositivo **MXChip IoT DevKit** da usare.

## <a name="create-simulated-devices"></a>Creare dispositivi simulati

Prima di creare un gruppo di dispositivi, aggiungere almeno cinque dispositivi simulati dal modello di dispositivo MXChip IoT DevKit da usare in questa esercitazione:Before you create a device group, add at least five simulated devices from the **MXChip IoT DevKit** device template to use in this tutorial:

![Cinque dispositivi di sensori simulati](./media/tutorial-use-device-groups/simulated-devices.png)

Per quattro dei dispositivi sensore simulati, utilizzare la visualizzazione **Gestisci dispositivo** per impostare il nome del cliente su *Contoso*:

![Impostare il nome del cliente su Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Creare un gruppo di dispositivi

Per creare un gruppo di dispositivi:

1. Scegliere **Gruppi di dispositivi** nel riquadro sinistro.

1. Selezionare **+**:

    ![Nuovo gruppo di dispositivi](media/tutorial-use-device-groups/image1.png)

1. Assegnare al gruppo di dispositivi il nome *Contoso devices*. È anche possibile aggiungere una descrizione. Un gruppo di dispositivi può contenere solo dispositivi da un singolo modello di dispositivo. Scegliere il modello di dispositivo **MXChip IoT DevKit** da usare per questo gruppo.

1. Per personalizzare il gruppo di dispositivi in modo da includere solo i dispositivi appartenenti a **Contoso,** selezionare **.** Selezionare la proprietà **Customer Name,** l'operatore di confronto **Equals** e **Contoso** come valore. È possibile aggiungere più filtri e dispositivi che soddisfano **tutti** i criteri di filtro vengono inseriti nel gruppo di dispositivi. Il gruppo di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, pertanto chiunque può visualizzare, modificare o eliminare il gruppo di dispositivi:The device group you create is accessible to anyone has access to the application, so anyone can view, modify, or delete the device group:

    ![Query gruppo di dispositivi](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Il gruppo di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

> [!NOTE]
> Per i dispositivi Azure IoT Edge, selezionare Modelli di Azure IoT Edge per creare un gruppo di dispositivi.

## <a name="analytics"></a>Analytics

È possibile usare **Analytics** con un gruppo di dispositivi per analizzare i dati di telemetria dai dispositivi nel gruppo. Ad esempio, è possibile tracciare la temperatura media indicata da tutti i sensori ambientali Contoso.

Per analizzare i dati di telemetria per un gruppo di dispositivi:To analyze the telemetry for a device group:

1. Scegli **Analytics** nel riquadro sinistro.

1. Selezionare il gruppo di **dispositivi Contoso** creato. Aggiungere quindi entrambi i tipi di telemetria **Temperatura** e **Umidità:**

    ![Creare analisi](./media/tutorial-use-device-groups/create-analysis.png)

    Usare le icone della ruota a ingranaggi accanto ai tipi di telemetria per selezionare un tipo di aggregazione. Il valore predefinito è **Media**. Utilizzare **Dividi per** per modificare la modalità di visualizzazione dei dati aggregati. Ad esempio, se si divide per ID dispositivo, viene visualizzato un grafico per ogni dispositivo quando si seleziona **Analizza**.

1. Selezionare Analizza per visualizzare i valori di telemetria medi:Select **Analyze** to view the average telemetry values:

    ![Visualizza analisi](./media/tutorial-use-device-groups/view-analysis.png)

    È possibile personalizzare la visualizzazione, modificare il periodo di tempo visualizzato ed esportare i dati.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare i gruppi di dispositivi nell'applicazione Azure IoT Central, ecco il passaggio successivo consigliato:Now that you've learned how to use device groups in your Azure IoT Central application, here is the suggested next step:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](tutorial-create-telemetry-rules.md)
