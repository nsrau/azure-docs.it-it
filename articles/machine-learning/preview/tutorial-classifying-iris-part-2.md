---
title: Creare un modello per i servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. In questa seconda parte viene illustrata la sperimentazione.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/06/2017
ms.openlocfilehash: 7e489526fd1b6a7d38172d147df1f30d74119e95
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="classify-iris-part-2-build-a-model"></a>Classificare i dati Iris - Parte 2: Creare un modello
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la seconda di una serie in tre parti. In questa parte dell'esercitazione si usano i servizi di Azure Machine Learning (anteprima) per:

> [!div class="checklist"]
> * Usare Azure Machine Learning Workbench.
> * Aprire script ed esaminare il codice.
> * Eseguire script in un ambiente locale.
> * Esaminare la cronologia di esecuzione.
> * Eseguire script in un ambiente Docker locale.
> * Eseguire script in una finestra dell'interfaccia della riga di comando di Azure locale.
> * Eseguire script in un ambiente Docker remoto.
> * Eseguire script in un ambiente HDInsight cloud di Azure.

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). Gli screenshot sono specifici di Windows, ma l'esperienza in Mac OS è quasi identica.

## <a name="prerequisites"></a>Prerequisiti
Completare la prima parte di questa serie di esercitazioni. Prima di eseguire i passaggi in questa esercitazione, seguire l'[esercitazione sulla preparazione dei dati](tutorial-classifying-iris-part-1.md) per creare le risorse di Azure Machine Learning e installare l'applicazione Azure Machine Learning Workbench.

È possibile, se si vuole, eseguire esperimenti con gli script in esecuzione in un contenitore Docker locale. A questo scopo, è necessario un motore Docker (è sufficiente la Community Edition) installato e avviato localmente nel computer Windows o Mac OS. Per altre informazioni sull'installazione di Docker, vedere [Docker installation instructions](https://docs.docker.com/engine/installation/) (Istruzioni per l'installazione di Docker).

Per sperimentare l'invio di script per l'esecuzione in un contenitore Docker in una VM remota di Azure oppure in un cluster HDInsight Spark di Azure, è possibile seguire le [istruzioni per la creazione di una macchina virtuale di data science di Azure basata su Ubuntu o un cluster HDInsight](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Esaminare iris_sklearn.py e i file di configurazione
1. Aprire l'applicazione Azure Machine Learning Workbench e aprire il progetto **myIris** creato nella parte precedente di questa serie di esercitazioni.

2. Dopo che il progetto è stato aperto, selezionare il pulsante **File** (icona della cartella) nel riquadro a sinistra per aprire l'elenco file nella cartella del progetto.

3. Selezionare il file **iris_sklearn.py**. Il codice Python verrà aperto in una nuova scheda dell'editor di testo nel workbench.

   ![Aprire un file](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Il codice visualizzato potrebbe non essere esattamente uguale al codice precedente, perché questo progetto di esempio viene aggiornato di frequente.

4. Esaminare il codice dello script Python per acquisire familiarità con lo stile di codifica. Questo script esegue le operazioni seguenti:

   - Carica il pacchetto di preparazione dati **iris.dprep** per creare un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Usare il pacchetto di preparazione dati `iris.dprep` fornito con il progetto di esempio, che dovrebbe corrispondere al file `iris-1.dprep` creato nella parte 1 di questa esercitazione.

   - Aggiunge funzionalità casuali per rendere il problema più difficile da risolvere. La casualità è necessaria perché Iris è un set di dati di piccole dimensioni che viene classificato con un'accuratezza quasi del 100%.

   - Usa la libreria di apprendimento automatico [scikit-learn](http://scikit-learn.org/stable/index.html) per creare un modello di regressione logistica. 

   - Serializza il modello inserendo la libreria [pickle](https://docs.python.org/2/library/pickle.html) in un file nella cartella `outputs`. Lo script quindi lo carica e lo deserializza nella memoria.

   - Usa il modello deserializzato per eseguire una stima su un nuovo record. 

   - Traccia due grafici, una matrice di confusione e una curva ROC (Receiver Operating Characteristic) multiclasse, usando la libreria [matplotlib](https://matplotlib.org/) e quindi li salva nella cartella `outputs`.

   - L'oggetto `run_logger` viene usato per registrare il tasso di regolarizzazione e modellare l'accuratezza nei log. I log vengono tracciati automaticamente nella cronologia di esecuzione.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Eseguire lo script iris_sklearn.py in un ambiente locale

A questo punto, è necessario preparare l'esecuzione dello script **iris_sklearn.py** per la prima volta. Lo script richiede i pacchetti **scikit-learn** e **matplotlib**. Il pacchetto **scikit-learn** è già installato da Azure Machine Learning Workbench. È tuttavia necessario installare **matplotlib**. 

1. In Azure Machine Learning Workbench selezionare il menu **File** e quindi **Open Command Prompt** (Apri prompt dei comandi) per aprire il prompt dei comandi. La finestra viene definita *finestra di interfaccia della riga di comando di Azure Machine Learning Workbench* o semplicemente *finestra di interfaccia della riga di comando*.

2. Nella finestra di interfaccia della riga di comando immettere il comando seguente per installare il pacchetto Python **matplotlib**. Per il completamento dovrebbe essere necessario meno di un minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Se si ignora il comando `pip install` precedente, il codice in `iris_sklearn.py` viene eseguito correttamente. Se si esegue solo `iris_sklearn.py`, il codice non produce i tracciati di output della matrice di confusione e della curva ROC multiclasse come illustrato nelle visualizzazioni della cronologia.

3. Tornare alla finestra dell'app Workbench. 

4. Nella barra degli strumenti nella parte superiore della scheda **iris_sklearn.py**, selezionare per aprire il menu a discesa accanto all'icona **Save** (Salva) e quindi selezionare **Run Configuration** (Configurazione di esecuzione). Selezionare **local** (locale) come ambiente di esecuzione e quindi immettere `iris_sklearn.py` come script da eseguire.

5. Passare quindi al lato destro della barra degli strumenti e immettere `0.01` nel campo **Arguments** (Argomenti). 

   ![Controllo di esecuzione](media/tutorial-classifying-iris/run_control.png)

6. Selezionare il pulsante **Run** (Esegui). Viene immediatamente pianificato un processo. Il processo viene elencato nel riquadro **Jobs** (Processi) sul lato destro della finestra di Workbench. 

7. Dopo alcuni istanti, lo stato del processo passa da **Submitting** (Invio in corso) a **Running** (In esecuzione) e quindi a **Completed** (Completato).

   ![Eseguire sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Selezionare **Completed** (Completato) nel testo relativo allo stato del processo nel riquadro **Jobs** (Processi). Verrà visualizzata una finestra popup con il testo di output standard (stdout) dello script in esecuzione. Per chiudere questo testo, selezionare il pulsante **Close** (Chiudi, **x**) nell'angolo in alto a destra della finestra popup.

9. Nello stato dello stesso processo nel riquadro **Jobs** (Processi) selezionare il testo in blu **iris_sklearn.py [n]** (_n_ indica il numero di esecuzione) subito sopra **Completed** (Completato) e all'ora di avvio. Si apre la finestra **Run Properties** (Proprietà esecuzione) contenente le informazioni seguenti su questa specifica esecuzione:
   - Informazioni sulle **proprietà di esecuzione**
   - File di **output**
   - Eventuali **visualizzazioni**
   - **Log** 

   Al termine dell'esecuzione, la finestra popup mostra i risultati seguenti:

   >[!NOTE]
   >Poiché in precedenza è stato introdotto nel set di training un certo livello di casualità, i risultati esatti potrebbero variare rispetto a quelli visualizzati qui.

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
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```

10. Chiudere la scheda **Run Properties** (Proprietà esecuzione) e quindi tornare alla scheda **iris_sklearn.py**. 

11. Ripetere le esecuzioni aggiuntive. 

    Immettere nel campo **Arguments** (Argomenti) una serie di valori numerici diversi compresi tra `0.001` e `10`. Selezionare **Run** (Esegui) per eseguire il codice alcune altre volte. Il valore dell'argomento che si modifica ogni volta viene immesso nell'algoritmo di regressione logistica nel codice e genera ogni volta risultati diversi.

## <a name="review-the-run-history-in-detail"></a>Esaminare la cronologia di esecuzione in dettaglio
In Azure Machine Learning Workbench l'esecuzione di ogni script viene acquisita come record della cronologia di esecuzione. Se si apre la visualizzazione **Runs** (Esecuzioni), è possibile visualizzare la cronologia di esecuzione di uno script specifico.

1. Per aprire l'elenco di **esecuzioni**, selezionare il pulsante **Runs** (Esecuzioni) (icona a forma di orologio) sulla barra degli strumenti a sinistra. Selezionare quindi **iris_sklearn.py** per visualizzare la scheda **Run Dashboard** (Dashboard esecuzioni) di `iris_sklearn.py`.

   ![Visualizzazione delle esecuzioni](media/tutorial-classifying-iris/run_view.png)

2. La scheda **Run Dashboard** (Dashboard esecuzioni) verrà aperta. Esaminare le statistiche acquisite in più esecuzioni. I grafici eseguono il rendering nella parte superiore della scheda. Ogni esecuzione ha un numero consecutivo e i dettagli delle esecuzioni vengono elencati nella tabella nella parte inferiore della schermata.

   ![Dashboard delle esecuzioni](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtrare la tabella e quindi selezionare uno dei grafici per visualizzare lo stato, la durata, l'accuratezza e il tasso di regolarizzazione di ogni esecuzione. 

4. Selezionare due o tre esecuzioni nella tabella **Runs** (Esecuzioni) e selezionare il pulsante **Compare** (Confronta) per aprire un riquadro di confronto dettagliato. Esaminare il confronto con le informazioni affiancate. Selezionare il pulsante **Run list** (Elenco di esecuzione) nella parte superiore sinistra del riquadro **Comparison** (Confronto) per tornare alla scheda **Run Dashboard** (Dashboard esecuzioni).

5. Selezionare una singola esecuzione per visualizzarne i dettagli. Si noti che le statistiche per l'esecuzione selezionata sono elencate nella sezione **Run Properties** (Proprietà esecuzione). I file scritti nella cartella di output sono elencati nella sezione **Outputs** (Output) da cui è possibile scaricarli.

   ![Dettagli dell'esecuzione](media/tutorial-classifying-iris/run_details.png)

   Nella sezione **Visualizations** (Visualizzazioni) viene eseguito il rendering dei due tracciati, ovvero la matrice di confusione e la curva ROC multiclasse. Tutti i file di log sono disponibili nella sezione **Logs** (Log).

## <a name="execute-scripts-in-the-local-docker-environment"></a>Eseguire script nell'ambiente Docker locale

Con Machine Learning è possibile configurare facilmente ambienti di esecuzione aggiuntivi, ad esempio Docker, ed eseguire gli script in tali ambienti. 

>[!IMPORTANT]
>Per eseguire questo passaggio, è necessario che un motore Docker sia installato in locale e avviato. Per altre informazioni, vedere le istruzioni di installazione di Docker.

1. Nel riquadro a sinistra selezionare l'icona **Folder** (Cartella) per aprire l'elenco **Files** (File) per il progetto. Espandere la cartella `aml_config`. 

2. Ci sono diversi ambienti preconfigurati, ad esempio **docker-python**, **docker-spark** e **local** (locale). 

   Ogni ambiente ha due file, ad esempio `docker-python.compute` e `docker-python.runconfig`. Aprire ogni file e osservare che alcune opzioni sono configurabili nell'editor di testo.  

   Selezionare **Close** (Chiudi) (**x**) nelle schede per gli editor di testo aperti.

3. Eseguire lo script **iris_sklearn.py** usando l'ambiente **docker-python**: 

   - Sulla barra degli strumenti a sinistra selezionare l'icona **Clock** (Orologio) per aprire il riquadro **Runs** (Esecuzioni). Selezionare **All Runs** (Tutte le esecuzioni). 
   - Nella parte superiore della scheda **All Runs** (Tutte le esecuzioni) selezionare **docker-python** come ambiente di destinazione al posto dell'opzione predefinita **local** (locale). 
   - Spostarsi quindi verso destra selezionare **iris_sklearn.py** come script da eseguire. 
   - Lasciare vuoto il campo **Arguments** (Argomenti) perché lo script specifica un valore predefinito. 
   - Selezionare il pulsante **Run** (Esegui).

4. Osservare che viene avviato un nuovo processo. Viene visualizzato nel riquadro **Jobs** (Processi) sul lato destro della finestra di Workbench.

   Quando l'esecuzione avviene per la prima volta nell'ambiente Docker, per il completamento sono necessari alcuni minuti in più. 

   In background Azure Machine Learning Workbench compila un nuovo file docker. 
   Il nuovo file fa riferimento all'immagine Docker di base specificata nel file `docker.compute` e ai pacchetti Python di dipendenze specificati nel file `conda_dependencies.yml`. 
   
   Il motore Docker esegue queste attività:

    - Scarica l'immagine di base da Azure.
    - Installa i pacchetti Python specificati nel file `conda_dependencies.yml`.
    - Avvia un contenitore Docker.
    - Copia o fa riferimento, a seconda della configurazione di esecuzione, alla copia locale della cartella di progetto.      
    - Esegue lo script `iris_sklearn.py`.

   Al termine, il risultato dovrebbe essere esattamente uguale a quello che si ottiene scegliendo come ambiente di destinazione **local** (locale).

5. Si proverà ora con Spark. L'immagine di base Docker contiene un'istanza di Spark preinstallata e configurata. Grazie a questa istanza, è possibile eseguire al suo interno uno script PySpark. Si tratta di un modo semplice per sviluppare e testare il programma Spark senza dover dedicare tempo a installare e configurare Spark. 

   Aprire il file `iris_spark.py` . Lo script carica il file di dati `iris.csv` e usa l'algoritmo di regressione logistica dalla libreria di Machine Learning per Spark per classificare il set di dati Iris. Modificare ora l'ambiente di esecuzione in **docker-spark** e lo script in **iris_spark.py** e avviare di nuovo l'esecuzione. Questo processo richiede un po' più tempo perché è necessario creare una sessione di Spark e avviarla nel contenitore Docker. È anche possibile vedere che l'output standard (stdout) è diverso da quello di `iris_spark.py`.

6. Provare con altre esecuzioni usando argomenti diversi. 

7. Aprire il file `iris_spark.py` per visualizzare il modello di regressione logistica creato usando la libreria di Machine Learning per Spark. 

8. Interagire con il riquadro **Jobs** (Processi), eseguire una visualizzazione elenco della cronologia ed eseguire una visualizzazione dei dettagli delle esecuzioni in ambienti di esecuzione diversi.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Eseguire script nella finestra di interfaccia della riga di comando di Azure Machine Learning

1. In Azure Machine Learning Workbench aprire la finestra del prompt dei comandi, selezionare il menu **File** e quindi **Open Command Prompt** (Apri prompt dei comandi). Verrà avviato il prompt dei comandi nella cartella di progetto, con il prompt `C:\Temp\myIris\>`.

   >[!IMPORTANT]
   >È necessario usare la finestra della riga di comando (aperta da Workbench) per eseguire i passaggi seguenti.

2. Usare il prompt dei comandi per l'accesso ad Azure. 

   L'app Workbench e l'interfaccia della riga di comando usano cache delle credenziali indipendenti per l'autenticazione nelle risorse di Azure. È sufficiente eseguire questa operazione una volta, fino alla scadenza del token memorizzato nella cache. Il comando **az account list** restituisce l'elenco delle sottoscrizioni disponibili per l'account di accesso in uso. Se è presente più di una sottoscrizione, usare il valore di ID di quella desiderata. Impostare tale sottoscrizione come account predefinito da usare con il comando **az account set -s** e quindi specificare il valore di ID della sottoscrizione. Verificare quindi l'impostazione usando il comando account **show**.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Quando l'autenticazione è terminata e il contesto della sottoscrizione di Azure corrente è stato impostato, immettere i comandi seguenti nella finestra di interfaccia della riga di comando per installare **matplotlib** e quindi inviare lo script Python come esperimento per l'esecuzione.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Esaminare l'output. L'output e i risultati sono gli stessi ottenuti quando si è usato il workbench per eseguire lo script. 

5. Eseguire di nuovo lo stesso script usando l'ambiente di esecuzione Docker, se Docker è installato nel computer.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. Nel workbench selezionare l'icona **Folder** (Cartella) nel riquadro a sinistra per visualizzare un elenco dei file di progetto e aprire lo script Python denominato **run.py**. 

   Questo script è utile per eseguire il ciclo su vari tassi di regolarizzazione. Eseguire l'esperimento più volte con tali tassi. Questo script avvia un processo `iris_sklearn.py` con un tasso di regolarizzazione pari a `10.0` (un numero davvero molto elevato). Lo script quindi dimezza il tasso nell'esecuzione seguente e così via, finché il tasso non è pari a `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Per aprire lo script **run.py** dalla riga di comando, eseguire i comandi seguenti:

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Al completamento di `run.py`, nella visualizzazione elenco della cronologia di esecuzione nel workbench è possibile vedere un grafico.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Eseguire script in un contenitore Docker in un computer remoto
Per eseguire lo script in un contenitore Docker in un computer Linux remoto, è necessario avere l'accesso SSH (nome utente e password) al computer remoto. Nel computer remoto deve anche essere installato e in esecuzione un motore Docker. Il modo più semplice per ottenere un computer Linux di questo tipo consiste nel creare una macchina virtuale di data science (DSVM) basata su Ubuntu in Azure. Vedere [come creare una DSVM Ubuntu da usare in Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>La DSVM basata su CentOS *non* è supportata.

1. Dopo la creazione della macchina virtuale, è possibile collegarla come ambiente di esecuzione se si genera una coppia di file `.runconfig` e `.compute`. Usare il comando seguente per generare i file. Assegnare al nuovo ambiente il nome `myvm`.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >L'indirizzo IP può anche essere un nome di dominio completo (FQDN) indirizzabile pubblicamente, ad esempio `vm-name.southcentralus.cloudapp.azure.com`. È consigliabile aggiungere il nome FQDN alla DSVM e usarlo qui invece di un indirizzo IP. Questa procedura è utile perché consente di disattivare la VM in futuro per risparmiare sui costi. Al successivo avvio della macchina virtuale, inoltre, l'indirizzo IP potrebbe essere cambiato.

   Eseguire quindi il comando seguente che costruisce l'immagine Docker nella macchina virtuale in modo che sia pronta per eseguire gli script:
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >È anche possibile cambiare il valore di `PrepareEnvironment` in `myvm.runconfig` sostituendo il valore predefinito `false` con `true`. Questa modifica consente di preparare automaticamente il contenitore Docker alla prima esecuzione.

2. Modificare il file `myvm.runconfig` generato in `aml_config` e sostituire il framework predefinito `PySpark` con `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Andrà bene anche lasciare il framework impostato su PySpark. Questa scelta tuttavia è meno efficiente se non è necessaria una sessione di Spark per eseguire lo script Python.

3. Eseguire lo stesso comando usato in precedenza nella finestra di interfaccia della riga di comando, ma questa volta specificare come destinazione _myvm_:
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   Il comando viene eseguito come se si fosse in un ambiente `docker-python`, a eccezione del fatto che l'esecuzione avviene nella macchina virtuale Linux remota. La finestra di interfaccia della riga di comando visualizza le stesse informazioni di output.

4. Provare a usare Spark nel contenitore. Aprire Esplora file. È possibile eseguire questa operazione dalla finestra di interfaccia della riga di comando se si conoscono i comandi di manipolazione di file di base. Creare una copia del file `myvm.runconfig` e assegnarvi il nome `myvm-spark.runconfig`. Modificare il nuovo file cambiando l'impostazione `Framework` da `Python` a `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Non apportare modifiche al file `myvm.compute`. La stessa immagine Docker nella stessa macchina virtuale viene usata per l'esecuzione di Spark. Nel nuovo file `myvm-spark.runconfig` il campo `target` punta allo stesso file `myvm.compute` tramite il nome `myvm`.

5. Digitare il comando seguente per l'esecuzione nell'istanza di Spark nel contenitore Docker remoto:
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Eseguire lo script in un cluster HDInsight
È anche possibile eseguire questo script in un cluster HDInsight Spark. Vedere [come creare un cluster HDInsight Spark da usare in Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Il cluster HDInsight deve usare BLOB di Azure come risorsa di archiviazione primaria. L'archiviazione in Azure Data Lake non è ancora supportata.

1. Se si ha accesso a un cluster Spark per Azure HDInsight, generare un comando di configurazione di esecuzione HDInsight come illustrato qui. Fornire come parametri il nome del cluster HDInsight e il nome utente e la password di HDInsight. Usare il comando seguente:

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   Il nome FQDN del nodo head del cluster è in genere `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` è il nome utente SSH del cluster. Il valore predefinito è `sshuser` se non lo si modifica durante la configurazione di HDInsight. Il valore non è `admin`, che è l'altro utente creato durante la configurazione per consentire l'accesso al sito Web di amministrazione del cluster. 

2. Eseguire il comando seguente e lo script viene eseguito nel cluster HDInsight:

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Quando l'esecuzione avviene in un cluster HDInsight remoto, è anche possibile visualizzare i dettagli dell'esecuzione del processo YARN (Yet Another Resource Negotiator) in `https://<cluster_name>.azurehdinsight.net/yarnui` usando l'account utente `admin`.


## <a name="next-steps"></a>Passaggi successivi
In questa seconda parte della serie di esercitazioni in tre parti si è appreso come usare i servizi di Azure Machine Learning per:
> [!div class="checklist"]
> * Usare Azure Machine Learning Workbench.
> * Aprire script ed esaminare il codice.
> * Eseguire script in un ambiente locale.
> * Esaminare la cronologia di esecuzione.
> * Eseguire script in un ambiente Docker locale.
> * Eseguire script in una finestra dell'interfaccia della riga di comando di Azure locale.
> * Eseguire script in un ambiente Docker remoto.
> * Eseguire script in un ambiente HDInsight cloud.

È ora possibile passare alla terza parte della serie. Ora che è stato creato il modello di regressione logistica, lo si distribuirà come servizio Web in tempo reale.

> [!div class="nextstepaction"]
> [Distribuire un modello](tutorial-classifying-iris-part-3.md)
