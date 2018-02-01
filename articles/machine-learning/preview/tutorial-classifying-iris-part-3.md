---
title: Distribuire un modello per i servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. Questa terza parte illustra il modello di distribuzione.
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/29/2017
ms.openlocfilehash: 97cd46819a4547ec743270871bcb6b4eef3eb365
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="classify-iris-part-3-deploy-a-model"></a>Classificare i dati Iris - Parte 3: Distribuire un modello
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata end-to-end per i data scientist professionisti. Consente ai data scientist di preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la terza di una serie in tre parti. In questa parte dell'esercitazione si usano i servizi di Azure Machine Learning (anteprima) per:

> [!div class="checklist"]
> * Individuare il file di modello.
> * Generare uno script di assegnazione dei punteggi e un file di schema.
> * Preparare l'ambiente.
> * Creare un servizio Web in tempo reale.
> * Eseguire il servizio Web in tempo reale.
> * Esaminare i dati del BLOB di output. 

 Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/iris_flower_data_set). Gli screenshot sono specifici di Windows, ma l'esperienza in Mac OS è quasi identica.

## <a name="prerequisites"></a>prerequisiti
Completare le prime due parti di questa serie di esercitazioni:

   * Seguire l'[esercitazione sulla preparazione dei dati](tutorial-classifying-iris-part-1.md) per creare le risorse di Machine Learning e installare l'applicazione Azure Machine Learning Workbench.

   * Seguire l'[esercitazione sulla creazione di un modello](tutorial-classifying-iris-part-2.md) per creare un modello di regressione logistica in Azure Machine Learning.

È necessario un motore Docker installato e in esecuzione nell'ambiente locale. In alternativa, è possibile eseguire la distribuzione in un cluster del servizio contenitore di Azure in Azure.

## <a name="download-the-model-pickle-file"></a>Scaricare il file pickle del modello
Nella parte precedente dell'esercitazione, lo script **iris_sklearn.py** è stato eseguito in locale in Machine Learning Workbench. Tale azione ha serializzato il modello di regressione logistica usando il diffuso pacchetto di serializzazione degli oggetti Python [pickle](https://docs.python.org/2/library/pickle.html). 

1. Aprire l'applicazione Machine Learning Workbench, quindi aprire il progetto **myIris** creato nella parte precedente di questa serie di esercitazioni.

2. Dopo che il progetto è stato aperto, selezionare il pulsante **File** (icona della cartella) nel riquadro a sinistra per aprire l'elenco file nella cartella del progetto.

3. Selezionare il file **iris_sklearn.py**. Il codice Python verrà aperto in una nuova scheda dell'editor di testo nel workbench.

4. Esaminare il file **iris_sklearn.py** per individuare dove è stato generato il file pickle. Usare CTRL+F per aprire la finestra di dialogo di **ricerca** e quindi trovare la parola **pickle** nel codice Python.

   Questo frammento di codice illustra come è stato generato il file di output ottenuto con pickle. Il file pickle di output è denominato **model.pkl** nel disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Individuare il file pickle del modello nei file di output di un'esecuzione precedente.
   
   Quando è stato eseguito lo script **iris_sklearn.py**, il file di modello è stato scritto nella cartella **outputs** con il nome **model.pkl**. Questa cartella risiede nell'ambiente di esecuzione scelto per eseguire lo script, non nella cartella di progetto locale. 
   
   - Per individuare il file, selezionare il pulsante **Runs** (Esecuzioni) (icona a forma di orologio) nel riquadro sinistro per aprire l'elenco **All Runs** (Tutte le esecuzioni).  
   - Verrà aperta la scheda **All Runs** (Tutte le esecuzioni). Nella tabella delle esecuzioni selezionare un'esecuzione recente con destinazione **local** e nome dello script **iris_sklearn.py**. 
   - Verrà visualizzato il riquadro **Run Properties** (Proprietà esecuzione). Si noti la sezione **Outputs** (Output) in alto a destra nella pagina. 
   - Per scaricare il file pickle, selezionare la casella di controllo accanto al file **model.pkl** e quindi fare clic sul pulsante **Download** (Scarica). Salvarlo nella radice della cartella di progetto. Il file sarà necessario nei prossimi passaggi.

   ![Scaricare il file pickle](media/tutorial-classifying-iris/download_model.png)

   Per altre informazioni sulla cartella `outputs`, vedere l'articolo su [come leggere e scrivere file di dati di grandi dimensioni](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Generare lo script di assegnazione dei punteggi e il file di schema
Per distribuire il servizio Web insieme al file di modello, sono necessari anche uno script di assegnazione dei punteggi e, facoltativamente, uno schema per i dati di input del servizio Web. Lo script di assegnazione dei punteggi carica il file **model.pkl** dalla cartella corrente e lo usa per generare una nuova classe stimata di Iris.  

1. Aprire l'applicazione Azure Machine Learning Workbench e quindi aprire il progetto **myIris** creato nella parte precedente di questa serie di esercitazioni.

2. Dopo che il progetto è stato aperto, selezionare il pulsante **File** (icona della cartella) nel riquadro a sinistra per aprire l'elenco file nella cartella del progetto.

3. Selezionare il file **score_iris.py**. Verrà aperto lo script Python. Questo file viene usato come file di assegnazione dei punteggi.

   ![File di assegnazione dei punteggi](media/tutorial-classifying-iris/model_data_collection.png)

4. Per ottenere il file di schema, eseguire lo script. Selezionare l'ambiente **local** (locale) e lo script **score_iris.py** nella barra dei comandi e quindi fare clic sul pulsante **Run** (Esegui). 

5. Questo script crea un file JSON nella sezione **Outputs**, in cui viene acquisito lo schema di dati di input richiesto dal modello.

6. Si noti il riquadro **Jobs** (Processi) sul lato destro del riquadro **Project Dashboard** (Dashboard progetto). Attendere che per il processo **score_iris.py** più recente venga visualizzato lo stato **Completed** (Completato) in verde. Fare quindi clic sul collegamento ipertestuale **score_iris.py [1]** per l'esecuzione più recente del processo per visualizzare i dettagli dell'esecuzione di **score_iris.py**. 

7. Nella sezione **Outputs** (Output) del riquadro **Run Properties** (Proprietà esecuzione) selezionare il file **service_schema.json** appena creato.  Selezionare la casella di controllo accanto al nome del file e quindi fare clic su **Download** (Scarica). Salvare il file nella cartella radice del progetto.

8. Tornare alla scheda precedente in cui è stato aperto lo script **score_iris.py**. Usando la raccolta dati è possibile acquisire gli input del modello e le stime dal servizio Web. I passaggi seguenti sono di particolare interesse per la raccolta dati.

9. Esaminare il codice nella parte superiore della classe di importazione file **ModelDataCollector** perché contiene la funzionalità di raccolta dati del modello:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Esaminare le righe di codice seguenti nella funzione **init()** che crea un'istanza di **ModelDataCollector**:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Esaminare le righe di codice seguenti nella funzione **run(input_df)** che raccoglie i dati di input e di stima:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

A questo punto è possibile preparare l'ambiente per rendere operativo il modello.



## <a name="prepare-to-operationalize-locally"></a>Preparare la messa in funzione in locale
Usare la distribuzione in _modalità locale_ per l'esecuzione in contenitori Docker nel computer locale.

È possibile usare la _modalità locale_ a scopo di sviluppo e test. Per completare la procedura seguente per rendere operativo il modello, è necessario eseguire il motore Docker in locale. È possibile usare il flag `-h` alla fine dei comandi per ottenere informazioni sull'uso del comando.

>[!NOTE]
>Se il motore Docker non è disponibile in locale, è comunque possibile procedere creando un cluster in Azure per la distribuzione. Assicurarsi di eliminare il cluster al termine dell'esercitazione per non sostenere continui addebiti.

1. Aprire l'interfaccia della riga di comando.
   In Azure Machine Learning Workbench scegliere **Open Command Prompt** (Apri prompt dei comandi) dal menu **File**.

   Il prompt della riga di comando verrà aperto nel percorso della cartella di progetto corrente **c:\temp\myIris>**.

2. Creare l'ambiente. Questo passaggio deve essere eseguito una sola volta per ambiente, ad esempio una volta per l'ambiente di sviluppo e una per l'ambiente di produzione. Per questo primo ambiente usare la _modalità locale_. È possibile provare l'opzione `-c` o `--cluster` nel comando seguente per configurare un ambiente in _modalità cluster_ in un secondo momento.

   Si noti che per il comando di configurazione seguente è necessario avere l'accesso come collaboratore alla sottoscrizione. Se non si ha tale accesso, si deve avere almeno l'accesso come collaboratore al gruppo di risorse in cui viene eseguita la distribuzione. In questo secondo caso, è necessario specificare il nome del gruppo di risorse nel comando di configurazione usando il flag `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Seguire le istruzioni visualizzate per effettuare il provisioning di un account di archiviazione per archiviare le immagini Docker, un registro contenitori di Azure per elencare le immagini Docker e un account Application Insights per raccogliere dati di telemetria. Se è stata usata l'opzione `-c`, viene creato anche un cluster del servizio contenitore di Azure.
   
   Il nome del cluster deve consentire di identificare l'ambiente. La località deve essere la stessa dell'account di Gestione modelli creato dal portale di Azure.

3. Creare un account di Gestione modelli. Si tratta di un'attività una tantum.  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. Impostare l'account di Gestione modelli.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. Impostare l'ambiente.

   Al termine della configurazione, usare il comando seguente per impostare le variabili di ambiente necessarie per rendere operativo l'ambiente. Usare il nome di ambiente usato in precedenza nel passaggio 2. Usare lo stesso nome del gruppo di risorse che è stato restituito come output nella finestra di comando al termine del processo di configurazione.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. Per verificare di aver configurato correttamente l'ambiente reso operativo per la distribuzione del servizio Web in locale, immettere il comando seguente:

   ```azurecli
   az ml env show
   ```

A questo punto è possibile creare il servizio Web in tempo reale.

>[!NOTE]
>È possibile riutilizzare l'ambiente e l'account di Gestione modelli per le distribuzioni successive del servizio Web. Non è necessario crearli per ogni servizio Web. A un account o un ambiente possono essere associati più servizi Web.

## <a name="create-a-real-time-web-service-in-one-command"></a>Creare un servizio Web in tempo reale con un unico comando
1. Usare il comando seguente per creare un servizio Web in tempo reale:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Viene generato un ID servizio Web che può essere usato in un secondo momento.

   Con il comando **az ml service create realtime** vengono usate le opzioni seguenti.
   * `-n`: nome dell'app, che può contenere solo lettere minuscole.
   * `-f`: nome del file di script di assegnazione dei punteggi.
   * `--model-file`: file di modello. In questo caso si tratta del file model.pkl ottenuto con pickle.
   * `-r`: tipo di modello. In questo caso si tratta di un modello Python.
   * `--collect-model-data true`: abilita la raccolta dati.
   * `-c`: percorso del file delle dipendenze conda in cui vengono specificati pacchetti aggiuntivi.

   >[!IMPORTANT]
   >Il nome del servizio, che è anche il nome della nuova immagine Docker, deve contenere solo lettere minuscole. In caso contrario, viene visualizzato un errore. 

2. Quando si esegue il comando, il modello e il file di assegnazione dei punteggi vengono caricati nell'account di archiviazione creato nell'ambito della configurazione dell'ambiente. Il processo di distribuzione crea un'immagine Docker contenente il modello, lo schema e il file di assegnazione dei punteggi e ne esegue il push nel registro contenitori di Azure: **\<nome_registro_contenitori_Azure\>.azureacr.io/\<nomeimmagine\>:\<versione\>**. 

   Il comando scarica quindi l'immagine nel computer locale e avvia un contenitore Docker basato su tale immagine. Se l'ambiente viene configurato in modalità cluster, il contenitore Docker viene invece distribuito nel cluster Kubernetes di Servizi cloud di Azure.

   Nell'ambito della distribuzione viene creato un endpoint HTTP REST per il servizio Web nel computer locale. Dopo alcuni minuti, il comando dovrebbe terminare con un messaggio di operazione riuscita e il servizio Web sarà pronto.

3. Per visualizzare il contenitore Docker in esecuzione, usare il comando **docker ps**:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Creare un servizio Web in tempo reale con comandi separati
In alternativa al comando **az ml service create realtime** illustrato in precedenza, è anche possibile eseguire i passaggi separatamente. 

Registrare prima il modello. Generare quindi il manifesto, compilare l'immagine Docker e creare il servizio Web. Questo approccio graduale offre maggiore flessibilità in ogni passaggio e consente anche di riusare le entità generate nel passaggio precedente e ricompilare le entità solo quando necessario.

1. Registrare il modello specificando il nome del file pickle

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Questo comando genera un ID modello.

2. Creare un manifesto.

   Per creare un manifesto, usare il comando seguente e specificare l'ID modello restituito nel passaggio precedente:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Questo comando genera un ID manifesto.

3. Creare un'immagine Docker.

   Per creare un'immagine Docker, usare il comando seguente e specificare il valore di ID manifesto restituito nel passaggio precedente. È anche possibile includere le dipendenze conda usando l'opzione `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c amlconfig\conda_dependencies.yml
   ```
   Questo comando genera un ID immagine Docker.
   
4. Creare il servizio.

   Per creare un servizio, usare il comando seguente e specificare l'ID immagine restituito nel passaggio precedente:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Questo comando genera un ID servizio Web.

A questo punto è possibile eseguire il servizio Web.

## <a name="run-the-real-time-web-service"></a>Eseguire il servizio Web in tempo reale

Per testare il servizio Web **irisapp** in esecuzione, usare un record con codifica JSON contenente una matrice di quattro numeri casuali:

1. Il servizio Web include dati di esempio. In caso di esecuzione in modalità locale, è possibile chiamare il comando **az ml service usage realtime**. Tale chiamata recupera un comando di esecuzione di esempio utile per testare il servizio, nonché l'URL di assegnazione dei punteggi che potrà essere usato per incorporare il servizio nell'app personalizzata:

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. Per testare il servizio, eseguire il comando di esecuzione del servizio restituito:

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   L'output è **"2"**, che corrisponde alla classe stimata. L'output effettivamente ottenuto potrebbe essere diverso. 

3. Per eseguire il servizio all'esterno dell'interfaccia della riga di comando, è necessario ottenere le chiavi per l'autenticazione:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Visualizzare i dati raccolti nell'archivio BLOB di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare gli account di archiviazione. A tale scopo, selezionare **Altri servizi**.

3. Nella casella di ricerca digitare **Account di archiviazione** e premere **INVIO**.

4. Nella casella di ricerca **Account di archiviazione** selezionare la risorsa **Account di archiviazione** corrispondente al proprio ambiente. 

   > [!TIP]
   > Per determinare l'account di archiviazione in uso:
   > 1. Aprire Azure Machine Learning Workbench.
   > 2. Selezionare il progetto al quale si sta lavorando.
   > 3. Aprire un prompt della riga di comando dal menu **File**.
   > 4. Al prompt della riga di comando immettere `az ml env show -v` e controllare il valore di *storage_account*. Questo è il nome dell'account di archiviazione.

5. Quando viene visualizzato il riquadro **Account di archiviazione**, fare clic su **Contenitori** nell'elenco a sinistra. Individuare il contenitore denominato **modeldata**. 
 
   Se non vengono visualizzati dati, potrebbe essere necessario attendere fino a 10 minuti dopo la prima richiesta al servizio Web per vedere i dati propagati all'account di archiviazione.

   I dati vengono trasmessi nei BLOB con il percorso del contenitore seguente:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. È possibile utilizzare questi dati dall'archivio BLOB di Azure. Sono disponibili diversi strumenti che usano sia software Microsoft che strumenti open source, ad esempio:

   - Azure Machine Learning: aprire il file CSV aggiungendolo come origine dati. 
   - Excel: aprire i file CSV giornalieri come foglio di calcolo.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): creare grafici con i dati estratti dai dati CSV nei BLOB.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): caricare i dati CSV in una tabella Hive ed eseguire query SQL direttamente sui BLOB.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): creare un frame di dati con una parte significativa dei dati CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Passaggi successivi
In questa terza parte della serie di esercitazioni in tre parti si è appreso come usare i servizi di Azure Machine Learning per:
> [!div class="checklist"]
> * Individuare il file di modello.
> * Generare uno script di assegnazione dei punteggi e un file di schema.
> * Preparare l'ambiente.
> * Creare un servizio Web in tempo reale.
> * Eseguire il servizio Web in tempo reale.
> * Esaminare i dati del BLOB di output. 

È stato eseguito uno script di training in vari ambienti di calcolo ed è stato creato e serializzato un modello che è stato quindi reso operativo tramite un servizio Web basato su Docker. 

A questo punto è possibile passare alla preparazione avanzata dei dati:
> [!div class="nextstepaction"]
> [Preparazione avanzata dei dati](tutorial-bikeshare-dataprep.md)
