---
title: Usare i gruppi di dispositivi nell'applicazione IoT Central di Azure | Microsoft Docs
description: Come operatore, informazioni su come usare i gruppi di dispositivi per analizzare i dati di telemetria dai dispositivi nell'applicazione IoT Central di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 0f222cf9907dbf768a5f93842ff3df3fddf224a4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180868"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>Esercitazione: usare i gruppi di dispositivi per analizzare i dati di telemetria del dispositivo (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un operatore per usare i gruppi di dispositivi per analizzare i dati di telemetria del dispositivo nell'applicazione IoT Central di Azure.

Un gruppo di dispositivi è un elenco di dispositivi raggruppati in quanto corrispondono ad alcuni criteri specificati. I gruppi di dispositivi consentono di gestire, visualizzare e analizzare i dispositivi su larga scala raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio, è possibile creare un gruppo di dispositivi per elencare tutti i dispositivi aria condizionata a Seattle per consentire a un tecnico di trovare i dispositivi per i quali sono responsabili.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di dispositivi
> * Usare un gruppo di dispositivi per analizzare i dati di telemetria del dispositivo

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare il [creare un'applicazione Azure IOT Central](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) e [aggiungere un dispositivo simulato alle guide introduttive dell'applicazione IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) per creare il modello di dispositivo del **sensore di ambiente** da usare.

## <a name="create-simulated-devices"></a>Creare dispositivi simulati

Prima di creare un gruppo di dispositivi, aggiungere almeno cinque dispositivi simulati dal modello di dispositivo del **sensore di ambiente** da usare in questa esercitazione:

![Cinque dispositivi del sensore ambientale simulati](./media/tutorial-use-device-groups-pnp/simulated-devices.png)

Per quattro dispositivi del sensore ambientale, utilizzare la visualizzazione **proprietà del sensore ambientale** per impostare il nome del cliente su **Contoso**:

![Imposta il nome del cliente su contoso](./media/tutorial-use-device-groups-pnp/customer-name.png)

## <a name="create-a-device-group"></a>Creare un gruppo di dispositivi

Per creare un gruppo di dispositivi:

1. Scegliere **gruppi di dispositivi** nel riquadro sinistro.

1. Selezionare **+ nuovo**.

    ![Nuovo gruppo di dispositivi](media/tutorial-use-device-groups-pnp/image1.png)

1. Assegnare un nome al gruppo di dispositivi, ad esempio i **dispositivi contoso**. È anche possibile aggiungere una descrizione. Un gruppo di dispositivi può contenere solo dispositivi da un singolo modello di dispositivo. Scegliere il modello di dispositivo del **sensore ambientale** da usare per questo gruppo.

1. Creare la query per identificare i dispositivi appartenenti a **Contoso** per il gruppo di dispositivi selezionando la proprietà **nome cliente** , l'operatore di confronto **uguale** a e **Contoso** come valore. È possibile aggiungere più query e dispositivi che soddisfano **tutti** i criteri vengono inseriti nel gruppo di dispositivi. Il gruppo di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, in modo che chiunque possa visualizzare, modificare o eliminare il gruppo di dispositivi.

    ![Query sul gruppo di dispositivi](media/tutorial-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Il gruppo di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

> [!NOTE]
> Per Azure IoT Edge dispositivi, selezionare Azure IoT Edge modelli per creare un gruppo di dispositivi.

## <a name="analytics"></a>Analytics

È possibile usare **Analytics** con un gruppo di dispositivi per analizzare i dati di telemetria dai dispositivi del gruppo. È possibile, ad esempio, tracciare la temperatura media indicata da tutti i sensori ambientali di contoso.

Per analizzare i dati di telemetria per un gruppo di dispositivi:

1. Scegliere **analisi** nel riquadro sinistro.

1. Selezionare il gruppo di dispositivi del dispositivo **Contoso** creato. Aggiungere quindi i tipi di telemetria di **temperatura** e **umidità** :

    ![Crea analisi](./media/tutorial-use-device-groups-pnp/create-analysis.png)

    Usare le icone a forma di ingranaggio accanto ai tipi di telemetria per selezionare un tipo di aggregazione. Il valore predefinito è **media**. Utilizzare **Split by** per modificare il modo in cui vengono visualizzati i dati di aggregazione. Ad esempio, se si divide per ID dispositivo viene visualizzato un tracciato per ogni dispositivo quando si seleziona **analizza**.

1. Selezionare **analizza** per visualizzare i valori medi di telemetria:

    ![Visualizza analisi](./media/tutorial-use-device-groups-pnp/view-analysis.png)

    È possibile personalizzare la visualizzazione, modificare il periodo di tempo indicato ed esportare i dati.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i gruppi di dispositivi nell'applicazione IoT Central di Azure, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
