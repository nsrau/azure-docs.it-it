---
title: Esplora Linux
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come completare diverse attività di data science comuni usando la macchina virtuale di analisi scientifica dei dati Linux.Learn how to complete several common data science tasks by using the Linux Data Science Virtual Machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 9857de0470baa35dcc8005e1622e5e593da95751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128361"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Data science with a Linux Data Science Virtual Machine in Azure

In questa procedura dettagliata viene illustrato come completare diverse attività di data science comuni usando Linux Data Science Virtual Machine (DSVM). DSVM Linux è un'immagine di macchina virtuale disponibile in Azure preinstallata con una raccolta di strumenti comunemente usati per l'analisi dei dati e l'apprendimento automatico. I componenti software chiave sono dettagliati in [Provisioning della macchina virtuale di data science Linux](linux-dsvm-intro.md). L'immagine DSVM semplifica l'avvio dell'analisi scientifica dei dati in pochi minuti, senza dover installare e configurare ciascuno degli strumenti singolarmente. È possibile aumentare facilmente la dSVM, se necessario, e si può interrompere quando non è in uso. La risorsa DSVM è elastica ed economica.

Le attività di data science illustrate in questa procedura dettagliata seguono i passaggi descritti in [Che cos'è il processo](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) di analisi scientifica dei dati del team? Il processo di analisi scientifica dei dati del team è un approccio sistematico alla data science che aiuta i team di data scientist a collaborare in modo efficace durante il ciclo di vita della creazione di applicazioni intelligenti. Il processo di analisi scientifica dei dati fornisce anche un framework iterativo di analisi scientifica dei dati da seguire.

In questa procedura dettagliata viene analizzato il set di dati [spambase.](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase è un insieme di e-mail contrassegnate come spam o prosciutto (non spam). Spambase contiene anche alcune statistiche sul contenuto delle e-mail. Parliamo delle statistiche più avanti nella procedura dettagliata.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter utilizzare una DSVM Linux, è necessario disporre dei prerequisiti seguenti:Before you can use a Linux DSVM, you must have the following prerequisites:

* **Sottoscrizione di Azure**. Per ottenere una sottoscrizione di Azure, vedere [Creare l'account Azure gratuito oggi](https://azure.microsoft.com/free/)stesso.
* [**Linux Data Science Virtual Machine**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Per informazioni sul provisioning della macchina virtuale, vedere [Effettuare il provisioning della macchina virtuale di analisi scientifica dei dati Linux](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) installato sul computer con una sessione XFCE aperta. Per ulteriori informazioni, vedere [Installare e configurare il client X2Go](linux-dsvm-intro.md#x2go).
* Per un'esperienza di scorrimento più fluida, nel browser web `gfx.xrender.enabled` Firefox `about:config`di DSVM, attivare il flag in . [Scopri di più](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considerare anche `mousewheel.enable_pixel_scrolling` `False`l'impostazione di . [Scopri di più](https://support.mozilla.org/questions/981140).
* **Account di Azure Machine Learning**. Se non ne hai già uno, iscriviti per ottenere un nuovo account nella home page di [Azure Machine Learning.](https://azure.microsoft.com/free/services/machine-learning//)

## <a name="download-the-spambase-dataset"></a>Scaricare il set di dati spambase

Il set di [dati spambase](https://archive.ics.uci.edu/ml/datasets/spambase) è un set di dati relativamente piccolo che contiene 4.601 esempi. Il set di dati è una dimensione comoda per illustrare alcune delle funzionalità principali di DSVM perché mantiene i requisiti delle risorse modesti.

> [!NOTE]
> Questa procedura dettagliata è stata creata utilizzando un DSVM Linux di dimensioni V2 (CentOS Edition). È possibile utilizzare una DSVM di queste dimensioni per completare le procedure illustrate in questa procedura dettagliata.

Se è necessario più spazio di archiviazione, è possibile creare dischi aggiuntivi e collegarli alla DSVM. I dischi usano l'archiviazione di Azure persistente, pertanto i dati vengono mantenuti anche se il server viene sottoposto a nuovo provisioning a causa del ridimensionamento o viene arrestato. Per aggiungere un disco e collegarlo alla macchina virtuale DSVM, completare i passaggi descritti in [Aggiungere un disco a una macchina virtuale Linux.](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) I passaggi per l'aggiunta di un disco usano l'interfaccia della riga di comando di Azure, già installata nella dSVM. È possibile completare completamente i passaggi dalla DSVM stessa. Un'altra opzione per aumentare lo spazio di archiviazione consiste nell'usare File di [Azure.](../../storage/files/storage-how-to-use-files-linux.md)

Per scaricare i dati, aprire una finestra del terminale e quindi eseguire questo comando:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Il file scaricato non dispone di una riga di intestazione. Creiamo un altro file che ha un'intestazione. Eseguire questo comando per creare un file con le intestazioni appropriate:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Quindi, concatenare i due file insieme:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Il set di dati include diversi tipi di statistiche per ogni messaggio di posta elettronica:The dataset has several types of statistics for each email:

* Le colonne come **word\_freq\_WORD** indicano la percentuale di parole nel messaggio di posta elettronica che corrispondono a *WORD*. Ad esempio, se **la parola\_freq\_make** è **1**, l'1% di tutte le parole nell'e-mail sono state *make*.
* Colonne come **\_char\_freq CHAR** indicano la percentuale di tutti i caratteri del messaggio di posta elettronica che sono *CHAR*.
* **capital\_run\_length\_longest** è la lunghezza massima di una sequenza di lettere maiuscole.
* **capital\_run\_length\_average** è la lunghezza media di tutte le sequenze di lettere maiuscole.
* **capital\_run\_length\_total** è la lunghezza totale di tutte le sequenze di lettere maiuscole.
* **spam** indica se il messaggio di posta elettronica è stato considerato posta indesiderata o meno: 1 = posta indesiderata, 0 = posta non indesiderata.

## <a name="explore-the-dataset-by-using-r-open"></a>Esplorare il set di dati usando R OpenExplore the dataset by using R Open

Esaminiamo i dati ed eseguiamo alcuni apprendimento automatico di base usando R.Let's examine the data and do some basic machine learning by using R. DSVM viene fornito con [Microsoft R Open](https://mran.revolutionanalytics.com/open/) preinstallato. Le librerie matematiche multithreading nella versione preinstallata di R offrono prestazioni migliori rispetto alle versioni a thread singolo. R Open fornisce inoltre la riproducibilità tramite uno snapshot del repository dei pacchetti CRAN.

Per ottenere copie degli esempi di codice usati in questa procedura dettagliata, usare git per clonare il repository Azure-Machine-Learning-Data-Science.To get copies of the code samples that are used in this walkthrough, use git to clone the Azure-Machine-Learning-Data-Science repository. Git è preinstallato nella DSVM. Alla riga di comando git, eseguire:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Aprire una finestra del terminale e avviare una nuova sessione R nella console interattiva R. È inoltre possibile utilizzare RStudio, preinstallato nella dSVM.

Per importare i dati e configurare l'ambiente:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Per visualizzare le statistiche di riepilogo relative a ogni colonna, usare:

    summary(data)

Per una visualizzazione diversa dei dati, usare:

    str(data)

Questa visualizzazione mostra il tipo di ogni variabile e i primi valori nel set di dati.

La colonna **spam** è stata letta come Integer, ma in realtà si tratta di un fattore o una variabile categorica. Per impostarne il tipo, usare:

    data$spam <- as.factor(data$spam)

Per eseguire alcune analisi esplorative, utilizzare il pacchetto [ggplot2,](https://ggplot2.tidyverse.org/) una libreria grafica popolare per R preinstallata nella dSVM. In base ai dati di riepilogo visualizzati in precedenza, sono disponibili statistiche di riepilogo sulla frequenza del carattere del punto esclamativo. Facciamo tracciare queste frequenze qui eseguendo i seguenti comandi:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Poiché la barra zero inclina il grafico, eliminiamolo:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

C'è una densità non banale superiore a 1 che sembra interessante. Diamo un'occhiata solo ai dati:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Quindi, dividerlo per spam contro prosciutto:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Questi esempi consentono di creare grafici simili ed esplorare i dati nelle altre colonne.

## <a name="train-and-test-a-machine-learning-model"></a>Eseguire il training e il test di un modello di apprendimento automatico

Esaminiamo un paio di modelli di apprendimento automatico per classificare i messaggi di posta elettronica nel set di dati come contenenti spam o prosciutto. In questa sezione viene esinato un modello di albero delle decisioni e un modello di foresta casuale. Quindi, testiamo l'accuratezza delle stime.

> [!NOTE]
> Il pacchetto *rpart* (Recursive Partitioning and Regression Trees) usato nel codice seguente è già installato nella DSVM.

In primo luogo, dividiamo il set di dati in set di training e set di test:First, let's split the dataset into training sets and test sets:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Quindi, creare un albero delle decisioni per classificare i messaggi di posta elettronica:Then, create a decision tree to classify the emails:

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

Viene ora esaminato un modello di foresta casuale. Le foreste casuali allenano una moltitudine di alberi delle decisioni e restituiscono una classe che è la modalità delle classificazioni da tutti i singoli alberi decisionali. Forniscono un approccio di apprendimento automatico più potente perché correggono la tendenza di un modello di albero delle decisioni a sovrafdire un set di dati di training.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Distribuire un modello in Azure Machine Learning Studio (classico)Deploy a model to Azure Machine Learning Studio (classic)

[Azure Machine Learning Studio (classico)](https://studio.azureml.net/) è un servizio cloud che semplifica la creazione e la distribuzione di modelli di analisi predittiva. Una bella funzionalità di Azure Machine Learning Studio (classico) è la possibilità di pubblicare qualsiasi funzione R come servizio Web.A nice feature of Azure Machine Learning Studio (classic) is its ability to publish any R function as a web service. Il pacchetto R di Azure Machine Learning Studio (classico) semplifica la distribuzione, direttamente dalla sessione R nella dSVM.

Per distribuire il codice dell'albero delle decisioni dalla sezione precedente, accedere ad Azure Machine Learning Studio (classico). A tale scopo sono necessari l'ID dell'area di lavoro e un token di autorizzazione. Per trovare questi valori e inizializzare le variabili di Azure Machine Learning con loro, completare questi passaggi:To find these values and initialize the Azure Machine Learning variables with them, complete these steps:

1. Nel menu a sinistra, selezionare **Impostazioni**. Prendere nota del valore di **WORKSPACE ID**.

   ![ID dell'area di lavoro di Azure Machine Learning Studio (classica)The Azure Machine Learning Studio (classic) workspace ID](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Selezionare la scheda **Token di autorizzazione.** Notare il valore di **Primary Authorization Token**.

   ![Token di autorizzazione principale di Azure Machine Learning Studio (classico)The Azure Machine Learning Studio (classic) primary authorization token](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Caricare il pacchetto **di AzureML** e quindi impostare i valori delle variabili con il token e l'ID dell'area di lavoro nella sessione R nella dSVM:

        if(!require("devtools")) install.packages("devtools")
        devtools::install_github("RevolutionAnalytics/AzureML")
        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Il modello viene semplificato per agevolare l'implementazione di questa dimostrazione. Scegliere le tre variabili nell'albero delle decisioni più vicino alla radice e creare un nuovo albero utilizzando solo queste tre variabili:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. È necessaria una funzione di previsione che usa le funzionalità come input e restituisce i valori previsti:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }

1. Creare un file settings.json per questa area di lavoro:Create a settings.json file for this workspace:

        vim ~/.azureml/settings.json

1. Assicurarsi che i seguenti contenuti siano inseriti all'interno di settings.json:

         {"workspace":{
           "id": "<workspace-id>",
           "authorization_token": "<authorization-token>",
           "api_endpoint": "https://studioapi.azureml.net",
           "management_endpoint": "https://management.azureml.net"
         }


1. Pubblicare la funzione predictSpam in AzureML usando la funzione **publishWebService:Publish** the **predictSpam** function to AzureML by using the publishWebService function:

        ws <- workspace()
        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Questa funzione accetta la funzione **predictSpam,** crea un servizio Web denominato **spamWebService** che dispone di input e output definiti e quindi restituisce informazioni sul nuovo endpoint.

    Usare questo comando per visualizzare i dettagli dell'ultimo servizio Web pubblicato, inclusi l'endpoint API e le chiavi di accesso:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Per fare una prova sulle prime dieci righe del set di test, eseguire:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Esercitazioni e procedure dettagliate di Deep learning

Oltre agli esempi basati su framework, viene fornito anche un set di procedure dettagliate complete. Queste procedure dettagliate consentono di iniziare subito a sviluppare applicazioni di apprendimento avanzato in domini come la comprensione delle immagini e del linguaggio/testo.

- Esecuzione di [reti neurali in framework diversi:](https://github.com/ilkarman/DeepLearningFrameworks)una procedura dettagliata completa che illustra come eseguire la migrazione del codice da un framework a un altro. Viene inoltre illustrato come confrontare le prestazioni del modello e di runtime tra i framework. 

- Una guida alle procedure per la creazione di [una soluzione end-to-end per rilevare i prodotti all'interno](https://github.com/Azure/cortana-intelligence-product-detection-from-images)delle immagini: il rilevamento delle immagini è una tecnica in grado di individuare e classificare gli oggetti all'interno delle immagini. La tecnologia ha il potenziale per portare enormi ricompense in molti domini di business reali. Ad esempio, i negozianti possono usare questa tecnica per determinare quale prodotto un cliente ha prelevato dallo scaffale. Queste informazioni a loro volta consentono ai negozi di gestire l'inventario dei prodotti. 

- [Deep learning per l'audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Questo tutorial mostra come eseguire il training di un modello di deep learning per il rilevamento di eventi audio sul set di dati [dei suoni urbani.](https://urbansounddataset.weebly.com/) L'esercitazione offre una panoramica sull'utilizzo dei dati audio.

- [Classificazione di documenti di testo](https://github.com/anargyri/lstm_han): questa procedura dettagliata viene illustrato come compilare ed eseguire il training di due diverse architetture di rete neurale: rete di attenzione gerarchica e memoria a breve termine (LSTM). Queste reti neurali usano l'API Keras per l'apprendimento avanzato per classificare i documenti di testo. Keras è un front-end a tre dei più popolari framework di apprendimento avanzato: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="other-tools"></a>Altri strumenti

Le sezioni rimanenti mostrano come utilizzare alcuni degli strumenti installati in Linux DSVM. Discutiamo di questi strumenti:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e SQuirreL SQL
* SQL Server Data Warehouse

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) fornisce un'implementazione rapida e accurata dell'albero.

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

Per lo sviluppo di Python, le distribuzioni di Anaconda Python 3.5 e 2.7 sono installate su DSVM.

> [!NOTE]
> La distribuzione di Anaconda include [Conda](https://conda.pydata.org/docs/index.html). È possibile utilizzare Conda per creare ambienti Python personalizzati in cui sono installate versioni o pacchetti diversi.

Leggiamo alcuni dei set di dati spambase e classifichiamo le e-mail con le macchine vettoriali di supporto in Scikit-learn:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per eseguire previsioni, usare:

    clf.predict(X.ix[0:20, :])

Per illustrare come pubblicare un endpoint di Azure Machine Learning, è possibile creare un modello più semplice. Useremo le tre variabili usate quando è stato pubblicato il modello R in precedenza:We'll use the three variables that we used when we published the R model earlier:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

To publish the model to Azure Machine Learning:

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
> Questa opzione è disponibile solo per Python 2.7. Non è ancora supportato su Python 3.5. Per eseguire, utilizzare **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

La distribuzione Anaconda in DSVM viene fornito con un notebook Jupyter, un ambiente multipiattaforma per la condivisione di Python, R o Julia codice e analisi. Il notebook Jupyter è accessibile tramite JupyterHub. Accedere utilizzando il nome utente e la\<password Linux locale al\>nome DNS Ohttps://indirizzo DSVM :8000/. Tutti i file di configurazione per JupyterHub si trovano in /etc/jupyterhub.

> [!NOTE]
> Per usare Python Package Manager `pip` (tramite il comando) da un Jupyter Notebook nel kernel corrente, usa questo comando nella cella del codice:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Per utilizzare il programma di `conda` installazione di Conda (tramite il comando) da un jupyter Notebook nel kernel corrente, utilizzare questo comando in una cella di codice:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Diversi blocchi appunti di esempio sono già installati nella DSVM:

* Blocchi appunti Python di esempio:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService (Servizio IrisClassifierPyMLWebService)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Blocco appunti R di esempio:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Il linguaggio Julia è disponibile anche dalla riga di comando su Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) è uno strumento grafico R per il data mining. Rattle ha un'interfaccia intuitiva che semplifica il caricamento, l'esplorazione e la trasformazione dei dati e la creazione e la valutazione dei modelli. [Rattle: una GUI di data mining per R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornisce una procedura dettagliata che illustra le funzionalità di Rattle.

Installare e avviare Rattle eseguendo questi comandi:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Non è necessario installare Rattle su DSVM. Tuttavia, potrebbe essere richiesto di installare pacchetti aggiuntivi all'apertura di Rattle.

Rattle usa un'interfaccia basata su schede. La maggior parte delle schede corrisponde ai passaggi del processo di analisi scientifica dei dati del [team,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)ad esempio il caricamento dei dati o l'esplorazione dei dati. Il processo di analisi scientifica dei dati va da sinistra a destra nelle schede, L'ultima scheda contiene un registro dei comandi R eseguiti da Rattle.

Per caricare e configurare il set di dati:

1. Per caricare il file, selezionare la scheda **Dati.**
1. Scegliere il selettore accanto a **Nome file**, quindi selezionare **spambaseHeaders.data**.
1. Per caricare il file, Selezionare **Esegui**. Verrà visualizzato un riepilogo di ogni colonna, incluso il tipo di dati identificato. se si tratta di un input, una destinazione o un altro tipo di variabile; e il numero di valori univoci.
1. Rattle ha identificato correttamente la colonna **spam** come destinazione. Selezionare la colonna **spam** e quindi impostare **Tipo** di dati di destinazione su **Categoric**.

Per esplorare i dati:

1. Selezionare la scheda **Explore** (Esplora).
1. Per visualizzare informazioni sui tipi di variabile e alcune statistiche di riepilogo, selezionare**Esecuzione** **riepilogo** > .
1. Per visualizzare altri tipi di statistiche su ogni variabile, selezionare altre opzioni, ad esempio **Descrivere** o **Nozioni di base.**

È inoltre possibile utilizzare la scheda **Esplora** per generare grafici approfonditi. Per tracciare un istogramma dei dati:

1. Scegliere **Distributions**(Distribuzioni).
1. Per **word_freq_remove** e **word_freq_you**, selezionare **Istogramma**.
1. Scegliere **Execute**(Esegui). Dovresti vedere entrambi i grafici di densità in una singola finestra del grafico, dove è chiaro che la parola _che_ appare molto più frequentemente nelle e-mail rispetto _a rimuovere_.

Anche i grafici **di correlazione** sono interessanti. Per creare un grafico:

1. Per **Tipo**, selezionare **Correlazione**.
1. Scegliere **Execute**(Esegui).
1. Rattle avvisa l'utente che è consigliabile usare un massimo di 40 variabili. Scegliere **Yes** (Sì) per visualizzare il tracciato.

Ci sono alcune correlazioni interessanti che vengono fuori: _la tecnologia_ è fortemente correlata a _HP_ e _laboratori_, per esempio. È anche fortemente correlato a _650_ perché il prefisso dei donatori del set di dati è 650.

I valori numerici per le correlazioni tra le parole sono disponibili nella finestra **Esplora.** È interessante notare, ad esempio, che la _tecnologia_ è correlata negativamente con _il tuo_ e _il denaro_.

Rattle può trasformare il set di dati per gestire alcuni problemi comuni. Ad esempio, può ridimensionare le feature, imputare valori mancanti, gestire gli outlier e rimuovere variabili o osservazioni con dati mancanti. Rattle può anche identificare le regole di associazione tra osservazioni e variabili. Queste schede non vengono trattate in questa procedura introduttiva.

Rattle può anche eseguire l'analisi del cluster. Escludere ora alcune funzionalità per migliorare la leggibilità dell'output. Nella scheda **Dati** selezionare **Ignora** accanto a ognuna delle variabili ad eccezione di questi 10 elementi:

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

Tornare alla scheda **Cluster.** Selezionare **KMeans**, quindi impostare **Numero di cluster** su **4**. Scegliere **Execute**(Esegui). I risultati verranno visualizzati nella finestra di output. Un cluster ha alta frequenza di _george_ e _hp_, ed è probabilmente un e-mail aziendale legittimo.

Per creare un modello di apprendimento automatico dell'albero delle decisioni di base:To build a basic decision tree machine learning model:

1. Selezionare la scheda **Modello,**
1. Per **Tipo**, selezionare **Albero**.
1. Selezionare **Execute** (Esegui) per visualizzare l'albero in formato testo nella finestra di output.
1. Fare clic sul pulsante **Draw** (Disegna) per visualizzarne una versione grafica. L'albero delle decisioni è simile all'albero ottenuto in precedenza utilizzando rpart.

Una caratteristica utile di Rattle è la sua capacità di eseguire diversi metodi di apprendimento automatico e valutarli rapidamente. Ecco i passaggi:

1. Per **Tipo**, selezionare **Tutto**.
1. Scegliere **Execute**(Esegui).
1. Al termine dell'esecuzione di Rattle, è possibile selezionare qualsiasi valore **Type,** ad **esempio SVM,** e visualizzare i risultati.
1. È inoltre possibile confrontare le prestazioni dei modelli nel set di convalida utilizzando la scheda **Valuta.** Ad esempio, la selezione **Matrice** di errore mostra la matrice di confusione, l'errore complessivo e l'errore di classe media per ogni modello nel set di convalida. È inoltre possibile tracciare curve ROC, eseguire analisi di sensibilità ed eseguire altri tipi di valutazioni del modello.

Al termine della creazione dei modelli, selezionare la scheda **Log** per visualizzare il codice R eseguito da Rattle durante la sessione. Per salvarlo, è possibile usare il pulsante **Export** (Esporta).

> [!NOTE]
> La versione corrente di Rattle contiene un bug. Per modificare lo script o usarlo per ripetere i **#** passaggi successivi, è necessario inserire un carattere davanti *a Esporta questo registro ...* nel testo del registro.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL e SQuirreL SQL

La DSVM viene fornita con PostgreSQL installato. PostgreSQL è un sofisticato database relazionale open source. In questa sezione viene illustrato come caricare il set di dati spambase in PostgreSQL e quindi eseguire query su di esso.

Prima di poter caricare i dati, è necessario consentire l'autenticazione della password da localhost. Al prompt dei comandi eseguire:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Verso la fine del file di configurazione sono presenti diverse righe con informazioni dettagliate sulle connessioni consentite:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modificare la linea di **connessioni locali IPv4** in modo da utilizzare **md5** invece di **ident**, in modo da poter accedere utilizzando un nome utente e una password:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Quindi, riavviare il servizio PostgreSQL:Then, restart the PostgreSQL service:

    sudo systemctl restart postgresql

Per avviare *psql* (un terminale interattivo per PostgreSQL) come utente postgres incorporato, eseguire questo comando:

    sudo -u postgres psql

Creare un nuovo account utente utilizzando il nome utente dell'account Linux utilizzato per l'accesso. Creare una password:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Accedere a psql:

    psql

Importare i dati in un nuovo database:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

A questo punto, esaminiamo i dati ed eseguire alcune query utilizzando SQuirreL SQL, uno strumento grafico che è possibile utilizzare per interagire con i database tramite un driver JDBC.

Per iniziare, nel menu **Applicazioni** aprire SQL SQuirreL. Per configurare il driver:

1. Selezionare**Driver di visualizzazione di** **Windows** > .
1. Fare clic con il pulsante destro del mouse su **PostgreSQL** e selezionare **Modifica driver**.
1. Selezionare **Percorso** > classe supplementare**Aggiungi**.
1. In **Nome file**immettere **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Selezionare **Apri**.
1. Selezionare **Elenca driver**. Per **Nome classe**, selezionare **org.postgresql.Driver**, quindi **scegliere OK**.

Per configurare la connessione al server locale:

1. Selezionare Alias visualizzazione **Windows.Select Windows** > **View Aliases.**
1. Selezionare **+** il pulsante per creare un nuovo alias. Per il nuovo nome alias, immettere **Database posta indesiderata**. 
1. Per **Driver**, selezionare **PostgreSQL**.
1. Impostare l'URL su **jdbc:postgresql://localhost/spam**.
1. Immettere nome utente e password.
1. Selezionare **OK**.
1. Per aprire la finestra **Connection** (Connessione), fare doppio clic sull'alias **Spam database** (Database di posta indesiderata).
1. Selezionare **Connetti**.

Per eseguire alcune query:

1. Selezionare la scheda **SQL** .
1. Nella casella della query nella **SQL** parte superiore della scheda `SELECT * from data;`SQL immettere una query di base, ad esempio .
1. Per eseguire la query, premete Ctrl-Invio. Per impostazione predefinita, SQuirreL SQL restituisce le prime 100 righe della query.

Esistono molte altre query che è possibile eseguire per esplorare questi dati. Ad esempio, in cosa differisce la frequenza della parola *make* tra posta indesiderata e non indesiderata?

    SELECT avg(word_freq_make), spam from data group by spam;

Oppure, quali sono le caratteristiche di e-mail che spesso contengono *3d*?

    SELECT * from data order by word_freq_3d desc;

La maggior parte delle e-mail che hanno un alto evento di *3d* a quanto pare sono spam. Queste informazioni potrebbero essere utili per la creazione di un modello predittivo per classificare i messaggi di posta elettronica.

Se si desidera eseguire l'apprendimento automatico utilizzando i dati archiviati in un database PostgreSQL, prendere in considerazione l'utilizzo di [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse è un database basato su cloud con scalabilità orizzontale in grado di elaborare enormi volumi di dati, sia relazionali che non relazionali. Per altre informazioni, vedere [Informazioni su Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Per connettersi al data warehouse e creare la tabella, eseguire questo comando al prompt dei comandi:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Al prompt dei comandi sqlcmd eseguire questo comando:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copiare i dati utilizzando bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Il file scaricato contiene terminazioni di riga in stile Windows. Lo strumento bcp prevede terminazioni di linea in stile Unix. Utilizzare il flag -r per indicare bcp.

Quindi, eseguire una query utilizzando sqlcmd:Then, query by using sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

È inoltre possibile eseguire una query utilizzando SQuirreL SQL. Seguire passaggi simili a PostgreSQL utilizzando il driver JDBC di SQL Server. Il driver JDBC si trova nella cartella /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica degli articoli che illustrano le attività che costituiscono il processo di data science in Azure, vedere [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Per una descrizione delle procedure dettagliate end-to-end che illustrano i passaggi del processo di analisi scientifica dei dati del team per scenari specifici, vedere [Procedure dettagliate](../team-data-science-process/walkthroughs.md)del processo di analisi scientifica dei dati del team . Le procedure dettagliate illustrano anche come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.
