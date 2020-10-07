---
title: Esercitazione - Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central
description: Questa esercitazione illustra come creare un'applicazione di analisi video in IoT Central. È possibile crearla, personalizzarla e connetterla ad altri servizi di Azure.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369570"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central

Di seguito vengono fornite informazioni per un creatore di soluzioni su come creare un'applicazione di analisi video con il modello di applicazione *Analisi video - rilevamento movimento e oggetti* di IoT Central, dispositivi Azure IoT Edge e Servizi multimediali di Azure. La soluzione usa un punto vendita al dettaglio per mostrare come soddisfare l'esigenza aziendale comune di monitorare le telecamere di sicurezza. La soluzione usa il rilevamento automatico degli oggetti in un feed video per identificare e individuare rapidamente gli eventi interessanti.

L'applicazione di esempio include due dispositivi simulati e un gateway IoT Edge. Le esercitazioni seguenti mostrano due approcci per sperimentare e comprendere le funzionalità del gateway:

* Creare il gateway IoT Edge in una macchina virtuale di Azure e connettere una telecamera simulata.
* Creare il gateway IoT Edge in un dispositivo reale come un dispositivo Intel NUC e connettere una telecamera reale.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Usare il modello di applicazione di analisi video di Azure IoT Central per creare un'applicazione per punti vendita al dettaglio
> * Personalizzare le impostazioni dell'applicazione
> * Creare un modello di dispositivo per un dispositivo gateway IoT Edge
> * Aggiungere un dispositivo gateway all'applicazione IoT Central

## <a name="prerequisites"></a>Prerequisiti

Per completare questa serie di esercitazioni, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).
* Se si usa una telecamera reale, sono necessari la connettività tra il dispositivo IoT Edge e la telecamera e il canale **RTSP (Real Time Streaming Protocol)** .

## <a name="initial-setup"></a>Configurazione iniziale

In queste esercitazioni si aggiornano e si usano diversi file di configurazione. Le versioni iniziali di questi file sono disponibili nel repository GitHub [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). Il repository include anche un file di testo denominato scratchpad che è possibile scaricare e usare per registrare i valori di configurazione dei servizi distribuiti.

Creare una cartella denominata *lva-configuration* nel computer locale per salvare copie di questi file. Fare quindi clic con il pulsante destro del mouse su ognuno dei collegamenti seguenti e scegliere **Salva con nome** per salvare il file nella cartella *lva-configuration*:

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Il repository GitHub include anche il codice sorgente per i moduli IoT Edge **LvaEdgeGatewayModule** e **lvaYolov3**. Per altre informazioni sull'uso del codice sorgente, vedere [Creare i moduli del gateway Analisi video live](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Distribuire e configurare Servizi multimediali di Azure

La soluzione usa un account di Servizi multimediali di Azure per archiviare i clip video di rilevamento degli oggetti creati dal dispositivo gateway IoT Edge.

Quando si crea l'account di Servizi multimediali:

- È necessario specificare un nome di account, una sottoscrizione di Azure, una località, un gruppo di risorse e un account di archiviazione. Creare un nuovo account di archiviazione usando le impostazioni predefinite durante la creazione dell'account di Servizi multimediali.

- Scegliere l'area **Stati Uniti orientali** come località.

- Creare un nuovo gruppo di risorse denominato *lva-rg* nell'area **Stati Uniti orientali** per gli account di archiviazione e di Servizi multimediali. Al termine delle esercitazioni, è possibile rimuovere tutte le risorse in modo semplice eliminando il gruppo di risorse *lva-rg*.

Creare l'[account di Servizi multimediali nel portale di Azure](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> In queste esercitazioni viene usata l'area **Stati Uniti orientali** per tutti gli esempi. Se lo si preferisce, è possibile usare un'area più vicina.

Prendere nota del nome dell'account di **Servizi multimediali** nel file *scratchpad.txt*.

Al termine della distribuzione, passare alla pagina **Proprietà** per l'account di **Servizi multimediali**. Prendere nota dell'**ID risorsa** nel file *scratchpad.txt* perché questo valore verrà usato in seguito durante la configurazione del modulo IoT Edge.

Configurare quindi un'entità servizio Azure Active Directory per la risorsa di Servizi multimediali. Selezionare **Accesso all'API** e quindi **Autenticazione tramite entità servizio**. Creare una nuova app di Azure Active Directory con lo stesso nome della risorsa di Servizi multimediali e creare un segreto con una descrizione *IoT Edge Access*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Configurare l'app AAD per AMS":::

Quando il segreto viene generato, scorrere verso il basso fino alla sezione **Copiare le credenziali per connettere l'applicazione dell'entità servizio.** . Selezionare quindi **JSON**. Da questa posizione è possibile copiare tutte le informazioni sulle credenziali contemporaneamente. Prendere nota di queste informazioni nel file *scratchpad.txt* perché verranno usate in seguito durante la configurazione del dispositivo IoT Edge.

> [!WARNING]
> Questa è l'unica possibilità per visualizzare e salvare il segreto. Se lo si perde, sarà necessario generare un altro segreto.

## <a name="create-the-azure-iot-central-application"></a>Creare l'applicazione Azure IoT Central

In questa sezione viene creata una nuova applicazione Azure IoT Central da un modello. Questa applicazione verrà usata in tutta la serie di esercitazioni per creare una soluzione completa.

Per creare una nuova applicazione Azure IoT Central:

1. Passare al sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral).

1. Accedere con le credenziali usate per l'accesso alla sottoscrizione di Azure.

1. Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione** nella pagina **Compila**.

1. Selezionare **Vendita al dettaglio**. Nella pagina relativa alla vendita al dettaglio sono visualizzati diversi modelli di applicazione di vendita al dettaglio.

Per creare una nuova applicazione di analisi video:

1. Selezionare il modello di applicazione **Analisi video - rilevamento movimento e oggetti**. Questo modello include i modelli per i dispositivi usati nell'esercitazione. Il modello include i dashboard di esempio che gli operatori possono usare per eseguire attività quali il monitoraggio e la gestione delle fotocamere.

1. Facoltativamente, scegliere un **nome applicazione** descrittivo. Questa applicazione si basa su un punto vendita al dettaglio fittizio denominato Northwind Traders. Per **Nome applicazione** l'esercitazione usa *Northwind Traders video analytics*.

    > [!NOTE]
    > Se si usa un nome descrittivo per **Nome applicazione**, è comunque necessario usare un valore univoco per l'**URL** dell'applicazione.

1. Se si ha una sottoscrizione di Azure, selezionare **Directory**, **Sottoscrizione di Azure** e **Stati Uniti** per **Località**. Se non si ha una sottoscrizione, è possibile abilitare la **versione di valutazione gratuita di 7 giorni** e completare le informazioni di contatto richieste. Questa esercitazione usa tre dispositivi, ovvero due telecamere e un dispositivo IoT Edge, quindi se non si usa la versione di valutazione gratuita verranno addebitati i costi per l'utilizzo.

    Per altre informazioni su directory, sottoscrizioni e località, vedere l'[argomento di avvio rapido per creare un'applicazione](../core/quick-deploy-iot-central.md).

1. Selezionare **Crea**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Configurare l'app AAD per AMS":::

### <a name="retrieve-the-configuration-data"></a>Recuperare i dati di configurazione

Più avanti in questa esercitazione quando si configura il gateway IoT Edge sono necessari alcuni dati di configurazione dell'applicazione IoT Central. Il dispositivo IoT Edge necessita di queste informazioni per la registrazione e la connessione all'applicazione.

Nella sezione **Amministrazione** selezionare **Applicazione** e prendere nota dei valori di **URL applicazione** e **ID applicazione** nel file *scratchpad.txt*:

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Configurare l'app AAD per AMS":::

Selezionare **Token API** e generare un nuovo token denominato **LVAEdgeToken** per il ruolo **Operatore**:

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Configurare l'app AAD per AMS":::

Prendere nota del token nel file *scratchpad.txt* per usarlo in seguito. Dopo la chiusura della finestra di dialogo, non sarà più possibile visualizzare il token.

Nella sezione **Amministrazione** selezionare **Connessione del dispositivo** e quindi **SAS-IoT-Devices**.

Prendere nota del valore di **Chiave primaria** per i dispositivi nel file *scratchpad.txt*. Questo *token di firma di accesso condiviso del gruppo primario* verrà usato in seguito durante la configurazione del dispositivo IoT Edge.

## <a name="edit-the-deployment-manifest"></a>Modificare il manifesto della distribuzione

La distribuzione di un modulo IoT Edge viene eseguita usando un manifesto della distribuzione. In IoT Central è possibile importare il manifesto come modello di dispositivo e quindi lasciare che IoT Central gestisca la distribuzione del modulo.

Per preparare il manifesto della distribuzione:

1. Aprire il file *deployment.amd64.json* salvato nella cartella *lva-configuration* usando un editor di testo.

1. Individuare le impostazioni `LvaEdgeGatewayModule` e modificare il nome dell'immagine come illustrato nel frammento di codice seguente:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Aggiungere il nome dell'account di Servizi multimediali nel nodo `env` nella sezione `LvaEdgeGatewayModule`. Il nome dell'account è stato annotato nel file *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Il modello non espone queste proprietà in IoT Central, quindi è necessario aggiungere i valori di configurazione di Servizi multimediali nel manifesto della distribuzione. Individuare il modulo `lvaEdge` e sostituire i segnaposto con i valori annotati nel file *scratchpad.txt* quando è stato creato l'account di Servizi multimediali.

    `azureMediaServicesArmId` è il valore di **ID risorsa** annotato nel file *scratchpad.txt* quando è stato creato l'account di Servizi multimediali.

    I valori `aadTenantId`, `aadServicePrincipalAppId` e `aadServicePrincipalSecret` sono stati annotati nel file *scratchpad.txt* quando è stata creata l'entità servizio per l'account di Servizi multimediali:

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Salvare le modifiche.

Facoltativamente, è possibile sostituire il modulo Yolov3 con il [modulo di estensione dell'intelligenza artificiale nei dispositivi perimetrali OpenVINO&trade; Model Server](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) di Intel. È possibile scaricare un manifesto della distribuzione di esempio [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Questo manifesto sostituisce le impostazioni di configurazione per il modulo `lvaYolov3` con la configurazione seguente:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Creare il dispositivo gateway Azure IoT Edge

L'applicazione Analisi video - rilevamento movimento e oggetti include un modello di dispositivo **LVA Edge Object Detector** e un modello di dispositivo **LVA Edge Motion Detection**. In questa sezione si creerà un modello di dispositivo gateway usando il manifesto della distribuzione e si aggiungerà il dispositivo gateway all'applicazione IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Creare un modello di dispositivo per LVA Edge Gateway

Per importare il manifesto della distribuzione e creare il modello di dispositivo **LVA Edge Gateway**:

1. Nell'applicazione IoT Central passare a **Modelli di dispositivo** e selezionare **+ Nuovo**.

1. Nella pagina **Selezionare il tipo di modello** selezionare il riquadro **Azure IoT Edge**. Selezionare quindi **Next: Personalizza**.

1. Nella pagina **Carica un manifesto della distribuzione di Azure IoT Edge** immettere *LVA Edge Gateway* come nome del modello e selezionare **Dispositivo gateway con dispositivi downstream**.

    Per il momento, non cercare il manifesto della distribuzione. Se lo si fa, la distribuzione guidata si aspetta un'interfaccia per ogni modulo, mentre è sufficiente esporre l'interfaccia per **LvaEdgeGatewayModule**. Il manifesto verrà caricato in un passaggio successivo.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Configurare l'app AAD per AMS":::

    Selezionare **Avanti: Review** (Avanti: Rivedi).

1. Nella pagina **Rivedi** selezionare **Crea**.

### <a name="import-the-device-capability-model"></a>Importare il modello di funzionalità di dispositivo

Il modello di dispositivo deve includere un modello di funzionalità di dispositivo. Nella pagina **LVA Edge Gateway** selezionare il riquadro **Importa modello di funzionalità**. Passare alla cartella *lva-configuration* creata in precedenza e selezionare il file *LvaEdgeGatewayDcm.json*.

Il modello di dispositivo **LVA Edge Gateway** include ora **LVA Edge Gateway Module** e tre interfacce: **Device information**, **LVA Edge Gateway Settings** e **LVA Edge Gateway Interface**.

### <a name="replace-the-manifest"></a>Sostituire il manifesto

Nella pagina **LVA Edge Gateway** selezionare **+ Sostituisci manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Configurare l'app AAD per AMS":::

Passare alla cartella *lva-configuration* e selezionare il file manifesto *deployment.amd64.json* modificato in precedenza. Selezionare **Carica**. Dopo aver completato la convalida, selezionare **Sostituisci**.

### <a name="add-relationships"></a>Aggiungere relazioni

Nel modello di dispositivo **LVA Edge Gateway**, in **Moduli/LVA Edge Gateway Module** selezionare **Relazioni**. Selezionare **+ Aggiungi relazione** e aggiungere le due relazioni seguenti:

|Nome visualizzato               |Nome          |Destinazione |
|-------------------------- |------------- |------ |
|LVA Edge Motion Detector   |Usare l'impostazione predefinita.   |LVA Edge Motion Detector Device |
|LVA Edge Object Detector   |Usare l'impostazione predefinita.   |LVA Edge Object Detector Device |

Selezionare quindi **Salva**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Configurare l'app AAD per AMS":::

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

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Configurare l'app AAD per AMS":::

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
> [Creare un'istanza di IoT Edge per l'analisi video (macchina virtuale Linux)](./tutorial-video-analytics-iot-edge-vm.md)

Se si vuole provare l'applicazione Analisi video - rilevamento movimento e oggetti usando i moduli IoT Edge in un dispositivo reale con la telecamera **ONVIF**:

> [!div class="nextstepaction"]
> [Creare un'istanza di IoT Edge per l'analisi video (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
