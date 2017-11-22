---
title: Distribuire Analisi di flusso di Azure con Azure IoT Edge | Microsoft Docs
description: Distribuire Analisi di flusso di Azure come modulo in un dispositivo Edge
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ebda79442b8feb9f052c3ae455fa43aafb7b5a6a
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Distribuire Analisi di flusso di Azure come modulo IoT Edge: anteprima

I dispositivi IoT possono produrre grandi quantità di dati. Prima che raggiungano il cloud, talvolta, questi dati devono essere analizzati o elaborati per ridurre le dimensioni dei dati caricati o per eliminare la latenza di round trip delle informazioni dettagliate su cui è possibile intervenire.

[Analisi di flusso di Azure][azure-stream] (ASA) offre una sintassi di query particolarmente strutturata per l'analisi dei dati sia nel cloud che nei dispositivi IoT Edge. Per altre informazioni su ASA in IoT Edge, vedere la [documentazione di tale funzionalità](../stream-analytics/stream-analytics-edge.md).

Questa esercitazione illustra la creazione di un processo di Analisi di flusso di Azure e la relativa distribuzione in un dispositivo IoT Edge allo scopo di elaborare un flusso di dati di telemetria locale direttamente nel dispositivo e generare avvisi per un intervento immediato sul dispositivo.  In questa esercitazione vengono usati due moduli. Un modulo di sensore di temperatura simulato (tempSensor) che genera i dati relativi a temperature comprese tra 20 ° e 120 °, incrementati di 1 ° ogni 5 secondi, e un modulo ASA che esclude le temperature superiori a 100 °. Il modulo ASA reimposta anche quello tempSensor quando raggiunge 100 in un intervallo medio di 30 secondi.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un processo ASA per elaborare i dati nel dispositivo Edge
> * Connettere il nuovo processo ASA con altri moduli IoT Edge
> * Distribuire il processo ASA in un dispositivo IoT Edge

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT 
* Il dispositivo creato e configurato nella guida introduttiva o in Distribuire Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin].
* Docker nel dispositivo IoT Edge
    * Installare [Docker in Windows][lnk-docker-windows] e assicurarsi che sia in esecuzione.
    * [Installare Docker in Linux][lnk-docker-linux] e assicurarsi che sia in esecuzione.
* Python 2.7.x nel dispositivo IoT Edge
    * [Installare Python 2.7 in Windows][lnk-python].
    * Nella maggior parte delle distribuzioni di Linux, inclusa Ubuntu, Python 2.7 è già installato.  Usare il comando seguente per assicurarsi che pip sia installato: `sudo apt-get install python-pip`.

> [!NOTE]
> Per questa esercitazione sono necessarie la stringa di connessione del dispositivo e l'ID del dispositivo IoT Edge.

IoT Edge sfrutta i moduli precompilati IoT Edge di Azure Service per velocizzare la distribuzione e Analisi di flusso di Azure (ASA) è uno di tali moduli. È possibile creare un processo ASA dal relativo portale e quindi passare al portale dell'hub IoT per distribuirlo come modulo di Edge IoT.  

Per altre informazioni su Analisi di flusso di Azure, vedere la sezione **Panoramica** della [Documentazione dell'analisi di flusso][azure-stream].

## <a name="create-an-asa-job"></a>Creare un processo ASA

In questa sezione viene creato un processo Analisi di flusso di Azure per prelevare i dati dall'hub IoT, eseguire query sui dati di telemetria inviati dal dispositivo e inoltrare i risultati a un contenitore di archiviazione di Azure (BLOB). Per altre informazioni, vedere la sezione **Panoramica** della [Documentazione dell'analisi di flusso][azure-stream]. 

> [!NOTE]
> Per fornire un endpoint da usare come output nel processo ASA, è necessario un account di archiviazione di Azure. Nell'esempio seguente viene usato il tipo di archiviazione BLOB.  Per altre informazioni, vedere la sezione **BLOB** della [Documentazione di Archiviazione di Azure][azure-storage].

1. Nel portale di Azure passare a **Crea una risorsa -> Archiviazione**, fare clic su **Vedi tutto** e quindi su **Account di archiviazione: BLOB, File, Tabelle, Code**.

2. Immettere un nome per l'account di archiviazione e selezionare lo stesso percorso in cui è archiviato l'hub IoT. Fare clic su **Crea**. Prendere nota del nome per usarlo in seguito.

    ![nuovo account di archiviazione][1]

3. Nel portale di Azure passare all'account di archiviazione appena creato. Fare clic su **Esplora BLOB** in **Servizio BLOB**. 
1. Creare un nuovo contenitore in cui il modulo ASA può archiviare i dati. Impostare l'accesso di livello su _Contenitore_. Fare clic su **OK**.

    ![impostazioni di archiviazione][10]

1. Nel portale di Azure passare a **Crea una risorsa** > **Internet delle cose** e selezionare **Processo di Analisi di flusso**.

1. Immettere un nome, **scegliere "Edge" come ambiente di hosting** e usare gli altri valori predefiniti.  Fare clic su **Crea**.

    ![Creazione del modulo ASA][5]

2. Passare al processo creato e in **Topologia processo** selezionare **Input** e fare clic su **Aggiungi**.

3. Immettere il nome `temperature`, scegliere **Flusso dati** come tipo di origine e usare i valori predefiniti per gli altri parametri. Fare clic su **Crea**.

    ![Input di ASA][2]

    > [!NOTE]
    > Gli input aggiuntivi possono includere endpoint specifici di IoT Edge.

4. In **Topologia processo** selezionare **Output** e fare clic su **Aggiungi**.

5. Immettere il nome `alert` e usare i valori predefiniti. Fare clic su **Crea**.

    ![Output di ASA][3]

6. In **Topologia processo** selezionare **Query** e immettere quando segue:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Distribuire il processo

È ora possibile iniziare la distribuzione del processo ASA nel dispositivo IoT Edge.

1. Nel portale di Azure passare a **IoT Edge (preview)** (IoT Edge (anteprima)) nell'hub IoT e aprire il pannello *{IDdispositivo}*.

1. Selezionare **Set modules** (Imposta moduli) e quindi selezionare **Import Azure Service IoT Edge Module** (Importa modulo IoT Edge di Azure Service).

1. Selezionare la sottoscrizione e il processo Edge ASA creato. Selezionare quindi l'account di archiviazione. Fare clic su **Salva**.

    ![impostazione del modulo][6]

1. Fare clic su **Add IoT Edge Module** (Aggiungi modulo IoT Edge) per aggiungere il modulo del sensore di temperatura. Immettere _tempSensor_ per il nome e `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` per l'URL dell'immagine. Lasciare invariate le altre impostazioni e fare clic su **Salva**.

    ![modulo temperatura][11]

1. Copiare il nome del modulo ASA. Fare clic su **Next** (Avanti) per configurare le route.

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

1. Tornare alla pagina dei dettagli del dispositivo e fare clic su **Refresh** (Aggiorna).  Dovrebbe essere visualizzato il nuovo _{moduleName}_ in esecuzione insieme al modulo dell'**agente IoT Edge** e all'**hub IoT Edge**.

    ![output del modulo][7]

## <a name="view-data"></a>Visualizzare i dati

A questo punto è possibile passare al dispositivo IoT Edge e verificare l'interazione tra il modulo ASA e il modulo tempSensor.

1. Al prompt dei comandi configurare il runtime con la stringa di connessione del dispositivo IoT Edge:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Eseguire il comando per avviare il runtime:

    ```cmd/sh
    iotedgectl start  
    ```

1. Eseguire il comando per vedere i moduli in esecuzione:

    ```cmd/sh
    docker ps  
    ```

    ![output di Docker][8]

1. Eseguire il comando per visualizzare tutti i registri di sistema e i dati di metrica. Usare il nome del modulo precedente:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![log di Docker][9]

1. Nel portale di Azure in **Servizio BLOB** nell'account di archiviazione, fare clic su **Esplora BLOB**, selezionare il contenitore e selezionare il file JSON appena creato.

1. Fare clic su **Scarica** e visualizzare i risultati.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un contenitore di archiviazione di Azure e un processo Analisi di flusso per analizzare i dati del dispositivo IoT Edge.  È stato quindi caricato un modulo ASA personalizzato per spostare i dati, attraverso il flusso, dal dispositivo a un BLOB per il download.  È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può contribuire alla creazione di soluzioni per il business.

> [!div class="nextstepaction"] 
> [Distribuire un modello di Azure Machine Learning come modulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
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