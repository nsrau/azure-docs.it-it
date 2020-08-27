---
title: Esercitazione - Monitorare il video usando il modello di applicazione Analisi video - rilevamento movimento e oggetti di Azure IoT Central
description: Questa esercitazione mostra come usare i dashboard del modello di applicazione Analisi video - rilevamento movimento e oggetti per gestire le telecamere e monitorare il video.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 76e72e8fd134c65cc9334e635375cc25e9b09a75
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719071"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Esercitazione: Monitorare e gestire un'applicazione Analisi video - rilevamento movimento e oggetti

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Aggiungere le telecamere per il rilevamento di oggetti e movimento all'applicazione IoT Central.
> * Gestire i flussi video e riprodurli quando vengono rilevati eventi interessanti.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare le esercitazioni seguenti:

* Esercitazione [Creare un'applicazione Analisi video live in Azure IoT Central](./tutorial-video-analytics-create-app.md).
* Una delle esercitazioni precedenti, [Creare un'istanza di IoT Edge per Analisi video live (macchina virtuale Linux)](tutorial-video-analytics-iot-edge-vm.md) or [Creare un'istanza di IoT Edge per Analisi video live (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md).

Per eseguire l'applicazione visualizzatore video, è necessario che nel computer locale sia installato [Docker](https://www.docker.com/products/docker-desktop).

## <a name="add-an-object-detection-camera"></a>Aggiungere una telecamera per il rilevamento degli oggetti

Nell'applicazione IoT Central passare al dispositivo **LVA Gateway 001** creato in precedenza. Selezionare quindi la scheda **Comandi**.

Usare i valori della tabella seguente come valori dei parametri per il comando **Add Camera Request**. I valori mostrati nella tabella presuppongono che si stia usando la telecamera simulata nella macchina virtuale di Azure. Se si usa una telecamera reale, modificare i valori in modo appropriato:

| Campo| Descrizione| Valore di esempio|
|---------|---------|---------|
| Camera ID      | ID dispositivo per il provisioning | camera-003 |
| Camera Name    | Nome descrittivo           | Object detection camera |
| RTSP Url       | Indirizzo del flusso   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP Username  |                         | utente    |
| RTSP Password  |                         | password    |
| Tipo di rilevamento | Elenco a discesa                | Rilevamento di oggetti       |

Selezionare **Esegui** per aggiungere il dispositivo telecamera:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Aggiungere la telecamera":::

> [!NOTE]
> Il modello di dispositivo **LVA Edge Object Detector** è già presente nell'applicazione.

## <a name="add-a-motion-detection-camera-optional"></a>Aggiungere una telecamera per il rilevamento del movimento (facoltativo)

Se si hanno due telecamere connesse al dispositivo gateway IoT Edge, ripetere i passaggi precedenti per aggiungere una telecamera per il rilevamento del movimento all'applicazione. Usare valori diversi per i parametri **Camera ID**, **Camera Name**, e **RTSP Url**.

## <a name="view-the-downstream-devices"></a>Visualizzare i dispositivi downstream

Selezionare la scheda **Dispositivi downstream** per il dispositivo **LVA Gateway 001** per vedere i dispositivi telecamera appena aggiunti:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Controllare":::

I dispositivi telecamera vengono visualizzati anche nell'elenco nella pagina **Dispositivi** nell'applicazione.

## <a name="configure-and-manage-the-camera"></a>Configurare e gestire la telecamera

Passare al dispositivo **camera-003** e selezionare la scheda **Gestisci**.

Usare i valori predefiniti oppure modificarli se è necessario personalizzare le proprietà del dispositivo:

**Impostazioni videocamera**

| Proprietà | Descrizione | Valore consigliato |
|-|-|-|
| Video Playback Host | Host per il visualizzatore Azure Media Player | http://localhost:8094 |

**Funzionamento e diagnostica di Analisi video live**

| Proprietà | Descrizione | Valore consigliato |
|-|-|-|
| Avvio automatico | Il rilevamento degli oggetti viene avviato al riavvio del gateway Analisi video live | Selezionata |
| Debug Telemetry | Tracce degli eventi | Facoltativo |
|Inference Timeout (sec)| Intervallo di tempo usato per determinare l'arresto delle inferenze | 20 |

**Rilevamento di oggetti tramite intelligenza artificiale**

| Proprietà | Descrizione | Valore consigliato |
|-|-|-|
| Max Inference Capture Time (sec) | Tempo di acquisizione massimo | 15 |
| Detection Classes | Stringhe, delimitate da virgole, con i tag di rilevamento. Per altre informazioni, vedere l'[elenco di tag supportati](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | truck, bus |
| Confidence Threshold | Percentuale di qualificazione per determinare se il rilevamento degli oggetti è valido | 70 |
| Inference Frame Sample Rate (fps) | [Aggiungere la descrizione qui] | 2 |

Selezionare **Salva**.

Dopo alcuni secondi verrà visualizzato il messaggio di conferma **Accettato** per ogni impostazione:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Rilevamento di oggetti":::

## <a name="start-lva-processing"></a>Avviare l'elaborazione di Analisi video live

Passare al dispositivo **camera-003** e selezionare la scheda **Comandi**.

Eseguire il comando **Start LVA Processing**.

Dopo che il comando è stato completato, visualizzare la relativa cronologia per assicurarsi che non siano presenti errori:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Comando per l'avvio dell'elaborazione di Analisi video live":::

## <a name="monitor-the-cameras"></a>Monitorare le telecamere

Passare al dispositivo **camera-003** e selezionare la scheda **Dashboard**:

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Dashboard della telecamera":::

Il riquadro **Detection Count** mostra il numero medio di rilevamenti per ogni oggetto delle classi di rilevamento selezionato durante un intervallo di rilevamento di un secondo.

Il grafico a torta **Detection Classes** mostra la percentuale di rilevamenti per ogni tipo di classe.

La sezione **Inference Event Video** è un elenco di collegamenti agli asset in Servizi multimediali di Azure che contengono i rilevamenti. Il collegamento usa il lettore host descritto nella sezione seguente.

## <a name="start-the-streaming-endpoint"></a>Avviare l'endpoint di streaming

Avviare l'endpoint di Servizi multimediali per consentire all'applicazione Media Player client di trasmettere in streaming il video registrato:

* Nel portale di Azure passare al gruppo di risorse **lva-rg**.
* Aprire la risorsa **Endpoint di streaming**.
* Nella pagina **Dettagli dell'endpoint di streaming** selezionare **Avvia**. Verrà visualizzato un avviso che indica che la fatturazione viene avviata all'avvio dell'endpoint.

## <a name="view-stored-video"></a>Visualizzare il video archiviato

Oggi non è più necessario guardare le telecamere e intervenire in caso di immagini sospette. Grazie all'assegnazione automatica di tag agli eventi e ai collegamenti diretti al video archiviato con il rilevamento derivato, gli operatori di sicurezza possono trovare gli eventi di interesse in un elenco e quindi seguire il collegamento per visualizzare il video.

È possibile usare il [lettore video Azure Media Player](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) per visualizzare il video archiviato nell'account di Servizi multimediali di Azure.

L'applicazione IoT Central archivia il video in Servizi multimediali di Azure, da dove è possibile trasmetterlo in streaming. Per riprodurre il video archiviato in Servizi multimediali di Azure, è necessario un lettore video.

Assicurarsi che **Docker** sia in esecuzione nel computer locale.

Aprire un prompt dei comandi e usare il comando seguente per eseguire il lettore video in un contenitore Docker nel computer locale. Aggiornare i segnaposto nei comandi con i valori annotati in precedenza nel file *scratchpad.txt*. I valori `amsAadClientId`, `amsAadSecret`, `amsAadTenantId`, `amsSubscriptionId` e `amsAccountName` sono stati annotati quando è stata creata l'entità servizio per l'account di Servizi multimediali:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Parametro Docker | Accesso all'API AMS (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Passare al dispositivo **camera-003** e selezionare la scheda **Dashboard**. Fare quindi clic su uno dei collegamenti ipertestuali di rilevamento degli oggetti acquisiti nel riquadro **Inference Event Video**. Il video viene visualizzato in una pagina aperta dal lettore video locale:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Frammento di video":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Modificare i dispositivi simulati nei dashboard dell'applicazione

I dashboard dell'applicazione vengono originariamente popolati con le proprietà e i dati di telemetria generati dai dispositivi simulati di IoT Central. Per configurare i riquadri per i dati di telemetria di telecamere reali o per il simulatore Live555, seguire questa procedura:

1. Passare al dashboard dell'applicazione **(Esempio) Real Camera Monitor**.
1. Selezionare **Modifica**.
1. Selezionare il riquadro **Note** ed eliminarlo.
1. Modificare il titolo del dashboard in *Real Camera Monitor*.
1. Nel riquadro **Inference Count** selezionare l'icona di configurazione.
1. Nella sezione **Configura grafico** selezionare una o più telecamere reali nel gruppo di dispositivi **LVA Edge Object Detector**.
1. Selezionare il campo di telemetria `AI Inference Interface/Inference Count`.
1. Selezionare **Aggiorna**.

1. Ripetere la procedura per i riquadri seguenti:
    1. **Detection**: il grafico a torta usa il tipo di telemetria `AI Inference Interface/Inference/entity/tag/value`.
    1. **Inference**: usa l'ultimo valore `AI Inference Interface/Inference/entity/tag/value` noto.
    1. **Confidence %** : usa l'ultimo valore `AI Inference Interface/Inference/entity/tag/confidence` noto.
    1. **Snapshot**: usa l'elemento `AI Inference Interface/Inference Image` visualizzato come immagine.
    1. **Inference Event Video**: usa l'elemento `AI Inference Interface/Inference Event Video` visualizzato come collegamento.
1. Selezionare **Salva**.

Il dashboard **Real Camera Monitor** mostra ora i valori per il dispositivo telecamera reale:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Dashboard dell'applicazione per le telecamere reali":::

## <a name="pause-processing"></a>Sospendere l'elaborazione

È possibile sospendere l'elaborazione di Analisi video live nell'applicazione:

1. Nell'applicazione passare alla pagina **Comandi** per la telecamera di rilevamento degli oggetti. Eseguire quindi il comando **Stop LVA Processing**.

1. Arrestare l'endpoint di streaming per l'account di Servizi multimediali:
    * Nel portale di Azure passare al gruppo di risorse **lva-rg**.
    * Fare clic sulla risorsa **Endpoint di streaming**.
    * Nella pagina **Dettagli dell'endpoint di streaming** selezionare **Arresta**.

## <a name="tidy-up"></a>Operazioni finali

Se l'applicazione non è più necessaria, è possibile rimuovere tutte le risorse create come indicato di seguito:

1. Nell'applicazione IoT Central passare alla pagina **Applicazione** nella sezione **Amministrazione**. Selezionare **Elimina**.
1. Nel portale di Azure eliminare il gruppo di risorse **lva-rg**.
1. Nel computer locale arrestare il contenitore Docker **amp-viewer**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come aggiungere telecamere all'applicazione IoT Central e configurarle per il rilevamento di oggetti e movimento.

Per informazioni su come personalizzare il codice sorgente per i moduli IoT Edge:

> [!div class="nextstepaction"]
> [Modificare e creare i moduli del gateway Analisi video live](./tutorial-video-analytics-build-module.md)
