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
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Distribuire Analisi di flusso di Azure come modulo IoT Edge: anteprima

I dispositivi IoT possono produrre grandi quantità di dati. Prima che raggiungano il cloud, talvolta, questi dati devono essere analizzati o elaborati per ridurre le dimensioni dei dati caricati o per eliminare la latenza di round trip delle informazioni dettagliate su cui è possibile intervenire.

IoT Edge sfrutta i moduli precompilati IoT Edge di servizi Azure per velocizzare la distribuzione e [Analisi di flusso di Azure][azure-stream] (ASA) è uno di tali moduli. È possibile creare un processo ASA dal relativo portale e quindi passare al portale dell'hub IoT per distribuirlo come modulo di Edge IoT.  

Analisi di flusso di Azure offre una sintassi di query particolarmente strutturata per l'analisi dei dati sia nel cloud che nei dispositivi IoT Edge. Per altre informazioni su ASA in IoT Edge, vedere la [documentazione di tale funzionalità](../stream-analytics/stream-analytics-edge.md).

Questa esercitazione illustra la creazione di un processo di Analisi di flusso di Azure e la relativa distribuzione in un dispositivo IoT Edge allo scopo di elaborare un flusso di dati di telemetria locale direttamente nel dispositivo e generare avvisi per un intervento immediato sul dispositivo.  In questa esercitazione vengono usati due moduli. Un modulo sensore di temperatura simulato (tempSensor) genera dati di temperatura da 20 a 120 gradi, incrementati di 1 ogni 5 secondi. Un modulo di Analisi di flusso reimposta tempSensor quando la temperatura raggiunge i 70 gradi per 30 secondi. In un ambiente di produzione, questa funzionalità potrebbe essere usata per spegnere un computer o intraprendere misure preventive quando la temperatura raggiunge livelli pericolosi. 

Si apprenderà come:

> [!div class="checklist"]
> * Creare un processo ASA per elaborare i dati nel dispositivo Edge
> * Connettere il nuovo processo ASA con altri moduli IoT Edge
> * Distribuire il processo ASA in un dispositivo IoT Edge

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT 
* Il dispositivo creato e configurato nella guida introduttiva o in Distribuire Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. È necessario conoscere la chiave di connessione del dispositivo e l'ID del dispositivo. 
* Docker in esecuzione nel dispositivo IoT Edge
    * [Installare Docker in Windows][lnk-docker-windows]
    * [Installare Docker in Linux][lnk-docker-linux]
* Python 2.7.x nel dispositivo IoT Edge
    * [Installare Python 2.7 in Windows][lnk-python].
    * Nella maggior parte delle distribuzioni di Linux, inclusa Ubuntu, Python 2.7 è già installato.  Usare il comando seguente per assicurarsi che pip sia installato: `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>Creare un processo ASA

In questa sezione viene creato un processo Analisi di flusso di Azure per prelevare i dati dall'hub IoT, eseguire query sui dati di telemetria inviati dal dispositivo e inoltrare i risultati a un contenitore di archiviazione di Azure (BLOB). Per altre informazioni, vedere la sezione **Panoramica** della [Documentazione dell'analisi di flusso][azure-stream]. 

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per fornire un endpoint da usare come output nel processo ASA, è necessario un account di archiviazione di Azure. Nell'esempio seguente viene usato il tipo di archiviazione BLOB.  Per altre informazioni, vedere la sezione **BLOB** della [Documentazione di Archiviazione di Azure][azure-storage].

1. Nel portale di Azure passare a **Crea una risorsa** e immettere `Storage account` nella barra di ricerca. Selezionare **Account di archiviazione: BLOB, File, Tabelle, Code**.

2. Immettere un nome per l'account di archiviazione e selezionare lo stesso percorso dell'hub IoT. Fare clic su **Crea**. Ricordarsi del nome per usarlo in seguito.

    ![nuovo account di archiviazione][1]

3. Passare all'account di archiviazione appena creato. Fare clic su **Esplora BLOB**. 
4. Creare un nuovo contenitore in cui il modulo ASA può archiviare i dati. Impostare l'accesso di livello su **Contenitore**. Fare clic su **OK**.

    ![impostazioni di archiviazione][10]

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel portale di Azure passare a **Crea una risorsa** > **Internet delle cose** e selezionare **Processo di Analisi di flusso**.

2. Immettere un nome, scegliere **Edge** come ambiente di hosting e usare gli altri valori predefiniti.  Fare clic su **Crea**.

    >[!NOTE]
    >Attualmente i processi di ASA su IoT Edge non sono supportati nell'area Stati Uniti occidentali 2. 

3. Passare al processo creato. Selezionare **Input** e quindi fare clic su **Aggiungi**.

4. Come alias di input immettere `temperature`, impostare il tipo di origine su **Flusso dati** e usare i valori predefiniti per gli altri parametri. Fare clic su **Crea**.

   ![Input di ASA](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Selezionare **Output** e quindi fare clic su **Aggiungi**.

6. Per l'alias di output immettere `alert` e usare i valori predefiniti per gli altri parametri. Fare clic su **Crea**.

   ![Output di ASA](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Selezionare **Query**.
8. Sostituire il testo predefinito con la query seguente:

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
9. Fare clic su **Salva**.

## <a name="deploy-the-job"></a>Distribuire il processo

È ora possibile iniziare la distribuzione del processo ASA nel dispositivo IoT Edge.

1. Nel portale di Azure passare a **IoT Edge (preview)** nell'hub IoT e aprire la pagina dei dettagli del dispositivo IoT Edge.
1. Selezionare **Set modules** (Configura i moduli).
1. Se il modulo tempSensor è stato distribuito in precedenza in questo dispositivo, la compilazione dei campi potrebbe essere automatica. In caso contrario, seguire questa procedura per aggiungere il modulo:
   1. Fare clic su **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
   1. Immettere `tempSensor` come nome e `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` per l'URI dell'immagine. 
   1. Lasciare invariate le altre impostazioni e fare clic su **Salva**.
1. Per aggiungere il processo Edge di Analisi di flusso di Azure, selezionare **Import Azure Stream Analytics IoT Edge Module** (Importa modulo di IoT Edge di Analisi di flusso di Azure).
1. Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. 
1. Selezionare la sottoscrizione e l'account di archiviazione creato. Fare clic su **Salva**.

    ![impostazione del modulo][6]

1. Copiare il nome generato automaticamente per il modulo di Analisi di flusso di Azure. 

    ![modulo temperatura][11]

1. Fare clic su **Next** (Avanti) per configurare le route.
1. Copiare quanto segue in **Routes** (Route).  Sostituire _{moduleName}_ con il nome del modulo copiato:

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

1. Fare clic su **Avanti**.

1. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Submit** (Invia).

1. Tornare alla pagina dei dettagli del dispositivo e fare clic su **Refresh** (Aggiorna).  Dovrebbe essere visualizzato il nuovo modulo di Analisi di flusso in esecuzione insieme al modulo dell'**agente IoT Edge** e all'**hub IoT Edge**.

    ![output del modulo][7]

## <a name="view-data"></a>Visualizzare i dati

A questo punto è possibile passare al dispositivo IoT Edge e verificare l'interazione tra il modulo ASA e il modulo tempSensor.

Verificare che tutti i moduli siano in esecuzione in Docker:

   ```cmd/sh
   docker ps  
   ```

   ![output di Docker][8]

Visualizzare tutti i registri di sistema e i dati di metrica. Usare il nome del modulo di Analisi di flusso:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Dovrebbe essere possibile osservare un progressivo aumento della temperatura del computer fino a raggiungere i 70 gradi per 30 secondi. Il modulo Analisi di flusso attiva quindi una reimpostazione e la temperatura del computer ritorna a 21. 

   ![log di Docker][9]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un contenitore di archiviazione di Azure e un processo Analisi di flusso per analizzare i dati del dispositivo IoT Edge.  È stato quindi caricato un modulo ASA personalizzato per spostare i dati, attraverso il flusso, dal dispositivo a un BLOB per il download.  È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può contribuire alla creazione di soluzioni per il business.

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
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/