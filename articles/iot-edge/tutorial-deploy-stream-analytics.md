---
title: Distribuire Analisi di flusso di Azure con Azure IoT Edge | Microsoft Docs
description: Distribuire Analisi di flusso di Azure come modulo in un dispositivo Edge
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f40fb81fc03e796b906db12bf3bf6904b27b46eb
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Distribuire Analisi di flusso di Azure come modulo IoT Edge: anteprima

I dispositivi IoT possono produrre grandi quantità di dati. Per ridurre la quantità di dati caricati o per eliminare la latenza di round trip delle informazioni approfondite su cui è possibile intervenire, è talvolta necessario analizzare o elaborare i dati prima che raggiungano il cloud.

Azure IoT Edge sfrutta i moduli IoT Edge del servizio di Azure precompilati per velocizzare la distribuzione. [Analisi di flusso di Azure][azure-stream] è uno di questi moduli. È possibile creare un processo di Analisi di flusso di Azure dal relativo portale e quindi passare al portale IoT Hub di Azure per distribuirlo come modulo IoT Edge. 

Analisi di flusso di Azure offre una sintassi di query particolarmente strutturata per l'analisi dei dati sia nel cloud che nei dispositivi IoT Edge. Per altre informazioni su Analisi di flusso di Azure in IoT Edge, vedere la [documentazione di Analisi di flusso di Azure](../stream-analytics/stream-analytics-edge.md).

Questa esercitazione illustra come creare un processo di Analisi di flusso di Azure e distribuirlo in un dispositivo IoT Edge. Questa operazione consente di elaborare un flusso di dati di telemetria locale direttamente sul dispositivo e di generare avvisi che determinano un'azione immediata nel dispositivo. 

L'esercitazione presenta due moduli: 
* Un modulo di sensore di temperatura simulato (tempSensor) che genera dati di temperatura da 20 a 120 gradi, incrementati di 1 ogni 5 secondi. 
* Un modulo di Analisi di flusso di Azure che reimposta tempSensor quando la temperatura raggiunge i 70 gradi per 30 secondi. In un ambiente di produzione, questa funzionalità potrebbe essere usata per spegnere un computer o intraprendere misure preventive quando la temperatura raggiunge livelli pericolosi. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un processo di Analisi di flusso di Azure per elaborare i dati al limite.
> * Connettere il nuovo processo di Analisi di flusso di Azure con altri moduli IoT Edge.
> * Distribuire il processo di Analisi di flusso di Azure in un dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT. 
* Il dispositivo creato e configurato nella guida introduttiva o negli articoli sulla distribuzione di Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] o in [Linux][lnk-tutorial1-lin]. È necessario conoscere la chiave di connessione del dispositivo e l'ID del dispositivo. 
* Docker in esecuzione nel dispositivo IoT Edge.
    * [Installare Docker in Windows][lnk-docker-windows].
    * [Installare Docker in Linux][lnk-docker-linux].
* Python 2.7.x nel dispositivo IoT Edge.
    * [Installare Python 2.7 in Windows][lnk-python].
    * Nella maggior parte delle distribuzioni di Linux, inclusa Ubuntu, Python 2.7 è già installato. Per assicurarsi che pip sia installato, usare il comando seguente: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Creare un processo di Analisi di flusso di Azure

In questa sezione si crea un processo di Analisi di flusso di Azure per prelevare i dati dall'hub IoT, eseguire una query sui dati di telemetria inviati dal dispositivo e inoltrare i risultati a un contenitore di archiviazione BLOB di Azure. Per altre informazioni, vedere la sezione "Panoramica" della [documentazione di Analisi di flusso][azure-stream]. 

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per specificare un endpoint da usare come output nel processo di Analisi di flusso di Azure, è necessario un account di Archiviazione di Azure. L'esempio in questa sezione usa il tipo di archiviazione BLOB. Per altre informazioni, vedere la sezione "BLOB" della [documentazione di Archiviazione di Azure][azure-storage].

1. Nel portale di Azure passare a **Crea una risorsa**, immettere **account di archiviazione** nella casella di ricerca e quindi selezionare **Account di archiviazione - BLOB, file, tabella e coda**.

2. Nel riquadro **Crea account di archiviazione** immettere un nome per l'account di archiviazione, selezionare la stessa posizione in cui è archiviato l'hub IoT e quindi selezionare **Crea**. Prendere nota del nome per usarlo in seguito.

    ![Creare un account di archiviazione][1]

3. Passare all'account di archiviazione appena creato e quindi selezionare **Esplora BLOB**. 

4. Creare un nuovo contenitore per il modulo di Analisi di flusso di Azure in cui archiviare i dati, impostare il livello di accesso su **Contenitore**, quindi selezionare **OK**.

    ![Impostazioni di archiviazione][10]

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel portale di Azure passare a **Crea una risorsa** > **Internet delle cose** e selezionare **Processo di Analisi di flusso**.

2. Nel riquadro **Nuovo processo di Analisi di flusso** eseguire queste operazioni:

    a. Nella casella **Nome processo** digitare un nome di processo.
    
    b. In **Ambiente host** selezionare **Edge**.
    
    c. Nei campi rimanenti usare i valori predefiniti.

    > [!NOTE]
    > I processi di Analisi di flusso di Azure in IoT Edge non sono al momento supportati nell'area Stati Uniti occidentali 2. 

3. Selezionare **Create**.

4. Nel processo creato, in **Topologia processo** selezionare **Input** e fare clic su **Aggiungi**.

5. Nel riquadro **Nuovo input** eseguire queste operazioni:

    a. Nella casella **Alias di input** immettere **temperatura**.
    
    b. Nella casella **Tipo di origine** selezionare **Flusso dati**.
    
    c. Nei campi rimanenti usare i valori predefiniti.

   ![Input di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Selezionare **Create**.

7. In **Topologia processo** selezionare **Output** e fare clic su **Aggiungi**.

8. Nel riquadro **Nuovo output** eseguire queste operazioni:

    a. Nella casella **Alias di output** digitare **avviso**.
    
    b. Nei campi rimanenti usare i valori predefiniti. 
    
    c. Selezionare **Create**.

   ![Output di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. In **Topologia processo** selezionare **Query** e quindi sostituire il testo predefinito con la query seguente:

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

10. Selezionare **Salva**.

## <a name="deploy-the-job"></a>Distribuire il processo

È ora possibile iniziare la distribuzione del processo di Analisi di flusso di Azure nel dispositivo IoT Edge.

1. Nel portale di Azure, nell'hub IoT passare a **IoT Edge (preview)** (IoT Edge (anteprima)) e aprire la pagina dei dettagli del dispositivo IoT Edge.

2. Selezionare **Set modules** (Configura i moduli).  
    Se il modulo tempSensor è stato distribuito in precedenza in questo dispositivo, la compilazione dei campi potrebbe essere automatica. In caso contrario, aggiungere il modulo effettuando le operazioni seguenti:

   a. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).

   b. Per il nome, digitare **tempSensor**.
    
   c. Per l'URI dell'immagine, immettere **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**. 

   d. Lasciare invariate le altre impostazioni.
   
   e. Selezionare **Salva**.

3. Per aggiungere il processo Edge di Analisi di flusso di Azure, selezionare **Import Azure Stream Analytics IoT Edge Module** (Importa modulo IoT Edge di Analisi di flusso di Azure).

4. Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. 

5. Selezionare la sottoscrizione e l'account di archiviazione creato, quindi fare clic su **Salva**.

    ![Impostare il modulo][6]

6. Copiare il nome del modulo di Analisi di flusso di Azure. 

    ![Modulo Temperatura][11]

7. Per configurare le route, selezionare **Avanti**.

8. Copiare il codice seguente in **Route**. Sostituire _{moduleName}_ con il nome del modulo copiato:

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

9. Selezionare **Avanti**.

10. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Invia**.

11. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  
    Dovrebbe essere visualizzato il nuovo modulo di Analisi di flusso in esecuzione insieme al modulo dell'agente IoT Edge e all'hub IoT Edge.

    ![Output del modulo][7]

## <a name="view-data"></a>Visualizzare i dati

È possibile a questo punto passare al dispositivo IoT Edge per verificare l'interazione tra il modulo di Analisi di flusso di Azure e il modulo tempSensor.

1. Verificare che tutti i moduli siano in esecuzione in Docker:

   ```cmd/sh
   docker ps  
   ```

   ![Output di Docker][8]

2. Visualizzare tutti i registri di sistema e i dati di metrica. Usare il nome del modulo di Analisi di flusso:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Dovrebbe essere possibile osservare un progressivo aumento della temperatura del computer fino a raggiungere i 70 gradi per 30 secondi. Il modulo di Analisi di flusso attiva quindi la reimpostazione e la temperatura del computer ritorna a 21. 

   ![Log di Docker][9]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati un contenitore di archiviazione di Azure e un processo Analisi di flusso per analizzare i dati del dispositivo IoT Edge. È stato quindi caricato un modulo di Analisi di flusso di Azure personalizzato per spostare i dati, attraverso il flusso, dal dispositivo a un BLOB per il download. È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"] 
> [Distribuire un modello di Azure Machine Learning come modulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
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
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
