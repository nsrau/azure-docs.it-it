---
title: Usare i gruppi di dispositivi nell'applicazione IoT Central di Azure | Microsoft Docs
description: Come operatore, come usare i gruppi di dispositivi nell'applicazione IoT Central di Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 9676d76f03b13ca93183e74790642e76b993b8b7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987903"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Usare i gruppi di dispositivi nell'applicazione IoT Central di Azure (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un operatore per usare i gruppi di dispositivi nell'applicazione IoT Central di Azure.

Un gruppo di dispositivi è un elenco di dispositivi raggruppati in quanto corrispondono ad alcuni criteri specificati. I gruppi di dispositivi consentono di gestire, visualizzare e analizzare i dispositivi su larga scala raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio, è possibile creare un gruppo di dispositivi per elencare tutti i dispositivi aria condizionata a Seattle per consentire a un tecnico di trovare i dispositivi per i quali sono responsabili. Questo articolo illustra come creare e configurare i gruppi di dispositivi.

>  [!NOTE] 
> Per i dispositivi Azure IoT Edge sarà necessario selezionare Azure IoT Edge modelli per creare un gruppo di dispositivi

## <a name="create-a-device-group"></a>Creare un gruppo di dispositivi

Per creare un gruppo di dispositivi:

1. Scegliere **gruppi di dispositivi** nel riquadro sinistro.

1. Selezionare **+ nuovo**.

    ![Nuovo gruppo di dispositivi](media/howto-use-device-groups-pnp/image1.png)

1. Assegnare al gruppo di dispositivi un nome univoco all'interno dell'intera applicazione. È anche possibile aggiungere una descrizione. Un gruppo di dispositivi può contenere solo dispositivi da un singolo modello di dispositivo. Scegliere il modello di dispositivo da usare per questo gruppo.

1. Creare la query per identificare i dispositivi per il gruppo di dispositivi selezionando una proprietà, un operatore di confronto e un valore. È possibile aggiungere più query e dispositivi che soddisfano **tutti** i criteri vengono inseriti nel gruppo di dispositivi. Il gruppo di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, in modo che chiunque possa visualizzare, modificare o eliminare il gruppo di dispositivi.

    ![Query sul gruppo di dispositivi](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Il gruppo di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

## <a name="analytics"></a>Analytics

L'analisi nei gruppi di dispositivi corrisponde alla scheda analisi principale nel riquadro sinistro. È possibile approfondire le analisi nell'articolo dedicato a [come creare le analisi](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i gruppi di dispositivi nell'applicazione IoT Central di Azure, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
