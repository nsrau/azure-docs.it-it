---
title: "Esercitazione: Usare i gruppi di dispositivi nell'applicazione Azure IoT Central | Microsoft Docs"
description: "Esercitazione: un operatore può apprendere come usare i gruppi di dispositivi per analizzare i dati di telemetria dei dispositivi nell'applicazione Azure IoT Central."
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967942"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Esercitazione: Usare i gruppi di dispositivi per analizzare la telemetria dei dispositivi

L'articolo descrive come un operatore può usare i gruppi di dispositivi per analizzare la telemetria dei dispositivi nell'applicazione Azure IoT Central.

Un gruppo di dispositivi è un elenco di dispositivi raggruppati in base ad alcuni criteri specificati. I gruppi di dispositivi consentono di gestire, visualizzare e analizzare grandi quantità di dispositivi raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio, è possibile creare un gruppo di dispositivi per elencare tutti i dispositivi condizionatore di Seattle per consentire a un tecnico di trovare i dispositivi di cui è responsabile.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di dispositivi
> * Usare un gruppo di dispositivi per analizzare la telemetria dei dispositivi

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare gli argomenti di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) e [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-simulated-device.md) per creare il modello di dispositivo **MXChip IoT DevKit** da usare.

## <a name="create-simulated-devices"></a>Creare dispositivi simulati

Prima di creare un gruppo di dispositivi, aggiungere almeno cinque dispositivi simulati dal modello di dispositivo **MXChip IoT DevKit** da usare in questa esercitazione:

![Cinque dispositivi sensore simulati](./media/tutorial-use-device-groups/simulated-devices.png)

Per quattro dei dispositivi sensore simulati usare la visualizzazione **Gestisci dispositivo** per impostare il nome del cliente su *Contoso*:

![Imposta il nome del cliente su Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Creare un gruppo di dispositivi

Per creare un gruppo di dispositivi:

1. Scegliere **Gruppi di dispositivi** nel riquadro sinistro.

1. Selezionare **+** :

    ![Nuovo gruppo di dispositivi](media/tutorial-use-device-groups/image1.png)

1. Assegnare al gruppo di dispositivi il nome *Contoso devices*. È anche possibile aggiungere una descrizione. Un gruppo di dispositivi può contenere solo dispositivi basati su un singolo modello di dispositivo. Scegliere il modello di dispositivo **MXChip IoT DevKit** da usare per questo gruppo.

1. Per personalizzare il gruppo di dispositivi in modo da includere solo i dispositivi appartenenti a **Contoso**, selezionare **+ Filtro**. Selezionare la proprietà **Customer Name**, l'operatore di confronto **Equals** e **Contoso** come valore. È possibile aggiungere più filtri per inserire nel gruppo i dispositivi che soddisfano **tutti** i criteri di filtro. Il gruppo di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, in modo che tutti gli utenti possano visualizzare, modificare o eliminare il gruppo di dispositivi:

    ![Query sul gruppo di dispositivi](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Il gruppo di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

> [!NOTE]
> Per i dispositivi Azure IoT Edge selezionare i modelli di Azure IoT Edge per creare un gruppo di dispositivi.

## <a name="analytics"></a>Analisi

È possibile usare **Analytics** con un gruppo di dispositivi per analizzare i dati di telemetria dei dispositivi del gruppo. È possibile, ad esempio, tracciare la temperatura media indicata da tutti i sensori ambientali di Contoso.

Per analizzare i dati di telemetria per un gruppo di dispositivi:

1. Scegliere **Analytics** nel riquadro sinistro.

1. Selezionare il gruppo di dispositivi **Contoso devices** creato. Aggiungere quindi sia la **temperatura** che l'**umidità** come tipi di dati di telemetria:

    ![Creare un'analisi](./media/tutorial-use-device-groups/create-analysis.png)

    Usare le icone a forma di ingranaggio accanto ai tipi di telemetria per selezionare un tipo di aggregazione. Il valore predefinito è **Media**. Usare **Dividi per** per modificare la modalità di visualizzazione dei dati di aggregazione. Ad esempio, se si divide per ID dispositivo viene visualizzato un tracciato per ogni dispositivo quando si seleziona **Analizza**.

1. Selezionare **Analizza** per visualizzare i valori medi di telemetria:

    ![Visualizzare l'analisi](./media/tutorial-use-device-groups/view-analysis.png)

    È possibile personalizzare la visualizzazione, modificare il periodo di tempo indicato ed esportare i dati.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per usare i gruppi di dispositivi nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](tutorial-create-telemetry-rules.md)
