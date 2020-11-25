---
title: "Esercitazione: Usare i gruppi di dispositivi nell'applicazione Azure IoT Central | Microsoft Docs"
description: "Esercitazione: un operatore può apprendere come usare i gruppi di dispositivi per analizzare i dati di telemetria dei dispositivi nell'applicazione Azure IoT Central."
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 8c26afc9cf9630f6d26ddc76759393a6ea1a8696
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990299"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Esercitazione: Usare i gruppi di dispositivi per analizzare la telemetria dei dispositivi

L'articolo descrive come un operatore può usare i gruppi di dispositivi per analizzare la telemetria dei dispositivi nell'applicazione Azure IoT Central.

Un gruppo di dispositivi è un elenco di dispositivi raggruppati in base ad alcuni criteri specificati. I gruppi di dispositivi consentono di gestire, visualizzare e analizzare grandi quantità di dispositivi raggruppando i dispositivi in gruppi logici più piccoli. Ad esempio, è possibile creare un gruppo di dispositivi per elencare tutti i dispositivi condizionatore di Seattle per consentire a un tecnico di trovare i dispositivi di cui è responsabile.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di dispositivi
> * Usare un gruppo di dispositivi per analizzare la telemetria dei dispositivi

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare le guide di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) e [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-simulated-device.md) per creare il modello di dispositivo di tipo **Controller sensore** da usare.

## <a name="create-simulated-devices"></a>Creare dispositivi simulati

Prima di creare un gruppo di dispositivi, aggiungere almeno cinque dispositivi simulati basati sul modello **Controller sensore** da usare in questa esercitazione:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Screenshot che mostra cinque dispositivi simulati di tipo controller sensore":::

Per quattro dei dispositivi sensore simulati usare la visualizzazione **Gestisci dispositivo** per impostare il nome del cliente su *Contoso*:

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Screenshot che mostra come impostare la proprietà cloud del nome cliente":::

## <a name="create-a-device-group"></a>Creare un gruppo di dispositivi

Per creare un gruppo di dispositivi:

1. Scegliere **Gruppi di dispositivi** nel riquadro sinistro.

1. Selezionare **+ Nuovo**.

1. Assegnare al gruppo di dispositivi il nome *Contoso devices*. È anche possibile aggiungere una descrizione. Un gruppo di dispositivi può contenere solo dispositivi basati su un singolo modello di dispositivo. Scegliere il modello di dispositivo **Controller sensore** da usare per questo gruppo.

1. Per personalizzare il gruppo di dispositivi in modo da includere solo i dispositivi appartenenti a **Contoso**, selezionare **+ Filtro**. Selezionare la proprietà **Customer Name**, l'operatore di confronto **Equals** e **Contoso** come valore. È possibile aggiungere più filtri per inserire nel gruppo i dispositivi che soddisfano **tutti** i criteri di filtro. Il gruppo di dispositivi creato è accessibile a chiunque abbia accesso all'applicazione, per cui chiunque può visualizzarlo, modificarlo o eliminarlo.

    > [!TIP]
    > Il gruppo di dispositivi è una query dinamica. Ogni volta che si visualizza l'elenco dei dispositivi, possono essere presenti dispositivi diversi nell'elenco. L'elenco dipende da quali dispositivi attualmente soddisfano i criteri della query.

1. Scegliere **Salva**.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Screenshot che mostra la configurazione della query per il gruppo di dispositivi":::

> [!NOTE]
> Per i dispositivi Azure IoT Edge selezionare i modelli di Azure IoT Edge per creare un gruppo di dispositivi.

## <a name="analytics"></a>Analisi

È possibile usare **Analytics** con un gruppo di dispositivi per analizzare i dati di telemetria dei dispositivi del gruppo. È possibile, ad esempio, tracciare la temperatura media indicata da tutti i sensori ambientali di Contoso.

Per analizzare i dati di telemetria per un gruppo di dispositivi:

1. Scegliere **Analytics** nel riquadro sinistro.

1. Selezionare il gruppo di dispositivi **Contoso devices** creato. Aggiungere quindi sia la **temperatura** che l'**umidità** come tipi di dati di telemetria:

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Screenshot che mostra i tipi di dati di telemetria selezionati per l'analisi":::

    Usare le icone a forma di ingranaggio accanto ai tipi di telemetria per selezionare un tipo di aggregazione. Il valore predefinito è **Media**. Usare **Raggruppa per** per cambiare il modo in cui vengono visualizzati i dati aggregati. Ad esempio, se si divide per ID dispositivo viene visualizzato un tracciato per ogni dispositivo quando si seleziona **Analizza**.

1. Selezionare **Analizza** per visualizzare i valori medi di telemetria:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Screenshot che mostra i valori medi per tutti i dispositivi Contoso":::

    È possibile personalizzare la visualizzazione, modificare il periodo di tempo indicato ed esportare i dati.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per usare i gruppi di dispositivi nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come creare regole dei dati di telemetria](tutorial-create-telemetry-rules.md)
