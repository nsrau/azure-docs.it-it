---
title: Esercitazione - Distribuire processi ASA in dispositivi Azure IoT Edge | Microsoft Docs
description: In questa esercitazione Analisi di flusso di Azure viene distribuito come modulo in un dispositivo Iot Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6997d40603bd6ebf39f8797a3e354e92c04da58d
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422746"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Esercitazione: Distribuire Analisi di flusso di Azure come modulo IoT Edge (anteprima)

Molte soluzioni IoT usano servizi di analisi per ottenere informazioni sui dati quando arrivano nel cloud dai dispositivi IoT. Con Azure IoT Edge, è possibile spostare direttamente nel dispositivo la logica di [Analisi di flusso di Azure][azure-stream]. Elaborando i flussi di telemetria sul perimetro, è possibile ridurre la quantità di dati caricati e ridurre il tempo necessario per reagire a informazioni dettagliate di utilità pratica.

Azure IoT Edge e Analisi di flusso di Azure sono integrati in modo tale che è possibile creare un processo di Analisi di flusso di Azure nel portale di Azure e quindi distribuirlo come modulo di IoT Edge senza codice aggiuntivo.  

Analisi di flusso di Azure offre una sintassi di query particolarmente strutturata per l'analisi dei dati sia nel cloud che nei dispositivi IoT Edge. Per altre informazioni su Analisi di flusso di Azure in IoT Edge, vedere la [documentazione di Analisi di flusso di Azure](../stream-analytics/stream-analytics-edge.md).

Il modulo di Analisi di flusso in questa esercitazione calcola la temperatura media in una finestra mobile di 30 secondi. Quando tale media è pari a 70, il modulo invia al dispositivo un avviso con una richiesta di intervento. In questo caso, tale azione consiste nel reimpostare il sensore temperatura simulato. In un ambiente di produzione, questa funzionalità potrebbe essere usata per spegnere un computer o intraprendere misure preventive quando la temperatura raggiunge livelli pericolosi. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un processo di Analisi di flusso di Azure per elaborare i dati al limite.
> * Connettere il nuovo processo di Analisi di flusso di Azure con altri moduli IoT Edge.
> * Distribuire il processo di Analisi di flusso di Azure in un dispositivo IoT Edge dal portale di Azure.

<center>
![Diagramma dell'architettura dell'esercitazione](./media/tutorial-deploy-stream-analytics/ASATutorialDiagram.png)
</center>

>[!NOTE]
>I moduli di Analisi di flusso di Azure per IoT Edge sono in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) del livello gratuito in Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Creare un processo di Analisi di flusso di Azure

In questa sezione si crea un processo di Analisi di flusso di Azure per prelevare i dati dall'hub IoT, eseguire una query sui dati di telemetria inviati dal dispositivo e inoltrare i risultati a un contenitore di archiviazione BLOB di Azure. 

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si crea un processo di Analisi di flusso di Azure da eseguire in un dispositivo IoT Edge, è necessario archiviarlo in modo che possa essere chiamato dal dispositivo. È possibile usare un account di archiviazione di Azure esistente o crearne uno nuovo ora. 

1. Nel portale di Azure andare a **Crea una risorsa** > **Archiviazione** > **Account di archiviazione: BLOB, File, Tabelle, Code**. 

1. Specificare i valori seguenti per creare l'account di archiviazione:

   | Campo | Valore |
   | ----- | ----- |
   | NOME | Immettere un nome univoco per l'account di archiviazione. | 
   | Località | Scegliere una località vicina. |
   | Sottoscrizione | Scegliere la stessa sottoscrizione dell'hub IoT. |
   | Gruppo di risorse | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |

1. Mantenere i valori predefiniti per gli altri campi e selezionare **Crea**. 

### <a name="create-a-new-job"></a>Creare un nuovo processo

1. Nel portale di Azure andare a **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

1. Specificare i valori seguenti per creare il processo:

   | Campo | Valore |
   | ----- | ----- |
   | Nome processo | Dare un nome al processo. Ad esempio, **IoTEdgeJob** | 
   | Sottoscrizione | Scegliere la stessa sottoscrizione dell'hub IoT. |
   | Gruppo di risorse | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Località | Scegliere una località vicina. | 
   | Ambiente di hosting | Selezionare **Edge**. |
 
1. Selezionare **Create**.

### <a name="configure-your-job"></a>Configurare il processo

Dopo avere creato il processo di Analisi di flusso nel portale di Azure, è possibile configurarlo con un input, un output e una query da eseguire sui dati di cui viene eseguito il pass-through. 

Usando i tre elementi di input, di output e di query, questa sezione crea un processo che riceve i dati sulla temperatura dal dispositivo IoT Edge. Analizza tali dati in una finestra mobile di 30 secondi. Se la temperatura media in tale finestra supera i 70 gradi, viene inviato un avviso al dispositivo IoT Edge. L'origine e la destinazione dei dati verranno specificate con esattezza nella sezione successiva quando si distribuirà il processo.  

1. Passare al processo di Analisi di flusso nel portale di Azure. 

1. In **Topologia processo** selezionare **Input**, quindi **Aggiungi input del flusso**.

   ![Input di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Scegliere **Hub Edge** dall'elenco a discesa.

1. Nel riquadro **Nuovo input** inserire la **temperatura** come alias di input. 

1. Mantenere i valori predefiniti per gli altri campi e selezionare **Salva**.

1. In **Topologia processo** aprire **Output**, quindi selezionare **Aggiungi**.

   ![Output di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. Scegliere **Hub Edge** dall'elenco a discesa.

1. Nel riquadro **Nuovo output** inserire **avviso** come alias di output. 

1. Mantenere i valori predefiniti per gli altri campi e selezionare **Salva**.

1. In **Topologia processo** selezionare **Query**.

1. Sostituire il testo predefinito con la query seguente. Il codice SQL invia un comando reset all'output dell'avviso se la temperatura media della macchina in una finestra di 30 secondi raggiunge i 70 gradi. Il comando reset è stato pre-programmato nel sensore come azione che può essere eseguita. 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. Selezionare **Salva**.

### <a name="configure-iot-edge-settings"></a>Configurare le impostazioni di IoT Edge

Per preparare la distribuzione del processo di Analisi di flusso in un dispositivo IoT Edge, è necessario associare il processo a un contenitore in un account di archiviazione. Quando si inizia a distribuire il processo, la definizione del processo viene esportata nel contenitore di archiviazione. 

1. In **Configura** selezionare **Impostazioni di IoT Edge**.

1. Selezionare il proprio **account di archiviazione** dal menu a discesa.

1. Per il campo **Contenitore**, selezionare **Crea nuovo** e specificare un nome per il contenitore di archiviazione. 

1. Selezionare **Salva**. 

## <a name="deploy-the-job"></a>Distribuire il processo

È ora possibile iniziare la distribuzione del processo di Analisi di flusso di Azure nel dispositivo IoT Edge. 

In questa sezione si usa la procedura guidata **Imposta moduli** nel portale di Azure per creare un *manifesto della distribuzione*. Un manifesto della distribuzione è un file JSON che descrive tutti i moduli che verranno distribuiti in un dispositivo, i registri di contenitori che archiviano le immagini dei moduli, come i moduli devono essere gestiti e come i moduli possono comunicare tra loro. Il dispositivo IoT Edge recupera il manifesto della distribuzione dall'hub IoT, quindi usa le informazioni contenute per distribuire e configurare tutti i moduli assegnati. 

Per questa esercitazione, si distribuiscono due moduli. Il primo è **tempSensor**, un modulo che simula un sensore di temperatura e umidità. Il secondo è il processo di Analisi di flusso. Il modulo del sensore fornisce il flusso di dati che verranno analizzati dalla query del processo. 

1. Nel portale di Azure, nell'hub IoT passare a **IoT Edge** e aprire la pagina dei dettagli del dispositivo IoT Edge.

1. Selezionare **Imposta moduli**.  

1. Se il modulo tempSensor è stato distribuito in precedenza in questo dispositivo, la compilazione dei campi potrebbe essere automatica. In caso contrario, aggiungere il modulo con questa procedura:

   1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.
   1. Per il nome, digitare **tempSensor**.
   1. Per l'URI dell'immagine, immettere **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Lasciare invariate le altre impostazioni e fare clic su **Save** (Salva).

1. Aggiungere il processo Edge di Analisi di flusso di Azure seguendo questa procedura:

   1. Fare clic su **Aggiungi** e selezionare **Azure Stream Analytics Module** (Modulo di Analisi di flusso di Azure).
   1. Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. 
   1. Selezionare **Salva**.

1. Dopo la pubblicazione del processo di Analisi di flusso nel contenitore di archiviazione creato, fare clic sul nome del modulo per visualizzare la struttura di un modulo di Analisi di flusso. 

   L'URI dell'immagine punta a un'immagine standard di Analisi di flusso di Azure standard. Questa è la stessa immagine usata per ogni processo che viene distribuito in un dispositivo IoT Edge. 

   Il modulo gemello viene configurato con una proprietà desiderata denominata **ASAJobInfo**. Il valore di tale proprietà punta alla definizione del processo nel contenitore di archiviazione. Questa proprietà determina come l'immagine di Analisi di flusso viene configurata con le informazioni specifiche del processo. 

1. Chiudere la pagina del modulo.

1. Prendere nota del nome del modulo di Analisi di flusso perché sarà necessario nel passaggio successivo, quindi selezionare **Avanti** per continuare.

1. Sostituire il valore predefinito in **Route** con il codice seguente. Aggiornare tutte e tre le istanze di _{moduleName}_ con il nome del modulo di Analisi di flusso di Azure. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Le route dichiarate qui definiscono il flusso di dati attraverso il dispositivo IoT Edge. I dati di telemetria vengono inviati da tempSensor all'hub IoT e all'input **temperature** configurato nel processo di Analisi di flusso. I messaggi di output **alert** vengono inviati all'hub IoT e al modulo tempSensor per attivare il comando reset. 

1. Selezionare **Avanti**.

1. Nel passaggio **Review Deployment** (Verifica la distribuzione) fare clic su **Invia**.

1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  

    Dovrebbe essere visualizzato il nuovo modulo di Analisi di flusso in esecuzione insieme al modulo dell'agente IoT Edge e all'hub IoT Edge.

    ![Output del modulo][7]

## <a name="view-data"></a>Visualizzare i dati

È possibile a questo punto passare al dispositivo IoT Edge per verificare l'interazione tra il modulo di Analisi di flusso di Azure e il modulo tempSensor.

1. Verificare che tutti i moduli siano in esecuzione in Docker:

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
1. Visualizzare tutti i registri di sistema e i dati di metrica. Usare il nome del modulo di Analisi di flusso:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Dovrebbe essere possibile osservare un progressivo aumento della temperatura del computer fino a raggiungere i 70 gradi per 30 secondi. Il modulo di Analisi di flusso attiva quindi la reimpostazione e la temperatura del computer ritorna a 21. 

   ![Log di Docker][9]

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un processo Analisi di flusso di Azure per analizzare i dati del dispositivo IoT Edge. Questo modulo di Analisi di flusso di Azure è stato quindi caricato nel dispositivo IoT Edge per elaborare e reagire all'aumento di temperatura in locale, nonché per inviare il flusso dei dati aggregati al cloud. È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"] 
> [Distribuire un modello di Azure Machine Learning come modulo][lnk-ml-tutorial]

<!-- Images. -->
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

