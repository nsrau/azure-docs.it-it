---
title: Esplora Linux
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come completare diverse attività comuni di data science usando il Data Science Virtual Machine Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 59f2db8ec4dd8affe1c87ca2bb85a7ff7b8a4d7c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485392"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Data Science con Linux Data Science Virtual Machine in Azure

Questa procedura dettagliata illustra come completare diverse attività comuni di data science usando Linux Data Science Virtual Machine (DSVM). Linux DSVM è un'immagine di macchina virtuale disponibile in Azure preinstallata con una raccolta di strumenti comunemente usati per l'analisi dei dati e l'apprendimento automatico. I componenti software principali sono disponibili nell'articolo relativo [al provisioning del Data Science Virtual Machine Linux](linux-dsvm-intro.md). L'immagine di DSVM consente di iniziare a eseguire data science in pochi minuti, senza dover installare e configurare ciascuno degli strumenti singolarmente. Se necessario, è possibile aumentare facilmente il DSVM, in modo da poterlo arrestare quando non è in uso. La risorsa DSVM è flessibile ed economicamente conveniente.

Le attività data science illustrate in questa procedura dettagliata seguono i passaggi descritti in [che cos'è il processo di analisi scientifica dei dati per i team?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Il processo di analisi scientifica dei dati per i team è un approccio sistematico ai data science che consente ai team di data scientist di collaborare in modo efficace nel ciclo di vita della creazione di applicazioni intelligenti. Il processo di analisi scientifica dei dati fornisce anche un framework iterativo di analisi scientifica dei dati da seguire.

In questa procedura dettagliata viene analizzato il set di dati [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Spambase è un set di messaggi di posta elettronica contrassegnati come posta indesiderata o prosciutto (non posta indesiderata). Spambase contiene anche alcune statistiche sul contenuto dei messaggi di posta elettronica. Le statistiche vengono illustrate più avanti nella procedura dettagliata.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare un DSVM Linux, è necessario che siano soddisfatti i prerequisiti seguenti:

* **Sottoscrizione di Azure**. Per ottenere una sottoscrizione di Azure, vedere [creare subito il tuo account Azure gratuito](https://azure.microsoft.com/free/).
* [**Data Science Virtual Machine Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Per informazioni sul provisioning della macchina virtuale, vedere effettuare il provisioning [del Data Science Virtual Machine Linux](linux-dsvm-intro.md).
* [**X2go**](https://wiki.x2go.org/doku.php) installato nel computer con una sessione di Xfce aperta. Per ulteriori informazioni, vedere [Install and configure the X2Go Client](linux-dsvm-intro.md#x2go).
* Per un'esperienza di scorrimento più uniforme, nel Web browser Firefox di DSVM, impostare il flag `gfx.xrender.enabled` in `about:config`. [Altre informazioni](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Si consiglia inoltre di impostare `mousewheel.enable_pixel_scrolling` su `False`. [Altre informazioni](https://support.mozilla.org/questions/981140).
* **Azure Machine Learning account**. Se non si dispone già di un account, iscriversi per ottenere un nuovo account nel [home page Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>Scaricare il set di dati spambase

Il set di dati [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) è un set di dati relativamente piccolo contenente 4.601 esempi. Il set di dati è una dimensione utile per illustrare alcune delle funzionalità principali del DSVM, in quanto mantiene i requisiti di risorse modesti.

> [!NOTE]
> Questa procedura dettagliata è stata creata con un DSVM Linux di dimensioni D2 V2 (CentOS Edition). Per completare le procedure illustrate in questa procedura dettagliata, è possibile usare un DSVM di questa dimensione.

Se è necessario più spazio di archiviazione, è possibile creare altri dischi e collegarli a DSVM. I dischi usano l'archiviazione di Azure permanente, quindi i dati vengono conservati anche se il server viene sottoposta a nuovo provisioning a causa del ridimensionamento o dell'arresto. Per aggiungere un disco e collegarlo alla DSVM, completare la procedura descritta in [aggiungere un disco a una VM Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). I passaggi per l'aggiunta di un disco usano l'interfaccia della riga di comando di Azure, che è già installata in DSVM. È possibile completare i passaggi interamente dalla DSVM stessa. Un'altra opzione per aumentare lo spazio di archiviazione consiste nell'usare [file di Azure](../../storage/files/storage-how-to-use-files-linux.md).

Per scaricare i dati, aprire una finestra del terminale ed eseguire il comando seguente:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Il file scaricato non contiene una riga di intestazione. Viene ora creato un altro file che dispone di un'intestazione. Eseguire questo comando per creare un file con le intestazioni appropriate:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Quindi, concatenare i due file insieme:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Il set di dati include diversi tipi di statistiche per ogni messaggio di posta elettronica:

* Colonne come **word\_freq\_* Word*** indicano la percentuale di parole nel messaggio di posta elettronica che corrisponde a *Word*. Se, ad esempio, **word\_freq\_make** è **1**, l'1% di tutte le parole nel messaggio di posta elettronica è stato *creato*.
* Colonne quali **char\_freq\_* char*** indicano la percentuale di tutti i caratteri nel messaggio di posta elettronica che sono di tipo *char*.
* **capital\_run\_length\_longest** è la lunghezza massima di una sequenza di lettere maiuscole.
* **capital\_run\_length\_average** è la lunghezza media di tutte le sequenze di lettere maiuscole.
* **capital\_run\_length\_total** è la lunghezza totale di tutte le sequenze di lettere maiuscole.
* **spam** indica se il messaggio di posta elettronica è stato considerato posta indesiderata o meno: 1 = posta indesiderata, 0 = posta non indesiderata.

## <a name="explore-the-dataset-by-using-r-open"></a>Esplorare il set di dati usando R Open

Esaminiamo i dati ed eseguo un apprendimento automatico di base usando R. DSVM viene fornita con [Microsoft R Open](https://mran.revolutionanalytics.com/open/) preinstallato. Le librerie matematiche multithread nella versione preinstallata di R offrono prestazioni migliori rispetto alle versioni a thread singolo. R Open fornisce anche la riproducibilità tramite uno snapshot del repository dei pacchetti CRAN.

Per ottenere copie degli esempi di codice usati in questa procedura dettagliata, usare git per clonare il repository Azure-Machine-Learning-Data-Science. Git è preinstallato in DSVM. Nella riga di comando di git eseguire:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Aprire una finestra del terminale e avviare una nuova sessione di R nella console interattiva di R. È anche possibile usare RStudio, che è preinstallato in DSVM.

Per importare i dati e configurare l'ambiente:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Per visualizzare le statistiche di riepilogo relative a ogni colonna, usare:

    summary(data)

Per una visualizzazione diversa dei dati, usare:

    str(data)

Questa visualizzazione Mostra il tipo di ogni variabile e i primi valori del set di dati.

La colonna **spam** è stata letta come Integer, ma in realtà si tratta di un fattore o una variabile categorica. Per impostarne il tipo, usare:

    data$spam <- as.factor(data$spam)

Per eseguire alcune analisi esplorative, usare il pacchetto [ggplot2](https://ggplot2.tidyverse.org/) , una famosa libreria di grafici per R preinstallata in DSVM. In base ai dati di riepilogo visualizzati in precedenza, sono disponibili statistiche di riepilogo sulla frequenza del carattere punto esclamativo. Tracciare queste frequenze eseguendo i comandi seguenti:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Poiché la barra di zero sta inclinando il tracciato, eliminarla:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Una densità non semplice superiore a 1 sembra interessante. Esaminiamo solo i dati:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Quindi, suddividerlo per posta indesiderata rispetto a Ham:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Questi esempi consentono di creare tracciati simili ed esplorare i dati nelle altre colonne.

## <a name="train-and-test-a-machine-learning-model"></a>Eseguire il training e il test di un modello di apprendimento automatico

Si eseguirà il training di un paio di modelli di apprendimento automatico per classificare i messaggi di posta elettronica nel set di dati come contenenti spam o Ham. In questa sezione si eseguirà il training di un modello di albero delle decisioni e di un modello di foresta casuale. Viene quindi testata l'accuratezza delle stime.

> [!NOTE]
> Il pacchetto *rpart* (alberi di partizionamento e regressione ricorsivi) usato nel codice seguente è già installato in DSVM.

Per prima cosa, suddividere il set di dati in set di training e set di test:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Quindi, creare un albero delle decisioni per classificare i messaggi di posta elettronica:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Il risultato è il seguente:

![Diagramma dell'albero delle decisioni creato](./media/linux-dsvm-walkthrough/decision-tree.png)

Per determinarne le prestazioni sul set di training, usare il codice seguente:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Per determinarne le prestazioni sul set di test, eseguire:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Viene ora esaminato un modello di foresta casuale. Le foreste casuali trainano una moltitudine di alberi delle decisioni e restituiscono una classe che è la modalità delle classificazioni da tutti i singoli alberi delle decisioni. Forniscono un approccio di apprendimento automatico più potente perché sono corretti per la tendenza di un modello di albero delle decisioni a overfitting un set di dati di training.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Distribuire un modello in Azure Machine Learning Studio (classico)

[Azure Machine Learning Studio (classico)](https://studio.azureml.net/) è un servizio cloud che semplifica la creazione e la distribuzione di modelli di analisi predittiva. Una funzionalità interessante della versione classica di Azure Machine Learning Studio è la possibilità di pubblicare qualsiasi funzione R come servizio Web. Il pacchetto R Azure Machine Learning Studio semplifica la distribuzione, direttamente dalla sessione di R nella DSVM.

Per distribuire il codice dell'albero delle decisioni dalla sezione precedente, accedere a Azure Machine Learning Studio (classico). A tale scopo sono necessari l'ID dell'area di lavoro e un token di autorizzazione. Per trovare questi valori e inizializzare le variabili di Azure Machine Learning con loro, completare i passaggi seguenti:

1. Nel menu a sinistra selezionare **Impostazioni**. Prendere nota del valore per **ID area di lavoro**.

   ![ID dell'area di lavoro Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Selezionare la scheda **token di autorizzazione** . prendere nota del valore per token di **autorizzazione primario**.

   ![Il Azure Machine Learning Studio token di autorizzazione primario](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Caricare il pacchetto **AzureML** e quindi impostare i valori delle variabili con il token e l'ID dell'area di lavoro nella sessione di R in DSVM:

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Il modello viene semplificato per agevolare l'implementazione di questa dimostrazione. Scegliere le tre variabili nell'albero delle decisioni più vicine alla radice e compilare un nuovo albero usando solo le tre variabili seguenti:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. È necessaria una funzione di previsione che usa le funzionalità come input e restituisce i valori previsti:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. Pubblicare la funzione **predictSpam** in AzureML usando la funzione **publishWebService** :

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Questa funzione accetta la funzione **predictSpam** , crea un servizio Web denominato **spamWebService** con input e output definiti, quindi restituisce informazioni sul nuovo endpoint.

    Usare questo comando per visualizzare i dettagli del servizio Web pubblicato più recente, inclusi l'endpoint API e le chiavi di accesso:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Per fare una prova sulle prime dieci righe del set di test, eseguire:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Esercitazioni e procedure dettagliate per l'apprendimento approfondito

Oltre agli esempi basati su Framework, viene fornito anche un set di procedure dettagliate complete. Queste procedure dettagliate consentono di iniziare subito a sviluppare applicazioni di apprendimento avanzato in domini come la comprensione delle immagini e del linguaggio/testo.

- [Esecuzione di reti neurali in diversi framework](https://github.com/ilkarman/DeepLearningFrameworks): una procedura dettagliata completa che illustra come eseguire la migrazione del codice da un Framework a un altro. Viene inoltre illustrato come confrontare le prestazioni del modello e del runtime tra i Framework. 

- [Guida alle procedure per la creazione di una soluzione end-to-end per rilevare i prodotti all'interno di immagini](https://github.com/Azure/cortana-intelligence-product-detection-from-images): il rilevamento delle immagini è una tecnica che consente di individuare e classificare gli oggetti all'interno delle immagini. La tecnologia è potenzialmente in grado di ottenere notevoli vantaggi in molti domini aziendali reali. Ad esempio, i negozianti possono usare questa tecnica per determinare quale prodotto un cliente ha prelevato dallo scaffale. Queste informazioni a loro volta consentono ai negozi di gestire l'inventario dei prodotti. 

- [Deep learning per audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): questa esercitazione illustra come eseguire il training di un modello di apprendimento avanzato per il rilevamento di eventi audio nel [set di dati dei suoni urbani](https://urbansounddataset.weebly.com/). Nell'esercitazione viene fornita una panoramica su come utilizzare i dati audio.

- [Classificazione dei documenti di testo](https://github.com/anargyri/lstm_han): questa procedura dettagliata illustra come compilare e sottolineare due architetture di rete neurale diverse, ovvero la rete gerarchica di attenzione e la memoria a breve termine lunga (LSTM). Queste reti neurali usano l'API Keras per l'apprendimento avanzato per classificare i documenti di testo. Keras è un front-end a tre dei più popolari framework di apprendimento avanzato: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="other-tools"></a>Altri strumenti

Le sezioni rimanenti illustrano come usare alcuni degli strumenti installati in DSVM Linux. Questi strumenti sono illustrati di seguito:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e SQuirreL SQL
* SQL Server Data Warehouse

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) fornisce un'implementazione di albero con boosting rapida e accurata.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost può anche chiamare da Python o da una riga di comando.

### <a name="python"></a>Python

Per lo sviluppo Python, le distribuzioni Anaconda Python 3,5 e 2,7 vengono installate in DSVM.

> [!NOTE]
> La distribuzione anaconda include [conda](https://conda.pydata.org/docs/index.html). È possibile usare conda per creare ambienti Python personalizzati con versioni o pacchetti diversi installati.

Si legga in parte del set di dati spambase e si classificano i messaggi di posta elettronica con macchine a vettori di supporto in Scikit-learn:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per eseguire previsioni, usare:

    clf.predict(X.ix[0:20, :])

Per illustrare come pubblicare un endpoint di Azure Machine Learning, è possibile creare un modello più semplice. Verranno usate le tre variabili usate quando è stato pubblicato il modello R in precedenza:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per pubblicare il modello in Azure Machine Learning:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)


> [!NOTE]
> Questa opzione è disponibile solo per Python 2,7. Non è ancora supportato in Python 3,5. Per eseguire, usare **/Anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

La distribuzione anaconda in DSVM è dotata di un Jupyter Notebook, un ambiente multipiattaforma per la condivisione di codice e analisi di Python, R o Julia. È possibile accedere al Jupyter Notebook tramite JupyterHub. Per accedere, usare il nome utente e la password di Linux locale in https://\<DSVM nome DNS o indirizzo IP\>: 8000/. Tutti i file di configurazione per JupyterHub sono disponibili in/etc/jupyterhub.

> [!NOTE]
> Per usare Gestione pacchetti Python (tramite il comando `pip`) da un Jupyter Notebook nel kernel corrente, usare questo comando nella cella di codice:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Per usare il programma di installazione conda (tramite il comando `conda`) da un Jupyter Notebook nel kernel corrente, usare il comando seguente in una cella di codice:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

In DSVM sono già installati diversi notebook di esempio:

* Notebook Python di esempio:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Notebook R di esempio:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Il linguaggio Julia è disponibile anche dalla riga di comando nella DSVM Linux.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*r* *A*analitici *t*OOL *t*o *L*guadagno *E*asily) è uno strumento R grafico per data mining. Rattle dispone di un'interfaccia intuitiva che semplifica il caricamento, l'esplorazione e la trasformazione dei dati, nonché la creazione e la valutazione di modelli. [Rattle: un'interfaccia utente grafica di data mining per R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornisce una procedura dettagliata in cui vengono illustrate le funzionalità di Rattle.

Installare e avviare Rattle eseguendo i comandi seguenti:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Non è necessario installare Rattle nella DSVM. Tuttavia, potrebbe essere richiesto di installare pacchetti aggiuntivi quando si apre Rattle.

Rattle usa un'interfaccia basata su schede. La maggior parte delle schede corrisponde ai passaggi del [processo di analisi scientifica dei dati](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)per i team, ad esempio il caricamento dei dati o l'esplorazione dei dati. Il processo di analisi scientifica dei dati va da sinistra a destra nelle schede, L'ultima scheda contiene un log dei comandi R eseguiti da Rattle.

Per caricare e configurare il set di dati:

1. Per caricare il file, selezionare la scheda **dati** .
1. Scegliere il selettore accanto a **nomefile**, quindi selezionare **spambaseHeaders. Data**.
1. Per caricare il file, Selezionare **Esegui**. Verrà visualizzato un riepilogo di ogni colonna, incluso il tipo di dati identificato. indica se si tratta di un input, di una destinazione o di un altro tipo di variabile; e il numero di valori univoci.
1. Rattle ha identificato correttamente la colonna **spam** come destinazione. Selezionare la colonna **posta indesiderata** , quindi impostare il **tipo di dati di destinazione** su **categorico**.

Per esplorare i dati:

1. Selezionare la scheda **Explore** (Esplora).
1. Per visualizzare informazioni sui tipi di variabile e alcune statistiche di riepilogo, selezionare **riepilogo** > **Esegui**.
1. Per visualizzare altri tipi di statistiche su ciascuna variabile, selezionare altre opzioni, ad esempio **Descrizione** o **nozioni di base**.

È anche possibile usare la scheda **Esplora** per generare tracciati approfonditi. Per tracciare un istogramma dei dati:

1. Scegliere **Distributions**(Distribuzioni).
1. Per **word_freq_remove** e **word_freq_you**Selezionare **istogramma**.
1. Scegliere **Execute**(Esegui). Verranno visualizzati entrambi i tracciati di densità in un'unica finestra del grafico, in cui è chiaro che _la parola che_ viene visualizzata molto più spesso nei messaggi di posta elettronica è la _rimozione_.

Sono inoltre interessanti i tracciati di **correlazione** . Per creare un tracciato:

1. Per **tipo**selezionare **correlazione**.
1. Scegliere **Execute**(Esegui).
1. Rattle avvisa l'utente che è consigliabile usare un massimo di 40 variabili. Scegliere **Yes** (Sì) per visualizzare il tracciato.

Sono disponibili alcune correlazioni interessanti: la _tecnologia_ è strettamente correlata a _HP_ e _Labs_, ad esempio. È anche strettamente correlato a _650_ perché il prefisso del set di dati è 650.

I valori numerici per le correlazioni tra le parole sono disponibili nella finestra **Esplora** . È interessante notare, ad esempio, che la _tecnologia_ è correlata negativamente al denaro _e_ al _denaro_.

Rattle può trasformare il set di dati per gestire alcuni problemi comuni. Ad esempio, è possibile ridimensionare le funzionalità, imputare i valori mancanti, gestire gli outlier e rimuovere variabili o osservazioni con dati mancanti. Rattle può anche identificare le regole di associazione tra osservazioni e variabili. Queste schede non sono descritte in questa procedura dettagliata introduttiva.

Rattle può anche eseguire l'analisi del cluster. Escludere ora alcune funzionalità per migliorare la leggibilità dell'output. Nella scheda **dati** selezionare **Ignora** accanto a ognuna delle variabili eccetto questi 10 elementi:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Tornare alla scheda **cluster** . Selezionare **KMeans**, quindi impostare **numero di cluster** su **4**. Scegliere **Execute**(Esegui). I risultati verranno visualizzati nella finestra di output. Un cluster ha una frequenza elevata di _Giorgio_ e _HP_ed è probabilmente un messaggio di posta elettronica aziendale legittimo.

Per compilare un modello di Machine Learning di base per l'albero delle decisioni:

1. Scegliere la scheda **Model** (Modello).
1. Per il **tipo**, selezionare **albero**.
1. Selezionare **Execute** (Esegui) per visualizzare l'albero in formato testo nella finestra di output.
1. Fare clic sul pulsante **Draw** (Disegna) per visualizzarne una versione grafica. L'albero delle decisioni ha un aspetto simile all'albero ottenuto in precedenza usando rpart.

Una funzionalità utile di Rattle è la possibilità di eseguire diversi metodi di apprendimento automatico e di valutarli rapidamente. Ecco i passaggi seguenti:

1. Per **tipo**selezionare **tutti**.
1. Scegliere **Execute**(Esegui).
1. Al termine dell'esecuzione di Rattle, è possibile selezionare qualsiasi valore di **tipo** , ad esempio **SVM**, e visualizzare i risultati.
1. È anche possibile confrontare le prestazioni dei modelli nel set di convalida usando la scheda **valuta** . La selezione della **matrice di errori** , ad esempio, Mostra la matrice di confusione, l'errore generale e l'errore di classe media per ogni modello nel set di convalida. È anche possibile tracciare le curve ROC, eseguire analisi di riservatezza ed eseguire altri tipi di valutazioni del modello.

Al termine della compilazione dei modelli, selezionare la scheda **log** per visualizzare il codice R eseguito da Rattle durante la sessione. Per salvarlo, è possibile usare il pulsante **Export** (Esporta).

> [!NOTE]
> La versione corrente di Rattle contiene un bug. Per modificare lo script o usarlo per ripetere i passaggi in un secondo momento, è necessario inserire un **#** carattere davanti a *export this log...* nel testo del log.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL e SQuirreL SQL

La DSVM viene fornita con PostgreSQL installato. PostgreSQL è un sofisticato database relazionale open source. Questa sezione illustra come caricare il set di dati spambase in PostgreSQL e quindi eseguire una query su di esso.

Prima di poter caricare i dati, è necessario consentire l'autenticazione tramite password da localhost. Al prompt dei comandi eseguire:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Verso la fine del file di configurazione sono presenti diverse righe con informazioni dettagliate sulle connessioni consentite:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modificare la linea **connessioni locali IPv4** per usare **MD5** anziché **ident**, in modo che sia possibile accedere usando un nome utente e una password:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Riavviare quindi il servizio PostgreSQL:

    sudo systemctl restart postgresql

Per avviare *PSQL* (un terminale interattivo per PostgreSQL) come utente postgres predefinito, eseguire questo comando:

    sudo -u postgres psql

Creare un nuovo account utente usando il nome utente dell'account Linux usato per l'accesso. Creare una password:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Accedere a PSQL:

    psql

Importare i dati in un nuovo database:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Verranno ora esaminati i dati ed eseguite alcune query utilizzando SQuirreL SQL, uno strumento grafico che è possibile utilizzare per interagire con i database tramite un driver JDBC.

Per iniziare, aprire SQuirreL SQL dal menu **applicazioni** . Per configurare il driver:

1. Selezionare **Windows** > **Visualizza driver**.
1. Fare clic con il pulsante destro del mouse su **PostgreSQL** e scegliere **modifica driver**.
1. Selezionare il **percorso della classe aggiuntivo** > **Aggiungi**.
1. Per **nome file**immettere **/usr/share/java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**.
1. Scegliere **Open**(Apri).
1. Selezionare **Elenca driver**. Per **nome classe**selezionare **org. PostgreSQL. driver**e quindi fare clic su **OK**.

Per configurare la connessione al server locale:

1. Selezionare **Windows** > **visualizzare gli alias.**
1. Selezionare il pulsante **+** per creare un nuovo alias. Per il nuovo nome alias, immettere **database di posta indesiderata**. 
1. Per **driver**selezionare **PostgreSQL**.
1. Impostare l'URL su **jdbc:postgresql://localhost/spam**.
1. Immettere nome utente e password.
1. Selezionare **OK**.
1. Per aprire la finestra **Connection** (Connessione), fare doppio clic sull'alias **Spam database** (Database di posta indesiderata).
1. Selezionare **Connessione**.

Per eseguire alcune query:

1. Selezionare la scheda **SQL** .
1. Nella casella query nella parte superiore della scheda **SQL** immettere una query di base, ad esempio `SELECT * from data;`.
1. Premere CTRL + INVIO per eseguire la query. Per impostazione predefinita, SQuirreL SQL restituisce le prime 100 righe della query.

Sono disponibili molte altre query che è possibile eseguire per esplorare questi dati. Ad esempio, in cosa differisce la frequenza della parola *make* tra posta indesiderata e non indesiderata?

    SELECT avg(word_freq_make), spam from data group by spam;

Oppure, quali sono le caratteristiche dei messaggi di posta elettronica che contengono spesso il *3D*?

    SELECT * from data order by word_freq_3d desc;

La maggior parte dei messaggi di posta elettronica con un'occorrenza elevata di *3D* è apparentemente indesiderata. Queste informazioni possono essere utili per la creazione di un modello predittivo per la classificazione dei messaggi di posta elettronica.

Se si vuole eseguire l'apprendimento automatico usando i dati archiviati in un database PostgreSQL, provare a usare [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse è un database basato su cloud e con scalabilità orizzontale in grado di elaborare grandi volumi di dati, sia relazionali che non relazionali. Per altre informazioni, vedere [Informazioni su Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Per connettersi al data warehouse e creare la tabella, eseguire questo comando al prompt dei comandi:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Al prompt di sqlcmd eseguire questo comando:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copiare i dati utilizzando bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Il file scaricato contiene le terminazioni riga in stile Windows. Lo strumento bcp prevede terminazioni di riga di tipo UNIX. Usare il flag-r per indicare a bcp.

Eseguire quindi una query utilizzando sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

È anche possibile eseguire una query usando SQuirreL SQL. Seguire i passaggi simili a PostgreSQL usando il driver JDBC per SQL Server. Il driver JDBC si trova nella cartella/usr/share/java/jdbcdrivers/sqljdbc42.jar

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica degli articoli che illustrano le attività che costituiscono il processo di data science in Azure, vedere processo di analisi [scientifica dei dati](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)per i team.

Per una descrizione delle procedure dettagliate end-to-end che illustrano i passaggi del processo di analisi scientifica dei dati per i team per scenari specifici, vedere [procedure dettagliate del processo di Data Science](../team-data-science-process/walkthroughs.md)per i team. Le procedure dettagliate illustrano anche come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.
