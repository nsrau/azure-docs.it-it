---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876668"
---
### <a name="add-relationships"></a>Aggiungere relazioni

Nel modello di dispositivo **LVA Edge Gateway**, in **Moduli/LVA Edge Gateway Module** selezionare **Relazioni**. Selezionare **+ Aggiungi relazione** e aggiungere le due relazioni seguenti:

|Nome visualizzato               |Nome          |Destinazione |
|-------------------------- |------------- |------ |
|LVA Edge Motion Detector   |Usare l'impostazione predefinita.   |LVA Edge Motion Detector Device |
|LVA Edge Object Detector   |Usare l'impostazione predefinita.   |LVA Edge Object Detector Device |

Selezionare quindi **Salva**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Aggiungere relazioni":::

### <a name="add-views"></a>Aggiungere visualizzazioni

Il modello di dispositivo **LVA Edge Gateway** non include alcuna definizione di visualizzazione.

Per aggiungere una visualizzazione al modello di dispositivo:

1. Nel modello di dispositivo **LVA Edge Gateway** passare a **Visualizzazioni** e selezionare il riquadro **Visualizzazione del dispositivo**.

1. Immettere *LVA Edge Gateway device* come nome della visualizzazione.

1. Aggiungere i riquadri seguenti alla visualizzazione:

    * Un riquadro con le proprietà **Device Info**: **Device model**, **Manufacturer**, **Operating system**, **Processor architecture**, **Software version**, **Total memory** e **Total storage**.
    * Un riquadro con un grafico a linee con i valori di telemetria **Free Memory** e **System Heartbeat**.
    * Un riquadro di cronologia eventi con gli eventi seguenti: **Create Camera**, **Delete Camera**, **Module Restart**, **Module Started**, **Module Stopped**.
    * Un riquadro 2x1 con l'ultimo valore noto per i dati di telemetria **IoT Central Client State**.
    * Un riquadro 2x1 con l'ultimo valore noto per i dati di telemetria **Module State**.
    * Un riquadro 1x1 con l'ultimo valore noto per i dati di telemetria **System Heartbeat**.
    * Un riquadro 1x1 con l'ultimo valore noto per i dati di telemetria **Connected Cameras**.

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Aggiungere relazioni":::

1. Selezionare **Salva**.

### <a name="publish-the-device-template"></a>Pubblicare il modello di dispositivo

Per poter aggiungere un dispositivo all'applicazione, è prima necessario pubblicare il modello di dispositivo:

1. Nel modello di dispositivo **LVA Edge Gateway** selezionare **Pubblica**.

1. Nella pagina **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica**.

**LVA Edge Gateway** è ora disponibile come tipo di dispositivo da usare nella pagina **Dispositivi** nell'applicazione.

## <a name="add-a-gateway-device"></a>Aggiungere un dispositivo gateway

Per aggiungere un dispositivo **LVA Edge Gateway** all'applicazione:

1. Passare alla pagina **Dispositivi** e selezionare il modello di dispositivo **LVA Edge Gateway**.

1. Selezionare **+ Nuovo**.

1. Nella finestra di dialogo **Crea un nuovo dispositivo** modificare il nome del dispositivo in *LVA Gateway 001* e l'ID dispositivo in *lva-gateway-001*.

    > [!NOTE]
    > L'ID dispositivo deve essere univoco nell'applicazione.

1. Selezionare **Crea**.

Lo stato del dispositivo è **Registrato**.

### <a name="get-the-device-credentials"></a>Ottenere le credenziali del dispositivo

È necessario ottenere le credenziali che consentono la connessione del dispositivo all'applicazione IoT Central. Per ottenere le credenziali del dispositivo:

1. Nella pagina **Dispositivi** selezionare il dispositivo **lva-gateway-001** creato.

1. Selezionare **Connetti**.

1. Nella pagina **Connessione del dispositivo** prendere nota nel valore di **Ambito ID**, **ID dispositivo** e **Chiave primaria** del dispositivo nel file *scratchpad.txt*. Questi valori verranno usati più avanti.

1. Assicurarsi che il metodo di connessione sia impostato su **Firma di accesso condiviso**.

1. Selezionare **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stata creata un'applicazione IoT Central usando il modello di applicazione **Analisi video - rilevamento movimento e oggetti**, è stato creato un modello di dispositivo per il dispositivo gateway ed è stato aggiunto un dispositivo gateway all'applicazione.

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in una macchina virtuale cloud con flussi video simulati:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (macchina virtuale Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in un dispositivo reale con la telecamera **ONVIF**:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
