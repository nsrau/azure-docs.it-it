---
title: Esercitazione sulla creazione di un modello per i servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. In questa seconda parte viene illustrata la sperimentazione.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: e4fc13e88d56677687e0f97d156f9b7761eae1d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Esercitazione 2: Classificare i dati Iris - Creare un modello
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la **seconda di una serie in tre parti**. In questa parte dell'esercitazione si usano i servizi di Azure Machine Learning per:

> [!div class="checklist"]
> * Aprire script ed esaminare il codice
> * Eseguire script in un ambiente locale
> * Esaminare le cronologie di esecuzione
> * Eseguire script in una finestra di interfaccia della riga di comando di Azure locale
> * Eseguire script in un ambiente Docker locale
> * Eseguire script in un ambiente Docker remoto
> * Eseguire script in un ambiente cloud di Azure HDInsight

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessari:
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
- Un account di Sperimentazione e Azure Machine Learning Workbench installato come illustrato in questa [guida introduttiva](quickstart-installation.md)
- Il progetto e i dati di Iris preparati nella [Parte 1 dell'esercitazione](tutorial-classifying-iris-part-1.md)
- Un motore Docker installato e in esecuzione localmente. Docker Community Edition è sufficiente. Per informazioni su come installare Docker, vedere qui: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Esaminare iris_sklearn.py e i file di configurazione

1. Avviare l'applicazione Azure Machine Learning Workbench.

1. Aprire il progetto **myIris** creato nella [Parte 1 della serie di esercitazioni](tutorial-classifying-iris-part-1.md).

2. Nel progetto aperto selezionare il pulsante **File** (icona della cartella) nel riquadro a sinistra per aprire l'elenco file nella cartella del progetto.

   ![Aprire il progetto di Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Selezionare il file di script **iris_sklearn.py** di Python. 

   ![Scegliere uno script](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   Il codice apre una nuova scheda dell'editor di testo in Workbench. Si tratta dello script che viene usato fino alla fine di questa parte dell'esercitazione. 

   >[!NOTE]
   >Il codice visualizzato potrebbe non essere esattamente uguale al codice precedente, perché questo progetto di esempio viene aggiornato di frequente.
   
   ![Aprire un file](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Esaminare il codice dello script Python per acquisire familiarità con lo stile di codifica. 

   Lo script **iris_sklearn.py** esegue le attività seguenti:

   * Carica il pacchetto di preparazione dati predefinito denominato **iris.dprep** per creare un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Aggiunge funzionalità casuali per rendere il problema più difficile da risolvere. La casualità è necessaria perché Iris è un set di dati di piccole dimensioni che viene classificato con un'accuratezza quasi del 100%.

   * Usa la libreria di apprendimento automatico [scikit-learn](http://scikit-learn.org/stable/index.html) per creare un modello di regressione logistica.  Questa libreria è inclusa con Azure Machine Learning Workbench per impostazione predefinita.

   * Serializza il modello usando la libreria [pickle](https://docs.python.org/3/library/pickle.html) in un file della cartella `outputs`. 
   
   * Carica il modello serializzato e quindi lo deserializza in memoria.

   * Usa il modello deserializzato per eseguire una stima su un nuovo record. 

   * Traccia due grafici, una matrice di confusione e una curva ROC (Receiver Operating Characteristic) multiclasse, usando la libreria [matplotlib](https://matplotlib.org/) e quindi li salva nella cartella `outputs`. È possibile installare questa libreria nell'ambiente, se non è già disponibile.

   * Determina automaticamente la frequenza di regolarizzazione e la precisione del modello nella cronologia di esecuzione. L'oggetto `run_logger` viene usato per registrare il tasso di regolarizzazione e l'accuratezza del modello nei log. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Eseguire iris_sklearn.py nell'ambiente locale

1. Avviare l'interfaccia della riga di comando di Azure Machine Learning:
   1. Avviare Azure Machine Learning Workbench.

   1. Dal menu di Workbench selezionare **File** > **Open Command Prompt** (Apri prompt dei comandi). 
   
   La finestra dell'interfaccia della riga di comando di Azure Machine Learning viene aperta nella cartella del progetto `C:\Temp\myIris\>` in Windows. Questo progetto corrisponde a quello creato nella Parte 1 dell'esercitazione.

   >[!IMPORTANT]
   >È necessario usare questa finestra dell'interfaccia della riga di comando per completare i passaggi successivi.

1. Nella finestra dell'interfaccia della riga di comando installare la libreria di tracciato Python, **matplotlib**, se non è già disponibile.

   Lo script **iris_sklearn.py** include dipendenze da due pacchetti Python: **scikit-learn** e **matplotlib**.  Il pacchetto **scikit-learn** viene installato automaticamente da Azure Machine Learning Workbench. È tuttavia necessario installare **matplotlib**, se non è già disponibile.

   Se si procede senza installare **matplotlib**, è comunque possibile eseguire il codice in questa esercitazione. Il codice non sarà tuttavia in grado di produrre l'output della matrice di confusione e i tracciati della curva ROC multiclasse mostrati nelle visualizzazioni della cronologia.

   ```azurecli
   pip install matplotlib
   ```

   L'installazione richiede circa un minuto.

1. Tornare all'applicazione Workbench. 

1. Trovare la scheda denominata **iris_sklearn.py**. 

   ![Trovare la scheda con lo script](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. Sulla barra degli strumenti della scheda selezionare **local** come ambiente di esecuzione e `iris_sklearn.py` come script da eseguire. È possibile che queste opzioni siano già selezionate.

   ![Selezione di ambiente locale e script](media/tutorial-classifying-iris/2-local-script.png)

1. Passare al lato destro della barra degli strumenti e immettere `0.01` nel campo **Arguments** (Argomenti). 

   Questo valore corrisponde al tasso di regolarizzazione del modello di regressione logistica.

   ![Selezione di ambiente locale e script](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Selezionare il pulsante **Run** (Esegui). Viene immediatamente pianificato un processo. Il processo viene elencato nel riquadro **Jobs** (Processi) sul lato destro della finestra di Workbench. 

   ![Selezione di ambiente locale e script](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Dopo alcuni istanti, lo stato del processo passa da **Submitting** (Invio in corso) a **Running** (In esecuzione) e infine a **Completed** (Completato).

1. Selezionare **Completed** (Completato) nel testo relativo allo stato del processo nel riquadro **Jobs** (Processi). 

   ![Eseguire sklearn](media/tutorial-classifying-iris/2-completed.png)

   Verrà visualizzata una finestra popup con il testo di output standard (stdout) per l'esecuzione. Per chiudere questo testo, selezionare il pulsante **Close** (Chiudi, **x**) nell'angolo in alto a destra della finestra popup.

   ![Output standard](media/tutorial-classifying-iris/2-standard-output.png)

9. Nello stato dello stesso processo nel riquadro **Jobs** (Processi) selezionare il testo in blu **iris_sklearn.py [n]** (_n_ indica il numero di esecuzione) subito sopra **Completed** (Completato) e la data/ora di avvio. Si apre la finestra **Run Properties** (Proprietà esecuzione) contenente le informazioni seguenti su questa specifica esecuzione:
   - Informazioni sulle **proprietà di esecuzione**
   - **Outputs**
   - **Metriche**
   - Eventuali **visualizzazioni**
   - **Log** 

   Al termine dell'esecuzione, la finestra popup mostra i risultati seguenti:

   >[!NOTE]
   >Poiché l'esercitazione ha introdotto nel set di training un certo livello di casualità, i risultati esatti potrebbero variare rispetto a quelli visualizzati qui.

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

11. Ripetere per le altre esecuzioni. 

    Nel campo **Arguments** (Argomenti) immettere una serie di valori compresi tra `0.001` e `10`. Selezionare **Run** (Esegui) per eseguire il codice alcune altre volte. Il valore dell'argomento che si modifica ogni volta viene immesso nel modello di regressione logistica nel codice, generando ogni volta risultati diversi.

## <a name="review-the-run-history-in-detail"></a>Esaminare la cronologia di esecuzione in dettaglio
In Azure Machine Learning Workbench l'esecuzione di ogni script viene acquisita come record della cronologia di esecuzione. Se si apre la visualizzazione **Runs** (Esecuzioni), è possibile visualizzare la cronologia di esecuzione di uno script specifico.

1. Per aprire l'elenco di **esecuzioni**, selezionare il pulsante **Runs** (Esecuzioni) (icona a forma di orologio) sulla barra degli strumenti a sinistra. Selezionare quindi **iris_sklearn.py** per visualizzare la scheda **Run Dashboard** (Dashboard esecuzioni) di `iris_sklearn.py`.

   ![Visualizzazione delle esecuzioni](media/tutorial-classifying-iris/run_view.png)

1. La scheda **Run Dashboard** (Dashboard esecuzioni) verrà aperta. 

   Esaminare le statistiche acquisite in più esecuzioni. I grafici eseguono il rendering nella parte superiore della scheda. Ogni esecuzione ha un numero consecutivo e i dettagli delle esecuzioni vengono elencati nella tabella nella parte inferiore della schermata.

   ![Dashboard delle esecuzioni](media/tutorial-classifying-iris/run_dashboard.png)

1. Filtrare la tabella e quindi selezionare uno dei grafici per visualizzare lo stato, la durata, l'accuratezza e il tasso di regolarizzazione di ogni esecuzione. 

1. Selezionare le caselle di controllo accanto a due o più esecuzioni nella tabella **Runs** (Esecuzioni). Selezionare il pulsante **Compare** (Confronta) per aprire un riquadro dettagliato di confronto. Esaminare il confronto con le informazioni affiancate. 

1. Per tornare a **Run Dashboard** (Dashboard esecuzioni) selezionare il pulsante **Run List** (Elenco di esecuzione) nella parte superiore sinistra del riquadro **Comparison** (Confronto).

   ![Tornare all'elenco di esecuzione](media/tutorial-classifying-iris/2-compare-back.png)

1. Selezionare una singola esecuzione per visualizzarne i dettagli. Si noti che le statistiche per l'esecuzione selezionata sono elencate nella sezione **Run Properties** (Proprietà esecuzione). I file scritti nella cartella di output sono elencati nella sezione **Outputs** (Output) da cui è possibile scaricarli.

   ![Dettagli dell'esecuzione](media/tutorial-classifying-iris/run_details.png)

   Nella sezione **Visualizations** (Visualizzazioni) viene eseguito il rendering dei due tracciati, ovvero la matrice di confusione e la curva ROC multiclasse. Tutti i file di log sono disponibili nella sezione **Logs** (Log).


## <a name="run-scripts-in-local-docker-environments"></a>Eseguire script negli ambienti Docker locali

È possibile, se si vuole, eseguire esperimenti con gli script in esecuzione in un contenitore Docker locale. È possibile configurare ambienti di esecuzione aggiuntivi, ad esempio Docker, ed eseguire gli script in tali ambienti. 

>[!NOTE]
>Per sperimentare l'invio di script per l'esecuzione in un contenitore Docker in una VM remota di Azure oppure in un cluster HDInsight Spark di Azure, è possibile seguire le [istruzioni per la creazione di una macchina virtuale di data science di Azure basata su Ubuntu o un cluster HDInsight](how-to-create-dsvm-hdi.md).

1. Se non è ancora stato fatto, installare e avviare Docker localmente nel computer Windows o MacOS. Per altre informazioni, vedere le istruzioni di installazione di Docker in https://docs.docker.com/install/. La versione Community Edition è sufficiente.

1. Nel riquadro a sinistra selezionare l'icona **Folder** (Cartella) per aprire l'elenco **Files** (File) per il progetto. Espandere la cartella `aml_config`. 

2. Sono disponibili diversi ambienti preconfigurati: **docker-python**, **docker-spark** e **local** (locale). 

   Ogni ambiente ha due file, ad esempio `docker.compute` (per **docker-python** e **docker-spark**) e `docker-python.runconfig`. Aprire ogni file e osservare che alcune opzioni sono configurabili nell'editor di testo.  

   Selezionare **Close** (Chiudi) (**x**) nelle schede per gli editor di testo aperti.

3. Eseguire lo script **iris_sklearn.py** usando l'ambiente **docker-python**: 

   - Sulla barra degli strumenti a sinistra selezionare l'icona **Clock** (Orologio) per aprire il riquadro **Runs** (Esecuzioni). Selezionare **All Runs** (Tutte le esecuzioni). 

   - Nella parte superiore della scheda **All Runs** (Tutte le esecuzioni) selezionare **docker-python** come ambiente di destinazione al posto dell'opzione predefinita **local** (locale). 

   - Spostarsi quindi verso destra selezionare **iris_sklearn.py** come script da eseguire. 

   - Lasciare vuoto il campo **Arguments** (Argomenti) perché lo script specifica un valore predefinito. 

   - Selezionare il pulsante **Run** (Esegui).

4. Osservare che viene avviato un nuovo processo. Viene visualizzato nel riquadro **Jobs** (Processi) sul lato destro della finestra di Workbench.

   Quando l'esecuzione avviene per la prima volta nell'ambiente Docker, per il completamento del processo sono necessari alcuni minuti in più. 

   Azure Machine Learning Workbench compila un nuovo file docker in background. 
   Il nuovo file fa riferimento all'immagine Docker di base specificata nel file `docker.compute` e ai pacchetti Python di dipendenze specificati nel file `conda_dependencies.yml`. 
   
   Il motore Docker esegue queste attività:

    - Scarica l'immagine di base da Azure.
    - Installa i pacchetti Python specificati nel file `conda_dependencies.yml`.
    - Avvia un contenitore Docker.
    - Copia o fa riferimento, a seconda della configurazione di esecuzione, alla copia locale della cartella di progetto.      
    - Esegue lo script `iris_sklearn.py`.

   Al termine, il risultato dovrebbe essere esattamente uguale a quello che si ottiene scegliendo come ambiente di destinazione **local** (locale).

5. Si proverà ora con Spark. L'immagine di base Docker contiene un'istanza di Spark preinstallata e configurata che può essere usata per eseguire uno script PySpark. L'uso di questa immagine di base costituisce un modo semplice per sviluppare e testare il programma Spark senza dover dedicare tempo a installare e configurare Spark. 

   Aprire il file `iris_spark.py` . Lo script carica il file di dati `iris.csv` e usa l'algoritmo di regressione logistica dalla libreria di Machine Learning per Spark per classificare il set di dati Iris. Modificare ora l'ambiente di esecuzione in **docker-spark** e lo script in **iris_spark.py** e avviare di nuovo l'esecuzione. Questo processo richiede un po' più tempo perché è necessario creare una sessione di Spark e avviarla nel contenitore Docker. È anche possibile vedere che l'output standard (stdout) è diverso da quello di `iris_spark.py`.

6. Provare con altre esecuzioni usando argomenti diversi. 

7. Aprire il file `iris_spark.py` per visualizzare il modello di regressione logistica creato usando la libreria di Machine Learning per Spark. 

8. Interagire con il riquadro **Jobs** (Processi), eseguire una visualizzazione elenco della cronologia ed eseguire una visualizzazione dei dettagli delle esecuzioni in ambienti di esecuzione diversi.

## <a name="run-scripts-in-the-cli-window"></a>Eseguire script nella finestra dell'interfaccia della riga di comando

1. Avviare l'interfaccia della riga di comando di Azure Machine Learning:
   1. Avviare Azure Machine Learning Workbench.

   1. Dal menu di Workbench selezionare **File** > **Open Command Prompt** (Apri prompt dei comandi). 
   
   Il prompt dell'interfaccia della riga di comando viene avviato nella cartella del progetto `C:\Temp\myIris\>` in Windows. Si tratta del progetto creato nella Parte 1 dell'esercitazione.

   >[!IMPORTANT]
   >È necessario usare questa finestra dell'interfaccia della riga di comando per completare i passaggi successivi.

1. Accedere ad Azure nella finestra dell'interfaccia della riga di comando. [Altre informazioni su az login](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   È possibile che l'accesso sia già stato eseguito. In questo caso è possibile ignorare questo passaggio.

   1. Al prompt dei comandi immettere:
      ```azurecli
      az login
      ```

      Questo comando restituisce un codice da usare nel browser in https://aka.ms/devicelogin.

   1. Passare a https://aka.ms/devicelogin nel browser.

   1. Quando richiesto, immettere nel browser il codice ricevuto nell'interfaccia della riga di comando.

   L'app Workbench e l'interfaccia della riga di comando usano cache delle credenziali indipendenti per l'autenticazione nelle risorse di Azure. Dopo l'accesso non sarà necessario eseguire di nuovo l'autenticazione fino alla scadenza del token memorizzato nella cache. 

1. Se l'organizzazione ha più sottoscrizioni di Azure (ambiente aziendale), è necessario impostare la sottoscrizione da usare. Trovare la sottoscrizione, impostarla usando l'ID sottoscrizione e quindi testarla.

   1. Elencare tutte le sottoscrizioni di Azure a cui si può accedere usando questo comando:
   
      ```azurecli
      az account list -o table
      ```

      Il comando **az account list** restituisce l'elenco delle sottoscrizioni disponibili per l'account di accesso in uso. 
      Se sono disponibili più sottoscrizioni, identificare il valore dell'ID sottoscrizione per la sottoscrizione da usare.

   1. Impostare la sottoscrizione di Azure da usare come account predefinito:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      Dove \<your-subscription-id\> è il valore dell'ID della sottoscrizione da usare. Non includere le parentesi acute.

   1. Confermare l'impostazione della nuova sottoscrizione richiedendo i dettagli per la sottoscrizione corrente. 

      ```azurecli
      az account show
      ```    

1. Nella finestra dell'interfaccia della riga di comando installare la libreria di tracciato Python, **matplotlib**, se non è già disponibile.

   ```azurecli
   pip install matplotlib
   ```

1. Nella finestra dell'interfaccia della riga di comando inviare lo script **iris_sklearn.py** come esperimento.

   L'esecuzione di iris_sklearn.py viene completata nel contesto di calcolo locale.

   + In Windows:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + In MacOS:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   L'output dovrebbe essere simile al seguente:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
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
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Esaminare l'output. L'output e i risultati sono gli stessi ottenuti quando si è usato Workbench per eseguire lo script. 

1. Nella finestra dell'interfaccia della riga di comando eseguire lo script **iris_sklearn.py** di Python usando di nuovo un ambiente di esecuzione Docker, se Docker è installato nel computer.

   + Se il contenitore si trova su Windows: 
     |Esecuzione<br/>Environment|Comando in Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Se il contenitore si trova su MacOS: 
     |Esecuzione<br/>Environment|Comando in Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Tornare a Workbench ed eseguire queste operazioni:
   1. Selezionare l'icona della cartella nel riquadro a sinistra per elencare i file del progetto.
   
   1. Aprire lo script di Python denominato **run.py**. Questo script è utile per eseguire il ciclo su vari tassi di regolarizzazione. 

   ![Tornare all'elenco di esecuzione](media/tutorial-classifying-iris/2-runpy.png)

1. Eseguire l'esperimento più volte con tali tassi. 

   Questo script avvia un processo `iris_sklearn.py` con un tasso di regolarizzazione pari a `10.0` (un numero davvero molto elevato). Lo script quindi dimezza il tasso nell'esecuzione seguente e così via, finché il tasso non è pari a `0.005`. 

   Lo script contiene il codice seguente:

   ![Tornare all'elenco di esecuzione](media/tutorial-classifying-iris/2-runpy-code.png)

1. Eseguire lo script **run.py** dalla riga di comando, come illustrato di seguito:

   ```cmd
   python run.py
   ```

   Questo comando invia più volte iris_sklearn.py con diverse frequenze di regolarizzazione.

   Al termine di `run.py`, verranno mostrati grafici di diverse metriche nella visualizzazione elenco della cronologia di esecuzione nel workbench.

## <a name="run-scripts-in-a-remote-docker-container"></a>Eseguire gli script in un contenitore Docker remoto
Per eseguire lo script in un contenitore Docker in un computer Linux remoto, è necessario avere l'accesso SSH (nome utente e password) al computer remoto. Nel computer deve anche essere installato e in esecuzione un motore Docker. Il modo più semplice per ottenere un computer Linux di questo tipo consiste nel creare una macchina virtuale di data science (DSVM) basata su Ubuntu in Azure. Vedere [come creare una DSVM Ubuntu da usare in Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>La DSVM basata su CentOS *non* è supportata.

1. Dopo la creazione della macchina virtuale, è possibile collegarla come ambiente di esecuzione generando una coppia di file `.runconfig` e `.compute`. Usare il comando seguente per generare i file. 

 Assegnare il nome `myvm` alla nuova risorsa di calcolo di destinazione.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >L'indirizzo IP può anche essere un nome di dominio completo (FQDN) indirizzabile pubblicamente, ad esempio `vm-name.southcentralus.cloudapp.azure.com`. È consigliabile aggiungere un nome FQDN alla DSVM e usarlo al posto di un indirizzo IP. Questa procedura è utile perché consente di disattivare la VM in futuro per risparmiare sui costi. Al successivo avvio della macchina virtuale, inoltre, l'indirizzo IP potrebbe essere cambiato.

   >[!NOTE]
   >Oltre all'autenticazione con nome utente e password, è possibile specificare una chiave privata e la passphrase corrispondente (se presente) usando `--private-key-file` ed eventualmente le opzioni `--private-key-passphrase`.

   Preparare quindi la risorsa di calcolo di destinazione **myvm** eseguendo questo comando.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   Il comando precedente costruisce l'immagine Docker nella macchina virtuale in modo che sia pronta per eseguire gli script.
   
   >[!NOTE]
   >È anche possibile cambiare il valore di `PrepareEnvironment` in `myvm.runconfig` sostituendo il valore predefinito `false` con `true`. Questa modifica consente di preparare automaticamente il contenitore Docker nell'ambito della prima esecuzione.

2. Modificare il file `myvm.runconfig` generato in `aml_config` e sostituire il framework predefinito `PySpark` con `Python`:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Anche se PySpark dovrebbe funzionare, l'uso di Python è più efficiente se non è necessaria una sessione di Spark per eseguire lo script Python.

3. Eseguire lo stesso comando usato in precedenza nella finestra dell'interfaccia della riga di comando, ma questa volta specificare come destinazione _myvm_ per eseguire iris_sklearn.py in un contenitore Docker remoto:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   Il comando viene eseguito come se si fosse in un ambiente `docker-python`, a eccezione del fatto che l'esecuzione avviene nella macchina virtuale Linux remota. La finestra di interfaccia della riga di comando visualizza le stesse informazioni di output.

4. Provare a usare Spark nel contenitore. Aprire Esplora file. Creare una copia del file `myvm.runconfig` e assegnarvi il nome `myvm-spark.runconfig`. Modificare il nuovo file cambiando l'impostazione `Framework` da `Python` a `PySpark`:
   ```yaml
   Framework: PySpark
   ```
   Non apportare modifiche al file `myvm.compute`. La stessa immagine Docker nella stessa macchina virtuale viene usata per l'esecuzione di Spark. Nel nuovo file `myvm-spark.runconfig` il campo `Target` punta allo stesso file `myvm.compute` tramite il nome `myvm`.

5. Digitare il comando seguente per eseguire lo script **iris_spark.py** nell'istanza Spark in esecuzione nel contenitore Docker remoto:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Eseguire script in cluster HDInsight
È anche possibile eseguire questo script in un cluster HDInsight Spark. Vedere [come creare un cluster HDInsight Spark da usare in Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Il cluster HDInsight deve usare BLOB di Azure come risorsa di archiviazione primaria. L'archiviazione in Azure Data Lake non è ancora supportata.

1. Se si ha accesso a un cluster Spark per Azure HDInsight, generare un comando di configurazione di esecuzione HDInsight come illustrato qui. Fornire come parametri il nome del cluster HDInsight e il nome utente e la password di HDInsight. 

   Usare il comando seguente per creare una risorsa di calcolo di destinazione che fa riferimento a un cluster HDInsight:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Per preparare il cluster HDInsight, eseguire questo comando:

   ```
   az ml experiment prepare -c myhdi
   ```

   Il nome FQDN del nodo head del cluster è in genere `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` è il nome utente SSH del cluster, definito durante la configurazione di HDInsight. Per impostazione predefinita, il valore è `sshuser`. Il valore non è `admin`, che è l'altro utente creato durante la configurazione per consentire l'accesso al sito Web di amministrazione del cluster. 

2. Eseguire lo script **iris_spark.py** nel cluster HDInsight con questo comando:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Quando l'esecuzione avviene in un cluster HDInsight remoto, è anche possibile visualizzare i dettagli dell'esecuzione del processo YARN (Yet Another Resource Negotiator) in `https://<your_cluster_name>.azurehdinsight.net/yarnui` usando l'account utente `admin`.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa seconda parte della serie di esercitazioni in tre parti si è appreso come:
> [!div class="checklist"]
> * Aprire script ed esaminare il codice in Workbench
> * Eseguire script in un ambiente locale
> * Esaminare la cronologia di esecuzione
> * Eseguire script in un ambiente Docker locale

È ora possibile passare alla terza parte di questa serie di esercitazioni, in cui viene distribuito il modello di regressione logistica creato come servizio Web in tempo reale.

> [!div class="nextstepaction"]
> [Esercitazione 3 - Distribuire modelli](tutorial-classifying-iris-part-3.md)
