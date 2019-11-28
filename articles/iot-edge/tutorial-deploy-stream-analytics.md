---
title: 'Esercitazione: Analisi di flusso sul perimetro - Azure IoT Edge'
description: In questa esercitazione Analisi di flusso di Azure viene distribuito come modulo in un dispositivo IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452541"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Esercitazione: Distribuire Analisi di flusso di Azure come modulo IoT Edge

Molte soluzioni IoT usano servizi di analisi per ottenere informazioni sui dati quando arrivano nel cloud dai dispositivi IoT. Con Azure IoT Edge, è possibile spostare direttamente nel dispositivo la logica di [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/). Elaborando i flussi di telemetria sul perimetro, è possibile ridurre la quantità di dati caricati e ridurre il tempo necessario per reagire a informazioni dettagliate di utilità pratica.

Azure IoT Edge e Analisi di flusso di Azure sono integrati in modo tale che è possibile creare un processo di Analisi di flusso di Azure nel portale di Azure e quindi distribuirlo come modulo di IoT Edge senza codice aggiuntivo.  

Analisi di flusso di Azure offre una sintassi di query particolarmente strutturata per l'analisi dei dati sia nel cloud che nei dispositivi IoT Edge. Per altre informazioni, vedere [Documentazione di Analisi di flusso di Azure](../stream-analytics/stream-analytics-edge.md).

Il modulo di Analisi di flusso in questa esercitazione calcola la temperatura media in una finestra mobile di 30 secondi. Quando tale media è pari a 70, il modulo invia al dispositivo un avviso con una richiesta di intervento. In questo caso, tale azione consiste nel reimpostare il sensore temperatura simulato. In un ambiente di produzione, questa funzionalità potrebbe essere usata per spegnere un computer o intraprendere misure preventive quando la temperatura raggiunge livelli pericolosi. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un processo di Analisi di flusso di Azure per elaborare i dati al limite.
> * Connettere il nuovo processo di Analisi di flusso di Azure con altri moduli IoT Edge.
> * Distribuire il processo di Analisi di flusso di Azure in un dispositivo IoT Edge dal portale di Azure.

<center>

![Diagramma - Architettura dell'esercitazione, staging e distribuzione di un processo di Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* è possibile usare una macchina virtuale di Azure come dispositivo IoT Edge seguendo la procedura illustrata nell'argomento di avvio rapido per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Creare un processo di Analisi di flusso di Azure

In questa sezione si crea un processo di Analisi di flusso di Azure che eseguirà i passaggi seguenti:
* Ricevere i dati dal dispositivo IoT Edge.
* Eseguire query sui dati di telemetria per i valori non compresi in un intervallo impostato.
* Eseguire un'azione sul dispositivo IoT Edge in base ai risultati della query. 

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si crea un processo di Analisi di flusso di Azure da eseguire in un dispositivo IoT Edge, è necessario archiviarlo in modo che possa essere chiamato dal dispositivo. È possibile usare un account di archiviazione di Azure esistente o crearne uno nuovo ora. 

1. Nel portale di Azure fare clic su **Crea una risorsa** > **Archiviazione** > **Account di archiviazione**. 

1. Specificare i valori seguenti per creare l'account di archiviazione:

   | Campo | Valore |
   | ----- | ----- |
   | Subscription | Scegliere la stessa sottoscrizione dell'hub IoT. |
   | Resource group | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | NOME | Immettere un nome univoco per l'account di archiviazione. | 
   | Location | Scegliere una località vicina. |


1. Mantenere i valori predefiniti per gli altri campi e selezionare **Rivedi e crea**.

1. Rivedere le impostazioni, quindi selezionare **Crea**.

### <a name="create-a-new-job"></a>Creare un nuovo processo

1. Nel portale di Azure andare a **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

1. Specificare i valori seguenti per creare il processo:

   | Campo | Valore |
   | ----- | ----- |
   | Nome processo | Dare un nome al processo. Ad esempio, **IoTEdgeJob** | 
   | Subscription | Scegliere la stessa sottoscrizione dell'hub IoT. |
   | Resource group | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Location | Scegliere una località vicina. | 
   | Ambiente di hosting | Selezionare **Edge**. |
 
1. Selezionare **Create** (Crea).

### <a name="configure-your-job"></a>Configurare il processo

Dopo avere creato il processo di Analisi di flusso nel portale di Azure, è possibile configurarlo con un input, un output e una query da eseguire sui dati di cui viene eseguito il pass-through. 

Usando i tre elementi di input, di output e di query, questa sezione crea un processo che riceve i dati sulla temperatura dal dispositivo IoT Edge. Analizza tali dati in una finestra mobile di 30 secondi. Se la temperatura media in tale finestra supera i 70 gradi, viene inviato un avviso al dispositivo IoT Edge. L'origine e la destinazione dei dati verranno specificate con esattezza nella sezione successiva quando si distribuirà il processo.  

1. Passare al processo di Analisi di flusso nel portale di Azure. 

1. In **Topologia processo** selezionare **Input**, quindi **Aggiungi input del flusso**.

   ![Aggiunta input in Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Scegliere **Hub Edge** dall'elenco a discesa.

1. Nel riquadro **Nuovo input** inserire la **temperatura** come alias di input. 

1. Mantenere i valori predefiniti per gli altri campi e selezionare **Salva**.

1. In **Topologia processo** aprire **Output**, quindi selezionare **Aggiungi**.

   ![Aggiunta output in Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/asa-output.png)

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

1. Sotto **Configura**, selezionare **Impostazioni account di archiviazione**, quindi selezionare **Aggiungi account di archiviazione**. 

   ![Aggiunta account di archiviazione in Analisi di flusso di Azure](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Selezionare l' **account di archiviazione** creato all'inizio di questa esercitazione dal menu a discesa.

1. Per il campo **Contenitore**, selezionare **Crea nuovo** e specificare un nome per il contenitore di archiviazione. 

1. Selezionare **Salva**. 

## <a name="deploy-the-job"></a>Distribuire il processo

È ora possibile iniziare la distribuzione del processo di Analisi di flusso di Azure nel dispositivo IoT Edge. 

In questa sezione si usa la procedura guidata **Imposta moduli** nel portale di Azure per creare un *manifesto della distribuzione*. Un manifesto della distribuzione è un file JSON che descrive tutti i moduli che verranno distribuiti in un dispositivo, i registri di contenitori che archiviano le immagini dei moduli, come i moduli devono essere gestiti e come i moduli possono comunicare tra loro. Il dispositivo IoT Edge recupera il manifesto della distribuzione dall'hub IoT, quindi usa le informazioni contenute per distribuire e configurare tutti i moduli assegnati. 

Per questa esercitazione, si distribuiscono due moduli. Il primo è **SimulatedTemperatureSensor**, un modulo che simula un sensore di temperatura e umidità. Il secondo è il processo di Analisi di flusso. Il modulo del sensore fornisce il flusso di dati che verranno analizzati dalla query del processo. 

1. Nel portale di Azure passare all'hub IoT.

1. Passare a **IoT Edge** e aprire la pagina dei dettagli del dispositivo IoT Edge.

1. Selezionare **Imposta moduli**.  

1. Se il modulo SimulatedTemperatureSensor è stato distribuito in precedenza in questo dispositivo, la compilazione dei campi potrebbe essere automatica. In caso contrario, aggiungere il modulo con questa procedura:

   1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.
   1. Per il nome digitare **SimulatedTemperatureSensor.**
   1. Per l'URI dell'immagine, immettere **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Lasciare invariate le altre impostazioni e fare clic su **Save** (Salva).

1. Aggiungere il processo Edge di Analisi di flusso di Azure seguendo questa procedura:

   1. Fare clic su **Aggiungi** e selezionare **Azure Stream Analytics Module** (Modulo di Analisi di flusso di Azure).
   1. Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. 
   1. Selezionare **Salva**.

   Dopo aver salvato le modifiche, i dettagli del processo di analisi di flusso vengono pubblicati nel contenitore di archiviazione creato. 

1. Quando il modulo di analisi di flusso viene aggiunto all'elenco dei moduli, selezionare **Configura** per verificarne la struttura. 

   L'URI dell'immagine punta a un'immagine standard di Analisi di flusso di Azure standard. Questa stessa immagine viene usata per ogni modulo di analisi di flusso che viene distribuito in un dispositivo IoT Edge. 

   Il modulo gemello viene configurato con una proprietà desiderata denominata **ASAJobInfo**. Il valore di tale proprietà punta alla definizione del processo nel contenitore di archiviazione. Questa proprietà determina come l'immagine di Analisi di flusso viene configurata con i dettagli specifici del processo. 

   Per impostazione predefinita, il modulo di analisi di flusso prende lo stesso nome del processo su cui si basa. Se lo si desidera, è possibile modificare il nome del modulo in questa pagina, ma non è necessario. 

1. Chiudere la pagina di configurazione del modulo.

1. Prendere nota del nome del modulo di Analisi di flusso perché sarà necessario nel passaggio successivo, quindi selezionare **Avanti** per continuare.

1. Sostituire il valore predefinito in **Route** con il codice seguente. Aggiornare tutte e tre le istanze di _{moduleName}_ con il nome del modulo di Analisi di flusso di Azure. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Le route dichiarate qui definiscono il flusso di dati attraverso il dispositivo IoT Edge. I dati di telemetria vengono inviati dal modulo SimulatedTemperatureSensor all'hub IoT e all'input **temperature** configurato nel processo di Analisi di flusso. I messaggi di output **alert** vengono inviati all'hub IoT e al modulo SimulatedTemperatureSensor per attivare il comando reset. 

1. Selezionare **Avanti**.

1. Nel passaggio **Rivedi distribuzione** è possibile vedere come le informazioni fornite nella procedura guidata vengono convertite in un manifesto della distribuzione JSON. Al termine della revisione del manifesto, selezionare **Invia**.

1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  

    Dovrebbe essere visualizzato il nuovo modulo di Analisi di flusso in esecuzione insieme ai moduli dell'agente IoT Edge e dell'hub IoT Edge. Potrebbero essere necessari alcuni minuti prima che le informazioni raggiungano il dispositivo IoT Edge e quindi per l'avvio dei nuovi moduli. Se i moduli non vengono eseguiti immediatamente, continuare ad aggiornare la pagina.

    ![Moduli SimulatedTemperatureSensor e ASA visualizzati dal dispositivo](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Visualizzare i dati

È possibile a questo punto passare al dispositivo IoT Edge per verificare l'interazione tra il modulo di Analisi di flusso di Azure e il modulo SimulatedTemperatureSensor.

1. Verificare che tutti i moduli siano in esecuzione in Docker:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Visualizzare tutti i log di sistema e i dati di metrica. Usare il nome del modulo di Analisi di flusso:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. È possibile osservare l'effetto del comando reset sul modulo SimulatedTemperatureSensor visualizzando i log del sensore:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   È possibile osservare un progressivo aumento della temperatura del computer fino a raggiungere i 70 gradi per 30 secondi. Il modulo di Analisi di flusso attiva quindi la reimpostazione e la temperatura del computer ritorna a 21. 

   ![Reimpostazione dell'output del comando nei log del modulo](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un processo Analisi di flusso di Azure per analizzare i dati del dispositivo IoT Edge. Questo modulo di Analisi di flusso di Azure è stato quindi caricato nel dispositivo IoT Edge per elaborare e reagire all'aumento di temperatura in locale, nonché per inviare il flusso dei dati aggregati al cloud. È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"] 
> [Distribuire un modello di Azure Machine Learning come modulo](tutorial-deploy-machine-learning.md)
