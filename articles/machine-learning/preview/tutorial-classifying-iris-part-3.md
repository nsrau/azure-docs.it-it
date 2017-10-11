---
title: Distribuire un modello per i servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. Questa parte 3 riguarda la distribuzione di un modello.
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 0dfcc965d96949527b3e80285061bff320872621
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-3-deploy-a-model"></a>Classificazione di dati Iris - Parte 3: Distribuire un modello
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di analisi scientifica dei dati e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la terza di una serie in tre parti. In questa parte dell'esercitazione si usano i servizi di Azure Machine Learning (anteprima) per imparare a:

> [!div class="checklist"]
> * Individuare il file di modello
> * Generare uno script di assegnazione dei punteggi e un file di schema
> * Preparare l'ambiente
> * Creare un servizio Web in tempo reale
> * Eseguire il servizio Web in tempo reale
> * Esaminare i dati del BLOB di output 

 Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/iris_flower_data_set) per presentare l'argomento in modo semplice. Gli screenshot sono specifici di Windows, ma l'esperienza in macOS è praticamente identica.

## <a name="prerequisites"></a>Prerequisiti
Completare le prime due parti di questa serie di esercitazioni:
- Per prima cosa, seguire l'[esercitazione sulla preparazione dei dati](tutorial-classifying-iris-part-1.md) per creare le risorse di Azure Machine Learning e installare l'applicazione Azure Machine Learning Workbench.
- In secondo luogo, seguire l'[esercitazione sulla creazione di un modello](tutorial-classifying-iris-part-2.md) per creare un modello di regressione logistica in Azure Machine Learning.

## <a name="download-the-model-pickle-file"></a>Scaricare il file pickle del modello
Nella parte precedente dell'esercitazione, lo script `iris_sklearn.py` è stato eseguito in locale in Azure Machine Learning Workbench. Tale azione ha serializzato il modello di regressione logistica usando il diffuso pacchetto di serializzazione degli oggetti Python **[pickle](https://docs.python.org/2/library/pickle.html)**. 

1. Avviare l'applicazione **Azure Machine Learning Workbench** e aprire il progetto **myIris** creato nella parte precedente di questa serie di esercitazioni.

2. Dopo aver aperto il progetto, fare clic sul pulsante **Files** (File) (icona a forma di cartella) sulla barra degli strumenti a sinistra in Azure Machine Learning Workbench per aprire l'elenco file nella cartella di progetto.

3. Selezionare il file **iris_sklearn.py**. Il codice Python verrà aperto in una nuova scheda dell'editor di testo in Workbench.

4. Esaminare il file **iris_sklearn.py** per individuare dove è stato generato il file pickle. Usare CTRL+F per aprire la finestra di dialogo di ricerca e quindi trovare la parola **pickle** nel codice Python.

   Questo frammento di codice illustra come è stato generato il file di output ottenuto con pickle. Si noti che il file pickle di output è denominato `model.pkl` sul disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Individuare il file pickle del modello nei file di output di un'esecuzione precedente.
   
   Quando è stato eseguito lo script **iris_sklearn.py**, il file di modello è stato scritto nella cartella `outputs` con il nome `model.pkl`. Questa cartella risiede nell'ambiente di esecuzione scelto per eseguire lo script, non nella cartella di progetto locale. 
   
   - Per individuare il file, usare l'applicazione Azure Machine Learning Workbench e fare clic sul pulsante **Runs** (Esecuzioni) (icona a forma di orologio) sulla barra degli strumenti a sinistra per aprire l'elenco **All Runs** (Tutte le esecuzioni).  
   - Verrà aperta la scheda **All Runs** (Tutte le esecuzioni). Nella tabella delle esecuzioni selezionare un'esecuzione recente con destinazione **locale** e nome dello script **iris_sklearn.py**. 
   - Verrà visualizzata la pagina **Run Properties** (Proprietà esecuzione). Si noti la sezione **Outputs** (Output) in alto a destra nella pagina. 
   - Scaricare il file pickle selezionando la casella di controllo accanto al file **model.pkl** e facendo clic sul pulsante **Download** (Scarica). Salvarlo nella radice della cartella di progetto. Sarà necessario nei prossimi passaggi.

   ![Scaricare il file pickle](media/tutorial-classifying-iris/download_model.png)

   Per altre informazioni sulla cartella `outputs`, vedere l'articolo su [come leggere e scrivere file di dati di grandi dimensioni](how-to-read-write-files.md).

## <a name="get-scoring-and-schema-files"></a>Ottenere i file di schema e di assegnazione dei punteggi
Per distribuire il servizio Web, insieme al file di modello, sono necessari anche uno script di assegnazione dei punteggi e, facoltativamente, uno schema per i dati di input del servizio Web. Lo script di assegnazione dei punteggi carica il file `model.pkl` dalla cartella corrente e lo usa per generare una nuova classe stimata di Iris.  

1. Avviare l'applicazione **Azure Machine Learning Workbench** e aprire il progetto **myIris** creato nella parte precedente di questa serie di esercitazioni.

2. Dopo aver aperto il progetto, fare clic sul pulsante **Files** (File) (icona a forma di cartella) sulla barra degli strumenti a sinistra in Azure Machine Learning Workbench per aprire l'elenco file nella cartella di progetto.

3. Selezionare il file **iris_score.py**. Verrà aperto lo script Python. Questo file viene usato come file di assegnazione dei punteggi.

4. Per ottenere il file di schema, eseguire lo script. Scegliere l'ambiente **local** (locale) e lo script **iris_score.py** nella barra dei comandi e quindi fare clic sul pulsante **Run** (Esegui). 

5. Questo script crea un file JSON nella cartella **outputs** in cui viene acquisito lo schema di dati di input richiesto dal modello.

   ![File di assegnazione dei punteggi](media/tutorial-classifying-iris/model_data_collection.png)

6. Attendere che nel riquadro dei processi sul lato destro della finestra di Machine Learning Workbench venga visualizzato lo stato **Completed** (Completato), in verde, per il processo **iris_score.py** più recente. Fare quindi clic sul collegamento ipertestuale **iris_score.py [1]** per l'esecuzione più recente del processo per visualizzare i dettagli dell'esecuzione di **iris_score.py**. 

7. Nella sezione **Outputs** (Output) della pagina Run Properties (Proprietà esecuzione) selezionare il file **service_schema.json** appena creato. Salvare il file nella cartella radice del progetto.

8. Tornare alla scheda in cui è stato aperto lo script **iris_score.py**. 

   Si noti l'utilizzo della raccolta dati che consente di acquisire gli input del modello e le stime dal servizio Web. I punti riportati di seguito sono di particolare interesse per la raccolta dati.

9. Esaminare il codice nella parte superiore della classe di importazione file ModelDataCollector contenente la funzionalità di raccolta dati del modello:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Esaminare le righe di codice seguenti nella funzione `init()` per la creazione di un'istanza di ModelDataCollector:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Esaminare le righe di codice seguenti nella funzione `run(input_df)` per la raccolta dei dati di input e di stima:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

A questo punto è possibile preparare l'ambiente per rendere operativo il modello.

## <a name="prepare-to-operationalize-locally"></a>Preparare la messa in funzione in locale
Usare la distribuzione in _modalità locale_ per l'esecuzione in contenitori Docker nel computer locale.

È possibile usare la _modalità locale_ a scopo di sviluppo e test. Per completare la procedura seguente per rendere operativo il modello, il motore Docker deve essere eseguito in locale. È possibile usare il flag `-h` alla fine dei comandi per ottenere informazioni sull'utilizzo del comando.

>[!NOTE]
>Se il motore Docker non è disponibile in locale, è comunque possibile procedere creando un cluster in Azure per la distribuzione. Assicurarsi di eliminare il cluster al termine dell'esercitazione per non sostenere continui addebiti.

1. Aprire l'interfaccia della riga di comando. In Azure Machine Learning Workbench scegliere **Open Command Prompt** (Apri prompt dei comandi) dal menu File.

   Il prompt della riga di comando verrà aperto nel percorso della cartella di progetto corrente `c:\temp\myIris>`.

2. Verificare che il provider di risorse di Azure `Microsoft.ContainerRegistry` sia registrato nella sottoscrizione. Per poter creare un ambiente nel passaggio 3, è prima necessario registrare questo provider di risorse. È possibile verificare se sia già registrato con il comando seguente:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Verrà visualizzato un output simile al seguente: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Se `Microsoft.ContainerRegistry` non è registrato, è possibile registrarlo con il comando seguente:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   La registrazione può richiedere alcuni minuti ed è possibile controllarne lo stato con il comando `az provider list` riportato sopra oppure con il comando seguente:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

3. Creare l'ambiente. Questo passaggio deve essere eseguito una sola volta per ambiente, ad esempio di sviluppo o di produzione. Per questo primo ambiente usare la _modalità locale_. Si potrà provare l'opzione `-c` o `--cluster` nel comando seguente per configurare un ambiente in _modalità cluster_ in un secondo momento.

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Seguire le istruzioni visualizzate per effettuare il provisioning di un account di archiviazione per archiviare le immagini Docker, un registro contenitori di Azure per elencare le immagini Docker e un account Application Insights per raccogliere dati di telemetria. Se è stata usata l'opzione `-c`, viene creato anche un cluster del servizio contenitore di Azure.
   
   Il nome del cluster deve consentire di identificare l'ambiente e la località deve essere la stessa dell'account di Gestione modelli creato dal portale di Azure.

4. Creare un account di Gestione modelli. Questa configurazione deve essere eseguita una sola volta.  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Impostare l'account di Gestione modelli.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Impostare l'ambiente.
Al termine della configurazione, impostare le variabili di ambiente necessarie per la messa in funzione con il comando seguente. Il nome dell'ambiente è il nome usato in precedenza nel passaggio 1. Il nome del gruppo di risorse è stato restituito come output dallo stesso processo e sarà visualizzato nella finestra di comando al termine del processo di configurazione.
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

   Per verificare di aver configurato correttamente l'ambiente di messa in funzione per la distribuzione del servizio Web in locale, immettere il comando seguente:

   ```azurecli
   az ml env show
   ```

A questo punto è possibile creare il servizio Web in tempo reale.

## <a name="create-a-real-time-web-service"></a>Creare un servizio Web in tempo reale
Usare il comando seguente per creare un servizio Web in tempo reale:

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Viene generato un ID servizio Web utilizzabile in un secondo momento.

   Di seguito sono riportate le opzioni per il comando `az ml service create realtime`.
   * -n: nome dell'app. Deve contenere solo lettere minuscole.
   * -f: nome del file di script di assegnazione dei punteggi.
   * --model-file: file di modello, in questo caso il file model.pkl ottenuto con pickle.
   * -r: tipo di modello, in questo caso un modello Python.
   * --collect-model-data true: abilita la raccolta dati.

   >[!IMPORTANT]
   >Il nome del servizio (che è anche il nome della nuova immagine Docker) deve contenere solo lettere minuscole. In caso contrario verrà restituito un errore. 

   Quando si esegue il comando, il modello e il file di assegnazione dei punteggi vengono caricati nell'account di archiviazione creato nella configurazione dell'ambiente descritta sopra. Il processo di distribuzione compila un'immagine Docker contenente il modello, lo schema e il file di assegnazione dei punteggi e ne esegue il push nel registro contenitori di Azure: `<ACR_name>.azureacr.io/<imagename>:<version>`. Scarica quindi tale immagine sul computer locale e avvia un contenitore Docker basato su tale immagine. Se l'ambiente viene configurato in modalità cluster, il contenitore Docker viene invece distribuito nel cluster Kubernetes del servizio contenitore di Azure.

   Nell'ambito della distribuzione viene creato un endpoint HTTP REST per il servizio Web nel computer locale. Dopo alcuni minuti, il comando dovrebbe terminare con un messaggio di operazione riuscita e il servizio Web sarà pronto.

   È possibile visualizzare il contenitore Docker in esecuzione con il comando `docker ps`:
   ```azurecli
   docker ps
   ```
### <a name="alternative-route"></a>Procedura alternativa
In alternativa al comando `az ml service create realtime` illustrato sopra, è anche possibile eseguire singolarmente i passaggi di registrazione del modello, generazione del manifesto, compilazione dell'immagine Docker e creazione del servizio Web. Questa procedura offre maggiore flessibilità in ogni passaggio e consente di riusare le entità generate nel passaggio precedente e ricompilare le entità solo quando necessario. A questo scopo, seguire queste istruzioni:

1. Registrare il modello specificando il nome del file pickle

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Viene così generato un ID modello.

2. Creare il manifesto

   Per creare un manifesto, usare questo comando e specificare l'ID modello restituito nel passaggio precedente:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   Viene così generato un ID manifesto.

3. Creare un'immagine Docker

   Per creare un'immagine Docker, usare questo comando e specificare il valore di ID manifesto restituito nel passaggio precedente:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Viene così generato un ID immagine Docker.
   
4. Creare il servizio

   Per creare un servizio, usare il comando riportato e specificare l'ID immagine restituito nel passaggio precedente:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Viene così generato un ID servizio Web.

A questo punto è possibile eseguire il servizio Web.

## <a name="run-the-real-time-web-service"></a>Eseguire il servizio Web in tempo reale

Testare l'esecuzione del servizio Web `irisapp` inserendo un record con codifica JSON contenente una matrice di numeri casuali.

1. Nella creazione del servizio Web sono stati inclusi dati di esempio. In caso di esecuzione in modalità locale, è possibile chiamare il comando `az ml service show realtime` per recuperare un comando di esecuzione di esempio utilizzabile per testare il servizio. In questo modo si otterrà anche l'URL di assegnazione dei punteggi che potrà essere usato per incorporare il servizio nell'app personalizzata:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Per testare il servizio, eseguire il comando di esecuzione del servizio restituito.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}
   ```
   L'output è `"2"`, che corrisponde alla classe stimata. L'output effettivamente ottenuto potrebbe essere diverso. 

3. Se si vuole eseguire il servizio all'esterno dell'interfaccia della riga di comando, è necessario ottenere le chiavi per l'autenticazione:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Visualizzare i dati raccolti nell'archivio BLOB di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare gli account di archiviazione. A tale scopo, fare clic su **Altri servizi**.

3. Nella casella di ricerca digitare **Account di archiviazione** e premere **INVIO**.

4. Nella pagina di ricerca **Account di archiviazione** selezionare la risorsa **Account di archiviazione** corrispondente al proprio ambiente. 

   > [!TIP]
   > Per determinare l'account di archiviazione usato, aprire Azure Machine Learning Workbench, selezionare il progetto su cui si sta lavorando e aprire il prompt della riga di comando dal menu **File**. Al prompt della riga di comando digitare `az ml env show -v` e controllare il valore di *storage_account*, che è il nome dell'account di archiviazione.

5. Quando viene visualizzata la pagina **Account di archiviazione**, fare clic sulla voce **Contenitori** nell'elenco a sinistra. Individuare il contenitore denominato **modeldata**. 
 
   Se non vengono visualizzati dati, potrebbe essere necessario attendere fino a 10 minuti dopo la prima richiesta al servizio Web prima che i dati vengano propagati nell'account di archiviazione.

   I dati vengono trasmessi nei BLOB con il percorso del contenitore seguente:

   `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

6. È possibile utilizzare questi dati dai BLOB di Azure in diversi modi, sia con software Microsoft che con strumenti open source. 

   Di seguito sono riportati alcuni approcci di esempio per l'utilizzo dei BLOB di output.
   - Azure ML Workbench: aprire il file CSV in Azure ML Workbench aggiungendo il file CSV come origine dati. 
   - Excel: aprire i file CSV giornalieri come foglio di calcolo.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): creare grafici con i dati estratti dai dati CSV nei BLOB.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): creare un frame di dati con una parte significativa dei dati CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): caricare i dati CSV in una tabella Hive ed eseguire query SQL direttamente sul BLOB.

## <a name="next-steps"></a>Passaggi successivi
In questa terza parte della serie di esercitazioni in tre parti si è appreso come usare i servizi di Azure Machine Learning per:
> [!div class="checklist"]
> * Individuare il file di modello
> * Generare uno script di assegnazione dei punteggi e un file di schema
> * Preparare l'ambiente
> * Creare un servizio Web in tempo reale
> * Eseguire il servizio Web in tempo reale
> * Esaminare i dati del BLOB di output 

È stato eseguito uno script di training in vari ambienti di calcolo ed è stato creato e serializzato un modello che è stato quindi reso operativo tramite un servizio Web basato su Docker. 

A questo punto è possibile passare alla preparazione avanzata dei dati:
> [!div class="nextstepaction"]
> [Preparazione avanzata dei dati](tutorial-bikeshare-dataprep.md)


