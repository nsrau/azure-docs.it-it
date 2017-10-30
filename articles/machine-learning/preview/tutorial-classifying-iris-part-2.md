---
title: Creare un modello per i servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: "Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. Questa è la parte 2 della sperimentazione."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.openlocfilehash: 5d86f3bdf19603d2f92fc1a704376beefd7323c0
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="classifying-iris-part-2-build-a-model"></a>Classificazione di dati Iris - Parte 2: Creare un modello
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di analisi scientifica dei dati e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la seconda di una serie in tre parti. In questa parte dell'esercitazione si usano i servizi di Azure Machine Learning (anteprima) per imparare a:

> [!div class="checklist"]
> * Lavorare in Azure Machine Learning Workbench
> * Aprire script ed esaminare il codice
> * Eseguire script in un ambiente locale
> * Esaminare la cronologia di esecuzione
> * Eseguire script in un ambiente Docker locale
> * Eseguire script in una finestra di interfaccia della riga di comando di Azure locale
> * Eseguire script in un ambiente Docker remoto
> * Eseguire script in un ambiente HDInsight cloud

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) per presentare l'argomento in modo semplice. Gli screenshot sono specifici di Windows, ma l'esperienza in macOS è praticamente identica.

## <a name="prerequisites"></a>Prerequisiti
È necessario completare la prima parte di questa serie di esercitazioni. Prima di eseguire i passaggi in questa esercitazione, seguire l'[esercitazione sulla preparazione dei dati](tutorial-classifying-iris-part-1.md) per creare le risorse di Azure Machine Learning e installare l'applicazione Azure Machine Learning Workbench.

È possibile, se si vuole, eseguire esperimenti con gli script in esecuzione in un contenitore Docker locale. A questo scopo, sarà necessario un motore Docker (è sufficiente la Community Edition) installato e avviato localmente nel computer Windows o macOS. Altre informazioni sulle [istruzioni di installazione di Docker](https://docs.docker.com/engine/installation/).

Se si vuole sperimentare l'invio di script per l'esecuzione in un contenitore Docker in una VM remota di Azure oppure in un cluster HDInsight Spark, è possibile seguire le [istruzioni per la creazione di una macchina virtuale di data science di Azure basata su Ubuntu o un cluster HDI](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-configuration-files"></a>Esaminare iris_sklearn.py e i file di configurazione
1. Avviare l'applicazione **Azure Machine Learning Workbench** e aprire il progetto **myIris** creato nella parte precedente di questa serie di esercitazioni.

2. Dopo aver aperto il progetto, fare clic sul pulsante **Files** (File) (icona a forma di cartella) sulla barra degli strumenti a sinistra in Azure Machine Learning Workbench per aprire l'elenco file nella cartella di progetto.

3. Selezionare il file **iris_sklearn.py**. Il codice Python verrà aperto in una nuova scheda dell'editor di testo in Workbench.

   ![Aprire il file](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Il codice visualizzato potrebbe non essere esattamente uguale al codice precedente, in quanto questo progetto di esempio viene aggiornato di frequente.

4. Esaminare il codice dello script Python per acquisire familiarità con lo stile di codifica. Si noti che lo script esegue queste attività:

   - Carica il pacchetto di preparazione dati **iris.dprep** per creare un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Si sta usando il pacchetto di preparazione dati `iris.dprep` fornito con il progetto di esempio, che dovrebbe corrispondere al file `iris-1.dprep` creato nella parte 1 di questa esercitazione.

   - Aggiunge funzionalità casuali per rendere il problema più difficile da risolvere. La casualità è necessaria perché Iris è un set di dati di piccole dimensioni che è possibile classificare con un'accuratezza quasi del 100%.

   - Usa la libreria di apprendimento automatico [scikit-learn](http://scikit-learn.org/stable/index.html) per creare un semplice modello di regressione logistica. 

   - Serializza il modello usando la libreria [pickle](https://docs.python.org/2/library/pickle.html) in un file nella cartella `outputs` e quindi lo carica e lo deserializza in memoria.

   - Usa il modello deserializzato per eseguire una stima su un nuovo record. 

   - Traccia due grafici: una matrice di confusione e una curva ROC multiclasse usando la libreria [matplotlib](https://matplotlib.org/) e li salva nella cartella `outputs`.

   - L'oggetto `run_logger` viene usato per registrare il tasso di regolarizzazione e l'accuratezza del modello nei log e i log vengono tracciati automaticamente nella cronologia di esecuzione.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Eseguire lo script iris_sklearn.py nell'ambiente locale

A questo punto, è necessario preparare l'esecuzione dello script **iris_sklearn.py** per la prima volta. Lo script richiede i pacchetti scikit-learn e matplotlib. Il pacchetto **scikit-learn** è già installato da Azure ML Workbench. È tuttavia necessario installare **matplotlib**. 

1. In Azure Machine Learning Workbench fare clic sul menu **File** e scegliere **Open Command Prompt** (Apri prompt dei comandi) per avviare il prompt dei comandi. Questa finestra viene definita finestra di interfaccia della riga di comando di Azure Machine Learning Workbench o semplicemente finestra di interfaccia della riga di comando.

2. Nella finestra di interfaccia della riga di comando digitare il comando seguente per installare il pacchetto Python **matplotlib**. Per il completamento dovrebbe essere necessario meno di un minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Se non si esegue il comando `pip install` precedente, il codice in `iris_sklearn.py` viene eseguito correttamente, ma non produce i tracciati di output della matrice di confusione e della curva ROC multiclasse come illustrato nelle visualizzazioni della cronologia.

3. Tornare alla finestra dell'app Workbench. 

4. In alto a sinistra nella scheda **iris_sklearn.py**, accanto all'icona per il salvataggio, fare clic sulla freccia del menu a discesa per scegliere la **configurazione di esecuzione**.  Scegliere **local** (locale) come ambiente di esecuzione e `iris_sklearn.py` come script da eseguire.

5. Spostandosi quindi verso destra nella stessa scheda, compilare il campo **Arguments** (Argomenti) con il valore `0.01`. 

   ![Immagine](media/tutorial-classifying-iris/run_control.png)

6. Fare clic sul pulsante **Run** (Esegui). Viene immediatamente pianificato un processo. Il processo viene elencato nel pannello **Jobs** (Processi) sul lato destro della finestra di Workbench. 

7. Dopo alcuni istanti, lo stato del processo passa da **Submitting** (Invio in corso) a **Running** (In esecuzione) e infine, poco dopo, a **Completed** (Completato).

   ![Eseguire sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Fare clic sulla parola **Completed** (Completato) nel testo relativo allo stato del processo nel pannello dei processi. Verrà visualizzata una finestra popup con il testo di output standard (stdout) dello script in esecuzione. Per chiudere questo testo, fare clic sul pulsante **X** nell'angolo in alto a destra della finestra popup.

9. Nello stato dello stesso processo nel pannello dei processi fare clic sul testo in blu **iris_sklearn.py [n]** (_n_ indica il numero di esecuzione) subito sopra **Completed** (Completato) e all'ora di avvio. Verrà visualizzata la pagina **Run Properties** (Proprietà esecuzione) con le informazioni relative alle proprietà di esecuzione, i file di **output**, eventuali **visualizzazioni** e i **log** per l'esecuzione specifica. 

   Al termine dell'esecuzione, la finestra popup mostra i risultati seguenti:

   >[!NOTE]
   >Poiché in precedenza è stato introdotto nel set di training un certo livello di casualità, i risultati esatti potrebbero variare leggermente.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Chiudere la scheda **Run Properties** (Proprietà esecuzione) e tornare alla scheda **iris_sklearn.py**. 

11. Ripetere le esecuzioni aggiuntive. 

    Immettere nel campo **Arguments** (Argomenti) una serie di valori numerici diversi compresi tra `0.001` e `10`. Fare clic su **Run** (Esegui) per eseguire il codice alcune altre volte. Il valore dell'argomento che si modifica ogni volta viene immesso nell'algoritmo di regressione logistica nel codice, generando ogni volta risultati diversi.

## <a name="review-run-history-in-detail"></a>Esaminare la cronologia di esecuzione in dettaglio
In Azure Machine Learning Workbench l'esecuzione di ogni script viene acquisita come record della cronologia di esecuzione. È possibile visualizzare la cronologia di esecuzione di uno script specifico aprendo la visualizzazione **Runs** (Esecuzioni).

1. Fare clic sul pulsante **Runs** (Esecuzioni) (icona a forma di orologio) sulla barra degli strumenti a sinistra per aprire l'elenco di **esecuzioni**. Fare quindi clic su **iris_sklearn.py** per visualizzare la scheda **Run Dashboard** (Dashboard esecuzioni) di `iris_sklearn.py`.

   ![Immagine](media/tutorial-classifying-iris/run_view.png)

2. La scheda **Run Dashboard** (Dashboard esecuzioni) verrà aperta. Esaminare le statistiche acquisite in più esecuzioni. Nella parte superiore della scheda viene eseguito il rendering dei grafici, mentre nella parte inferiore della pagina sono elencate le singole esecuzioni numerate con i relativi dettagli.

   ![Immagine](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtrare la tabella e fare clic sui grafici per visualizzare, in modo interattivo, lo stato, durata, l'accuratezza e il tasso di regolarizzazione di ogni esecuzione. 

4. Selezionare due o tre esecuzioni nella tabella **Runs** (Esecuzioni) e fare clic sul pulsante **Compare** (Confronta) per aprire una pagina di confronto dettagliata. Esaminare il confronto con le informazioni affiancate. Fare clic sul pulsante **Run list** (Elenco di esecuzione) nella parte superiore sinistra della pagina di confronto per tornare alla scheda **Run Dashboard** (Dashboard esecuzioni).

5. Fare clic su una singola esecuzione per visualizzarne i dettagli. Si noti che le statistiche per l'esecuzione selezionata sono elencate nella sezione _Run Properties_ (Proprietà esecuzione). I file scritti nella cartella di output sono elencati nella sezione **Output** e possono essere scaricati.

   ![Immagine](media/tutorial-classifying-iris/run_details.png)

   Nella sezione **Visualizations** (Visualizzazioni) viene eseguito il rendering dei due tracciati, ovvero la matrice di confusione e la curva ROC multiclasse. Tutti i file di log sono disponibili nella sezione **Logs** (Log).

## <a name="execute-scripts-in-the-local-docker-environment"></a>Eseguire script nell'ambiente Docker locale

Azure ML consente di configurare facilmente ambienti di esecuzione aggiuntivi, ad esempio Docker, ed eseguire gli script in tali ambienti. 

>[!IMPORTANT]
>Per eseguire questo passaggio, è necessario che il motore Docker sia installato in locale e avviato. Per maggiori dettagli, vedere la guida all'installazione.

1. Sulla barra degli strumenti a sinistra selezionare l'icona a forma di cartella per aprire l'elenco **Files** (File) per il progetto. Espandere la cartella `aml_config`. 

2. Si noti che ci sono diversi ambienti preconfigurati, ad esempio **docker-python**, **docker-spark** e **local** (locale). 

   Ogni ambiente ha due file, ad esempio `docker-python.compute` e `docker-python.runconfig`. Aprire ogni tipo di file e osservare che alcune opzioni sono configurabili nell'editor di testo.  

   Chiudere (X) le schede per gli editor di testo aperti.

3. Eseguire lo script **iris_sklearn.py** usando l'ambiente **docker-python**. 

   - Sulla barra degli strumenti a sinistra fare clic sull'icona a forma di orologio per aprire il pannello **Runs** (Esecuzioni). Fare clic su **All Runs** (Tutte le esecuzioni). 
   - Nella parte superiore della scheda **All Runs** (Tutte le esecuzioni) scegliere **docker-python** come ambiente di destinazione al posto dell'opzione predefinita **local** (locale). 
   - Spostandosi quindi verso destra scegliere **iris_sklearn.py** come script da eseguire. 
   - Lasciare vuoto il campo **Arguments** (Argomenti) perché lo script specifica un valore predefinito. 
   - Fare clic sul pulsante **Run** (Esegui).

4. Osservare che viene avviato un nuovo processo, come indicato nel pannello **Jobs** (Processi) a destra della finestra di Workbench.

   Quando l'esecuzione avviene per la prima volta nell'ambiente Docker, per il completamento sono necessari alcuni minuti in più. 

   In background Azure Machine Learning Workbench crea un nuovo file Docker facendo riferimento all'immagine Docker di base specificata nel file `docker.compute` e ai pacchetti Python delle dipendenze specificati nel file `conda_dependencies.yml`. Il motore Docker scarica quindi l'immagine di base da Azure, installa i pacchetti Python specificati nel file `conda_dependencies.yml` e avvia un contenitore Docker. In seguito, copia (o vi fa riferimento, a seconda della configurazione dell'esecuzione) la copia locale della cartella di progetto e quindi esegue lo script `iris_sklearn.py`. Al termine, il risultato dovrebbe essere esattamente uguale a quello che si ottiene scegliendo come ambiente di destinazione **local** (locale).

5. Si proverà ora con Spark. L'immagine di base Docker contiene un'istanza di Spark preinstallata e configurata. Per questo motivo, è possibile eseguire al suo interno uno script PySpark. Si tratta di un modo semplice per sviluppare e testare il programma Spark senza dover dedicare tempo a installare e configurare Spark. 

   Aprire il file `iris_pyspark.py` . Lo script carica il file di dati `iris.csv` e usa l'algoritmo di regressione logistica dalla libreria di apprendimento automatico Spark per classificare il set di dati Iris. Modificare ora l'ambiente di esecuzione in **docker-spark** e lo script in **iris_pyspark.py** ed avviare di nuovo l'esecuzione. Questa operazione richiede un po' più tempo perché è necessario creare una sessione di Spark e avviarla nel contenitore Docker. È anche possibile vedere che l'output standard (stdout) è diverso da quello di `iris_pyspark.py`.

6. Provare con altre esecuzioni usando argomenti diversi. 

7. Aprire il file `iris_pyspark.py` per visualizzare il semplice modello di regressione logistica creato usando la libreria di apprendimento automatico Spark. 

8. Interagire con il pannello **Jobs** (Processi), la visualizzazione elenco della cronologia di esecuzione e la visualizzazione dei dettagli delle esecuzioni in ambienti di esecuzione diversi.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Eseguire script nella finestra di interfaccia della riga di comando di Azure ML

1. Usando Azure Machine Learning Workbench, avviare la finestra di interfaccia della riga di comando facendo clic sul menu **File** e quindi su **Open Command Prompt** (Apri prompt dei comandi). Verrà avviato il prompt dei comandi nella cartella di progetto, con il prompt `C:\Temp\myIris\>`.

   >[!Important]
   >È necessario usare la finestra della riga di comando (avviata da Workbench) per eseguire i passaggi seguenti.

2. Usare il prompt dei comandi (interfaccia della riga di comando) per accedere ad Azure. 

   L'app Workbench e l'interfaccia della riga di comando usano cache delle credenziali indipendenti per l'autenticazione nelle risorse di Azure. È sufficiente eseguire questa operazione una volta, fino alla scadenza del token memorizzato nella cache. Il comando **az account list** restituisce l'elenco delle sottoscrizioni disponibili per l'account di accesso in uso. Se ci sono più sottoscrizioni, usare il valore ID della sottoscrizione desiderata e impostarlo come account predefinito da usare con il comando **az set account -s**, fornendo il valore ID della sottoscrizione. Verificare quindi l'impostazione usando il comando account show.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. Quando l'autenticazione è stata eseguita e il contesto della sottoscrizione di Azure corrente è stato impostato, digitare i comandi seguenti nella finestra di interfaccia della riga di comando per installare matplotlib e inviare lo script Python come esperimento per l'esecuzione.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Esaminare l'output. Si noti che l'output e il risultato corrispondono a quelli dell'esecuzione precedente di questa esercitazione in cui è stato usato Workbench per eseguire lo script. 

5. Eseguire lo stesso script usando l'ambiente di esecuzione Docker, se Docker è installato nel computer.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. In Azure Machine Learning Workbench fare clic sull'icona a forma di cartella sulla barra degli strumenti a sinistra per visualizzare un elenco dei file di progetto e aprire lo script Python denominato **run.py**. 

   Lo script è utile per eseguire un ciclo con diversi tassi di regolarizzazione ed eseguire l'esperimento più volte con tali tassi. Questo script avvia un processo `iris_sklearn.py` con un tasso di regolarizzazione di `10.0` (un numero estremamente grande), quindi il valore viene dimezzato nell'esecuzione successiva e così via, fino a quando non raggiunge `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Per avviare lo script **run.py** dalla riga di comando, eseguire i comandi seguenti:

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Al completamento di `run.py`, nella visualizzazione elenco della cronologia di esecuzione in Azure Machine Learning Workbench è possibile vedere un grafico.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Eseguire script in un contenitore Docker in un computer remoto
Per eseguire lo script in un contenitore Docker in un computer Linux remoto, è necessario avere l'accesso SSH (nome utente e password) al computer remoto. Nel computer remoto il motore Docker deve essere installato e in esecuzione. Il modo più semplice per ottenere un computer Linux di questo tipo consiste nel creare una [macchina virtuale di data science (DSVM) basata su Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) in Azure. Si noti che la macchina virtuale di data science basata su CentOS non è supportata. 

1. Una volta creata la macchina virtuale, è possibile collegarla come ambiente di esecuzione generando una coppia di file `.runconfig` e `.compute`, con il comando seguente. Assegnare al nuovo ambiente il nome `myvm`.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >L'area dell'indirizzo IP può anche essere un nome di dominio completo (FQDN) indirizzabile pubblicamente, ad esempio `vm-name.southcentralus.cloudapp.azure.com`. È consigliabile aggiungere il nome di dominio completo alla macchina virtuale di data science e usarlo al posto dell'indirizzo IP, perché è possibile che a un certo punto si voglia spegnere la macchina virtuale per risparmiare sui costi. Al successivo avvio della macchina virtuale, inoltre, l'indirizzo IP potrebbe essere cambiato.

   Eseguire quindi il comando seguente che costruisce l'immagine Docker nella macchina virtuale in modo che sia pronta per l'esecuzione degli script.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >È anche possibile cambiare il valore di `PrepareEnvironment` in `myvm.runconfig` dal valore predefinito `false` in `true`. Ciò consente di preparare automaticamente il contenitore Docker alla prima esecuzione.

2. Modificare il file `myvm.runconfig` generato in `aml_config` e cambiare il framework da quello predefinito, `PySpark` a `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Anche l'impostazione del framework PySpark dovrebbe funzionare. Questa impostazione tuttavia non è molto efficiente se non è necessaria una sessione di Spark per eseguire lo script Python.

3. Eseguire lo stesso comando usato in precedenza nella finestra di interfaccia della riga di comando, ma questa volta usare come destinazione _myvm_:
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   Il comando viene eseguito come se si usasse un ambiente `docker-python`, ad eccezione del fatto che l'esecuzione avviene nella macchina virtuale Linux remota. La finestra di interfaccia della riga di comando visualizza le stesse informazioni di output.

4. Provare Spark nel contenitore. Aprire Esplora file. È possibile eseguire questa operazione dalla finestra di interfaccia della riga di comando se si conoscono i comandi di manipolazione di file di base. Creare una copia del file `myvm.runconfig` e assegnarvi il nome `myvm-spark.runconfig`. Modificare il nuovo file cambiando l'impostazione `Framework` da `Python` a `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Non apportare modifiche al file `myvm.compute`. La stessa immagine Docker nella stessa macchina virtuale viene usata per l'esecuzione di Spark. Nel nuovo file `myvy-spark.runconfig` il campo `target` punta allo stesso file `myvm.compute` tramite il nome `myvm`.

5. Digitare il comando seguente per l'esecuzione nell'istanza di Spark nel contenitore Docker remoto:
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Eseguire lo script in un cluster HDInsight
È anche possibile eseguire questo script in un cluster Spark effettivo. 

1. Se si ha accesso a un cluster Spark per Azure HDInsight, generare un comando di configurazione dell'esecuzione HDI come illustrato. Fornire come parametri il nome del cluster HDInsight, il nome utente di HDInsight e la password. Usare il comando seguente:

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   Il nome di dominio completo (FQDN) del nodo head del cluster è in genere `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` è il nome utente SSH del cluster. Il valore predefinito è `sshuser` se non lo si modifica durante il provisioning HDI. Non è `admin`, che è l'altro utente creato durante il provisioning per consentire l'accesso al sito Web di amministrazione del cluster. 

2. Eseguire il comando seguente e lo script viene eseguito nel cluster HDInsight:

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >Quando l'esecuzione avviene in un cluster HDI remoto, è anche possibile visualizzare i dettagli dell'esecuzione del processo YARN in `https://<cluster_name>.azurehdinsight.net/yarnui` usando l'account utente `admin`.


## <a name="next-steps"></a>Passaggi successivi
In questa seconda parte della serie di esercitazioni in tre parti si è appreso come usare i servizi di Azure Machine Learning per:
> [!div class="checklist"]
> * Lavorare in Azure Machine Learning Workbench
> * Aprire script ed esaminare il codice
> * Eseguire script in un ambiente locale
> * Esaminare la cronologia di esecuzione
> * Eseguire script in un ambiente Docker locale
> * Eseguire script in una finestra di interfaccia della riga di comando di Azure locale
> * Eseguire script in un ambiente Docker remoto
> * Eseguire script in un ambiente HDInsight cloud

È ora possibile passare alla terza parte della serie. Ora che è stato creato il modello di regressione logistica, lo si distribuirà come servizio Web in tempo reale.

> [!div class="nextstepaction"]
> [Distribuire un modello](tutorial-classifying-iris-part-3.md)
