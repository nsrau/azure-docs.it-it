---
title: Esercitazione - Distribuire processi ASA in dispositivi Azure IoT Edge | Microsoft Docs
description: Distribuire Analisi di flusso di Azure come modulo in un dispositivo Iot Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0790f504c978b4302812cffc9b655e817c156da3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38540173"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Esercitazione: Distribuire Analisi di flusso di Azure come modulo IoT Edge - anteprima

Molte soluzioni IoT usano servizi di analisi per ottenere informazioni sui dati quando arrivano nel cloud dai dispositivi IoT. Con Azure IoT Edge, è possibile spostare direttamente nel dispositivo la logica di [Analisi di flusso di Azure][azure-stream]. Elaborando i flussi di telemetria sul perimetro, è possibile ridurre la quantità di dati caricati e ridurre il tempo necessario per reagire a informazioni dettagliate di utilità pratica.

Azure IoT Edge e Analisi di flusso di Azure sono integrati in modo tale che è possibile creare un processo di Analisi di flusso di Azure nel portale di Azure e quindi distribuirlo come modulo di IoT Edge senza codice aggiuntivo.  

Analisi di flusso di Azure offre una sintassi di query particolarmente strutturata per l'analisi dei dati sia nel cloud che nei dispositivi IoT Edge. Per altre informazioni su Analisi di flusso di Azure in IoT Edge, vedere la [documentazione di Analisi di flusso di Azure](../stream-analytics/stream-analytics-edge.md).

Il modulo di Analisi di flusso in questa esercitazione calcola la temperatura media in una finestra mobile di 30 secondi. Quando tale media è pari a 70, il modulo invia al dispositivo un avviso con una richiesta di intervento. In questo caso, tale azione consiste nel reimpostare il sensore temperatura simulato. In un ambiente di produzione, questa funzionalità potrebbe essere usata per spegnere un computer o intraprendere misure preventive quando la temperatura raggiunge livelli pericolosi. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un processo di Analisi di flusso di Azure per elaborare i dati al limite.
> * Connettere il nuovo processo di Analisi di flusso di Azure con altri moduli IoT Edge.
> * Distribuire il processo di Analisi di flusso di Azure in un dispositivo IoT Edge dal portale di Azure.

>[!NOTE]
>I moduli di Analisi di flusso di Azure per IoT Edge sono in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

* Un hub IoT
* Il dispositivo IoT Edge creato e configurato nella guida introduttiva per [Windows][lnk-quickstart-win] o [Linux][lnk-quickstart-lin]. 

## <a name="create-an-azure-stream-analytics-job"></a>Creare un processo di Analisi di flusso di Azure

In questa sezione si crea un processo di Analisi di flusso di Azure per prelevare i dati dall'hub IoT, eseguire una query sui dati di telemetria inviati dal dispositivo e inoltrare i risultati a un contenitore di archiviazione BLOB di Azure. Per altre informazioni, vedere la sezione "Panoramica" della [documentazione di Analisi di flusso][azure-stream]. 

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

È necessario un account di archiviazione di Azure per i processi di Analisi di flusso di Azure, che funga da endpoint per l'output del processo. L'esempio in questa sezione usa il tipo di archiviazione BLOB. Per altre informazioni, vedere la sezione "BLOB" della [documentazione di Archiviazione di Azure][azure-storage].

1. Nel portale di Azure passare a **Crea una risorsa**, immettere **account di archiviazione** nella casella di ricerca e quindi selezionare **Account di archiviazione - BLOB, file, tabella e coda**.

2. Nel riquadro **Crea account di archiviazione** immettere un nome per l'account di archiviazione, selezionare la stessa posizione in cui è archiviato l'hub IoT, selezionare lo stesso gruppo di risorse di hub IoT e quindi selezionare **Crea**. Prendere nota del nome per usarlo in seguito.

    ![Creare un account di archiviazione][1]


### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel portale di Azure passare a **Crea una risorsa** > **Internet delle cose** e selezionare **Processo di Analisi di flusso**.

2. Nel riquadro **Nuovo processo di Analisi di flusso** seguire questa procedura:

   1. Nella casella **Nome processo** digitare un nome di processo.
   
   2. Usare lo stesso **gruppo di risorse** e la stessa **località** dell'hub IoT. 

      > [!NOTE]
      > I processi di Analisi di flusso di Azure in IoT Edge non sono al momento supportati nell'area Stati Uniti occidentali 2. 

   3. In **Ambiente host** selezionare **Edge**.
    
3. Selezionare **Create**.

4. Nel processo creato, in **Topologia processo**, aprire **Input**.

   ![Input di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Selezionare **Aggiungi input del flusso**, quindi selezionare **Hub Edge**.

6. Nel riquadro **Nuovo input** inserire la **temperatura** come alias di input. 

7. Selezionare **Salva**.

8. In **Topologia processo** aprire **Output**.

   ![Output di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. Selezionare **Aggiungi**, quindi selezionare **Hub Edge**.

10. Nel riquadro **Nuovo output** inserire **avviso** come alias di output. 

11. Selezionare **Salva**.

12. In **Topologia processo** selezionare **Query** e quindi sostituire il testo predefinito con la query seguente che crea un avviso se la temperatura media del computer raggiunge i 70 gradi in un arco di 30 secondi:

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

13. Selezionare **Salva**.

14. In **Configura** selezionare **Impostazioni di IoT Edge**.

15. Selezionare il proprio **account di archiviazione** dal menu a discesa.

16. Per il campo **Contenitore**, selezionare **Crea nuovo** e specificare un nome per il contenitore di archiviazione. 

17. Selezionare **Salva**. 


## <a name="deploy-the-job"></a>Distribuire il processo

È ora possibile iniziare la distribuzione del processo di Analisi di flusso di Azure nel dispositivo IoT Edge.

1. Nel portale di Azure, nell'hub IoT passare a **IoT Edge** e aprire la pagina dei dettagli del dispositivo IoT Edge.

2. Selezionare **Set modules** (Configura i moduli).  

   Se il modulo tempSensor è stato distribuito in precedenza in questo dispositivo, la compilazione dei campi potrebbe essere automatica. In caso contrario, aggiungere il modulo seguendo questa procedura:

   1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.
   2. Per il nome, digitare **tempSensor**.
   3. Per l'URI dell'immagine, immettere **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   4. Lasciare invariate le altre impostazioni.
   5. Selezionare **Salva**.

3. Aggiungere il processo Edge di Analisi di flusso di Azure seguendo questa procedura:

   1. Fare clic su **Aggiungi** e selezionare **Azure Stream Analytics Module** (Modulo di Analisi di flusso di Azure).
   2. Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. 
   3. Selezionare **Salva**.

4. Selezionare **Avanti**.

5. Sostituire il valore predefinito in **Route** con il codice seguente. Aggiornare _{moduleName}_ con il nome del modulo di Analisi di flusso di Azure. Il modulo deve avere lo stesso nome del processo da cui è stato creato. 

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

6. Selezionare **Avanti**.

7. Nel passaggio **Review Deployment** (Verifica la distribuzione) fare clic su **Invia**.

8. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  

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
2. Visualizzare tutti i registri di sistema e i dati di metrica. Usare il nome del modulo di Analisi di flusso:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Dovrebbe essere possibile osservare un progressivo aumento della temperatura del computer fino a raggiungere i 70 gradi per 30 secondi. Il modulo di Analisi di flusso attiva quindi la reimpostazione e la temperatura del computer ritorna a 21. 

   ![Log di Docker][9]

## <a name="clean-up-resources"></a>Pulire le risorse 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Se si continua con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni già create e riutilizzarle.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure create in questo articolo per evitare addebiti. 

> [!IMPORTANT]
> L'eliminazione delle risorse di Azure e del gruppo di risorse è irreversibile. Dopo l'eliminazione, il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare solo l'hub IoT, eseguire il comando seguente usando il nome dell'hub e il nome del gruppo di risorse:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Per eliminare l'intero gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse che contiene l'hub IoT. 

3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un processo Analisi di flusso di Azure per analizzare i dati del dispositivo IoT Edge. Questo modulo di Analisi di flusso di Azure è stato quindi caricato nel dispositivo IoT Edge per elaborare e reagire all'aumento di temperatura in locale, nonché per inviare il flusso dei dati aggregati al cloud. È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"] 
> [Distribuire un modello di Azure Machine Learning come modulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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

