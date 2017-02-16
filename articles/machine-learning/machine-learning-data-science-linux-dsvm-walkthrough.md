---
title: Analisi scientifica dei dati in una macchina virtuale Linux per l&quot;analisi scientifica dei dati | Documentazione Microsoft
description: "Come eseguire varie attività comuni di analisi scientifica dei dati con la macchina virtuale Linux per l&quot;analisi scientifica dei dati."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev;paulsh
translationtype: Human Translation
ms.sourcegitcommit: d0075eec26c2131f2019e7aca4c00d2d63cc976b
ms.openlocfilehash: 80051996b0c39c53da63dc6b7bc75c869f692575


---
# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Analisi scientifica dei dati in una macchina virtuale Linux per l'analisi scientifica dei dati
Questa procedura dettagliata illustra come eseguire varie attività comuni di analisi scientifica dei dati con la macchina virtuale Linux per l'analisi scientifica dei dati. La macchina virtuale Linux per l'analisi scientifica dei dati (DSVM) è un'immagine di macchina virtuale, disponibile in Azure, in cui è preinstallata una raccolta di strumenti usati comunemente per l'analisi dei dati e l'apprendimento automatico. I componenti software principali sono elencati nell'argomento [Effettuare il provisioning di una macchina virtuale Linux per l'analisi scientifica dei dati](machine-learning-data-science-linux-dsvm-intro.md). L'immagine di macchina virtuale permette di iniziare le attività di analisi scientifica dei dati in pochi minuti, senza dover installare e configurare ogni strumento singolarmente. Se necessario, è possibile aumentare facilmente le prestazioni della macchina virtuale e arrestarla quando non viene usata, caratteristiche che rendono questa risorsa flessibile e conveniente.

Le attività di analisi scientifica dei dati illustrate in questa procedura dettagliata seguono i passaggi descritti nel [Processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Questo processo offre un approccio sistematico all'analisi scientifica dei dati, che permette ai team di data scientist di collaborare in modo efficace al ciclo di vita della compilazione di applicazioni intelligenti. Il processo di analisi scientifica dei dati fornisce anche un framework iterativo di analisi scientifica dei dati da seguire.

Questa procedura dettagliata illustra il set di dati [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Si tratta di un set di messaggi di posta elettronica contrassegnati come posta indesiderata o ham, ovvero non indesiderata. Il set contiene anche alcune statistiche sul contenuto dei messaggi di posta elettronica che verranno illustrate più avanti in questa procedura dettagliata.

## <a name="prerequisites"></a>Prerequisiti
Prima di usare una macchina virtuale Linux per l'analisi scientifica dei dati, è necessario avere a disposizione quanto segue:

* Una **sottoscrizione di Azure**. Se non è già disponibile, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free/).
* Una [**macchina virtuale Linux per l'analisi scientifica dei dati**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Per informazioni sul provisioning di questa macchina virtuale, vedere [Effettuare il provisioning di una macchina virtuale Linux per l'analisi scientifica dei dati](machine-learning-data-science-linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) installato nel computer con una sessione di XFCE aperta. Per informazioni sull'installazione e la configurazione di un **client X2Go**, vedere [Installazione e configurazione del client X2Go](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client). 
* Un **account Azure ML**. Se non è già disponibile, è possibile iscriversi e ottenere un nuovo account nella [home page di Azure ML](https://studio.azureml.net/). Il livello di utilizzo gratuito permette di iniziare.

## <a name="download-the-spambase-dataset"></a>Scaricare il set di dati spambase
Il set di dati [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) è un set di dati relativamente piccolo che contiene solo 4601 esempi. Grazie alle dimensioni ridotte può essere usato per dimostrare alcune delle funzionalità principali della macchina virtuale per l'analisi scientifica dei dati, perché i requisiti in termini di risorse rimangono contenuti.

> [!NOTE]
> Questa procedura dettagliata è stata creata in una macchina virtuale Linux per l'analisi scientifica dei dati di dimensioni D2 v2. Le dimensioni della DSVM permettono di gestire le procedure descritte in questa procedura dettagliata.
>
>

Se è necessario più spazio di archiviazione, è possibile creare altri dischi e collegarli alla macchina virtuale. Dato che questi dischi usano l'archiviazione di Azure persistente, i relativi dati vengono conservati anche quando il server viene arrestato o sottoposto a un nuovo provisioning in seguito a un ridimensionamento. Per aggiungere un disco e collegarlo alla macchina virtuale, seguire le istruzioni in [Aggiungere un disco a una VM Linux](../virtual-machines/virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questa procedura usa l'interfaccia della riga di comando di Azure, già installata nella DSVM, e può quindi essere eseguita interamente dalla macchina virtuale stessa. Per aumentare lo spazio di archiviazione, è anche possibile usare l'[archiviazione file di Azure](../storage/storage-how-to-use-files-linux.md).

Per scaricare i dati, aprire una finestra del terminale ed eseguire questo comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Il file scaricato non ha una riga di intestazione. È quindi necessario creare un altro file che abbia un'intestazione. Eseguire questo comando per creare un file con le intestazioni appropriate:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Quindi, concatenare i due file usando il comando seguente:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Il set di dati include diversi tipi di statistiche per ogni messaggio di posta elettronica:

* Le colonne come ***word\_freq\_WORD*** indicano la percentuale di parole nel messaggio di posta elettronica che corrispondono a *WORD*. Ad esempio, se *word\_freq\_make* è 1, l'1% di tutte le parole nel messaggio di posta elettronica è costituito dalla parola *make*.
* Le colonne come ***char\_freq\_CHAR*** indicano la percentuale di caratteri *CHAR* nel messaggio di posta elettronica.
* ***capital\_run\_length\_longest*** è la lunghezza massima di una sequenza di lettere maiuscole.
* ***capital\_run\_length\_average*** è la lunghezza media di tutte le sequenze di lettere maiuscole.
* ***capital\_run\_length\_total*** è la lunghezza totale di tutte le sequenze di lettere maiuscole.
* ***spam*** indica se il messaggio di posta elettronica è stato considerato posta indesiderata o meno: 1 = posta indesiderata, 0 = posta non indesiderata.

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Esplorare il set di dati con Microsoft R Open
Si passa ora all'esame dei dati e all'esecuzione di alcune attività di apprendimento automatico di base con R. La macchina virtuale per l'analisi scientifica dei dati viene fornita con [Microsoft R Open](https://mran.revolutionanalytics.com/open/) preinstallato. Le librerie matematiche multithreading in questa versione di R offrono prestazioni migliori rispetto a diverse versioni a thread singolo. Microsoft R Open garantisce anche la riproducibilità grazie all'uso di uno snapshot del repository dei pacchetti CRAN.

Per ottenere copie degli esempi di codice usati in questa procedura dettagliata, clonare il repository **Azure-Machine-Learning-Data-Science** usando Git, che è preinstallato nella macchina virtuale. Dalla riga di comando di Git, eseguire:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Aprire una finestra del terminale e avviare una nuova sessione di R con la console interattiva di R.

> [!NOTE]
> Per le procedure seguenti è possibile usare anche RStudio. Per installare RStudio, eseguire questo comando a un terminale: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Per importare i dati e configurare l'ambiente, eseguire:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Per visualizzare le statistiche di riepilogo relative a ogni colonna, usare:

    summary(data)

Per una visualizzazione diversa dei dati, usare:

    str(data)

Questa visualizzazione mostra il tipo di ogni variabile e i primi valori nel set di dati.

La colonna *spam* è stata letta come Integer, ma in realtà si tratta di un fattore o una variabile categorica. Per impostarne il tipo, usare:

    data$spam <- as.factor(data$spam)

Per effettuare alcune analisi esplorative, usare il pacchetto [ggplot2](http://ggplot2.org/) , una diffusa libreria grafica per R già installata nella macchina virtuale. Grazie ai dati di riepilogo visualizzati in precedenza, sono disponibili statistiche di riepilogo sulla frequenza del carattere punto esclamativo. I comandi seguenti permettono di tracciare tali frequenze:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Per eliminare l'alterazione causata dalla barra dello zero, usare:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

La densità non semplice superiore a 1 sembra interessante. Per esaminare solo quei dati, eseguire:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Per dividere quindi i dati in posta indesiderata e non indesiderata, eseguire:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

In base a questi esempi è possibile creare tracciati simili delle altre colonne per esplorare i dati in esse contenuti.

## <a name="train-and-test-an-ml-model"></a>Eseguire il training di un modello ML e testarlo
A questo punto è possibile eseguire il training di un paio di modelli di apprendimento automatico per classificare i messaggi di posta elettronica nel set di dati come posta indesiderata o non indesiderata. Questa sezione illustra come eseguire il training di un modello di albero delle decisioni e di un modello di foresta casuale e quindi testarne la correttezza delle previsioni.

> [!NOTE]
> Il pacchetto rpart, che sta per partizionamento ricorsivo e alberi di regressione, usato nel codice seguente è già installato nella macchina virtuale per l'analisi scientifica dei dati.
>
>

Dividere prima di tutto il set di dati in set di training e set di test:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Quindi, creare un albero delle decisioni per classificare i messaggi di posta elettronica.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Il risultato è il seguente:

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

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

Viene ora esaminato un modello di foresta casuale. Le foreste casuali permettono di eseguire il training di numerosi alberi delle decisioni e restituiscono una classe che rappresenta la modalità delle classificazioni da tutti i singoli alberi delle decisioni. Offrono un approccio più efficace all'apprendimento automatico perché compensano la tendenza all'overfitting del set di dati di training da parte del modello di albero delle decisioni.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Distribuire un modello in Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (Azure ML) è un servizio cloud che semplifica la compilazione e la distribuzione di modelli di analisi predittiva. Una funzionalità interessante di Azure ML è la possibilità di pubblicare qualsiasi funzione R come servizio Web. Il pacchetto R di Azure ML permette di eseguire la distribuzione direttamente dalla sessione di R nella macchina virtuale per l'analisi scientifica dei dati.

Per distribuire il codice dell'albero delle decisioni della sezione precedente, occorre eseguire l'accesso ad Azure Machine Learning Studio. A tale scopo sono necessari l'ID dell'area di lavoro e un token di autorizzazione. Per trovare questi valori e usarli per inizializzare le variabili di Azure ML, procedere come segue:

Selezionare **SETTINGS** (Impostazioni) dal menu a sinistra. Prendere nota del valore **WORKSPACE ID**(ID area di lavoro). ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Selezionare **Authorization Tokens** (Token di autorizzazione) dal menu in alto e prendere nota del valore di **Primary Authorization Token** (Token di autorizzazione primario).![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Caricare il pacchetto di **Azure ML** e quindi impostare i valori delle variabili con il token e l'ID area di lavoro nella sessione di R nella DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Il modello viene semplificato per agevolare l'implementazione di questa dimostrazione. Nell'albero delle decisioni selezionare le tre variabili più vicine alla radice e compilare un nuovo albero usando solo quelle tre variabili:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

È necessaria una funzione di previsione che usa le funzionalità come input e restituisce i valori previsti:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Pubblicare la funzione predictSpam in Azure ML usando la funzione **publishWebService** :

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Questa funzione accetta la funzione **predictSpam**, crea un servizio Web denominato **spamWebService** con gli input e gli output definiti e restituisce informazioni relative al nuovo endpoint.

Per visualizzare i dettagli del servizio Web pubblicato, inclusi l'endpoint API e le chiavi di accesso, usare il comando:

    spamWebService[[2]]

Per fare una prova sulle prime dieci righe del set di test, eseguire:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Usare altri strumenti disponibili
Le sezioni seguenti illustrano come usare alcuni degli strumenti installati nella macchina virtuale Linux per l'analisi scientifica dei dati. Gli strumenti descritti sono elencati di seguito:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) è uno strumento che consente un'implementazione di albero con boosting rapida e accurata.

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

XGBoost permette anche di eseguire chiamate da Python o da una riga di comando.

## <a name="python"></a>Python
Per lo sviluppo tramite Python, nella DSVM sono installate le distribuzioni Anaconda Python 2.7 e 3.5.

> [!NOTE]
> La distribuzione Anaconda include [Condas](http://conda.pydata.org/docs/index.html), che può essere usato per creare ambienti personalizzati per Python in cui sono installati pacchetti diversi e/o versioni diverse.
>
>

Leggere una parte del set di dati spambase e classificare i messaggi di posta elettronica con macchine a vettori di supporto in scikit-learn:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per eseguire previsioni, usare:

    clf.predict(X.ix[0:20, :])

Per illustrare la pubblicazione di un endpoint di Azure ML viene creato un modello più semplice con tre variabili, come si è fatto in precedenza per la pubblicazione del modello R.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per pubblicare il modello in Azure ML, eseguire:

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
> È disponibile solo per Python 2.7 e non è ancora supportato nella versione 3.5. Eseguire con **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>JupyterHub
La distribuzione Anaconda nella DSVM include un notebook di Jupyter, un ambiente multipiattaforma per la condivisione di analisi e codice Python, R o Julia. Notebook di Jupyter è accessibile tramite JupyterHub. Per eseguire l'accesso, usare il nome utente e la password locali di ***https://\<Nome DNS o indirizzo IP della VM\>:8000/***. Tutti i file di configurazione per JupyterHub si trovano nella directory **/etc/jupyterhub**.

Nella macchina virtuale sono già installati diversi notebook di esempio:

* Per un notebook di Python di esempio, vedere [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) .
* Per un notebook di [R](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) di esempio, vedere **IntroTutorialinR** .
* Per un altro notebook di [Python](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) di esempio, vedere **IrisClassifierPyMLWebService** .

> [!NOTE]
> Il linguaggio Julia è disponibile anche dalla riga di comando nella macchina virtuale Linux per l'analisi scientifica dei dati.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) , acronimo di R Analytical Tool To Learn Easily, è uno strumento grafico di R per il data mining. È dotato di un'interfaccia intuitiva che permette di caricare, esplorare e trasformare i dati e di creare e valutare i modelli in modo molto semplice.  Per una procedura dettagliata che ne illustra le funzionalità, vedere l'articolo [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) (Rattle: un'interfaccia utente grafica di data mining per R).

Installare e avviare Rattle con i comandi seguenti:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> L'installazione nella DSVM non è obbligatoria, ma Rattle può richiedere di installare pacchetti aggiuntivi al momento del caricamento.
>
>

Rattle usa un'interfaccia basata su schede. La maggior parte delle schede corrisponde ai passaggi del [Processo di analisi scientifica dei dati](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), come il caricamento dei dati o l'esplorazione. Il processo di analisi scientifica dei dati va da sinistra a destra nelle schede, ma l'ultima scheda contiene un log dei comandi R eseguiti da Rattle.

Per caricare e configurare il set di dati:

* Per caricare il file, selezionare la scheda **Data** (Dati).
* Fare clic sul selettore accanto a **Filename** (Nome file) e scegliere **spambaseHeaders.data**.
* Per caricare il file, selezionare **Execute** (Esegui) nella riga superiore dei pulsanti. Verrà visualizzato un riepilogo di ogni colonna, incluso il tipo di dati identificato, se si tratta di un input, una destinazione o un altro tipo di variabile e il numero di valori univoci.
* Rattle ha identificato correttamente la colonna **spam** come destinazione. Selezionare la colonna e quindi impostare **Target Data Type** (Tipo di dati di destinazione) su **Categoric** (Categorico).

Per esplorare i dati:

* Selezionare la scheda **Explore** (Esplora).
* Fare clic su **Summary** (Riepilogo) e quindi su **Execute** (Esegui) per visualizzare alcune informazioni sui tipi di variabili e le statistiche di riepilogo.
* Per visualizzare altri tipi di statistiche sulle singole variabili, selezionare altre opzioni, ad esempio **Describe** (Descrizione) o **Basics** (Informazioni di base).

La scheda **Explore** (Esplora) permette anche di generare molti tracciati dettagliati. Per tracciare un istogramma dei dati:

* Scegliere **Distributions**(Distribuzioni).
* Selezionare **Histogram** (Istogramma) per **word_freq_remove** e **word_freq_you**.
* Scegliere **Execute**(Esegui). Entrambi i tracciati di densità verranno visualizzati in un'unica area grafica, in cui appare chiaro che nei messaggi di posta elettronica la parola "you" è molto più frequente della parola "remove".

Anche i tracciati di correlazione sono interessanti. Per crearne uno:

* Scegliere **Correlation** (Correlazione) per **Type** (Tipo).
* Scegliere **Execute**(Esegui).
* Rattle avvisa l'utente che è consigliabile usare un massimo di 40 variabili. Scegliere **Yes** (Sì) per visualizzare il tracciato.

Vengono evidenziate alcune correlazioni interessanti. Ad esempio, la parola "technology" è strettamente correlata a "HP" e "labs". È strettamente correlata anche a "650", perché l'indicativo di località dei donatori di set di dati è 650.

I valori numerici per le correlazioni tra le parole sono disponibili nella finestra di esplorazione. È interessante notare, ad esempio, che la parola "technology" è correlata negativamente a "your" e "money".

Rattle può trasformare il set di dati per gestire alcuni problemi comuni. Ad esempio, permette di ridimensionare le funzionalità, attribuire valori mancanti, gestire gli outlier e rimuovere variabili o osservazioni con dati mancanti. Rattle può anche identificare le regole di associazione tra le osservazioni e/o le variabili. Queste schede non rientrano nell'ambito di questa procedura dettagliata introduttiva.

Rattle permette anche di eseguire l'analisi a cluster. Escludere ora alcune funzionalità per migliorare la leggibilità dell'output. Nella scheda **Data** (Dati) scegliere **Ignore** (Ignora) accanto a ogni variabile, a eccezione di questi dieci elementi:

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

Tornare quindi alla scheda **Cluster**, scegliere **KMeans** e impostare *Number of clusters* (Numero di cluster) su 4. Scegliere **Execute**(Esegui). I risultati verranno visualizzati nella finestra di output. Un cluster ha una frequenza elevata di "george" e "hp" ed è probabilmente un messaggio di lavoro legittimo.

Per compilare un modello di apprendimento automatico ad albero decisionale:

* Scegliere la scheda **Model** (Modello).
* Selezionare **Tree** (Albero) per **Type** (Tipo).
* Selezionare **Execute** (Esegui) per visualizzare l'albero in formato testo nella finestra di output.
* Fare clic sul pulsante **Draw** (Disegna) per visualizzarne una versione grafica. Il risultato è simile all'albero ottenuto in precedenza con *rpart*.

Una delle funzionalità interessanti di Rattle è la possibilità di eseguire diversi metodi di apprendimento automatico e valutarli rapidamente. Di seguito è riportata la procedura:

* Scegliere **All** (Tutti) per **Type** (Tipo).
* Scegliere **Execute**(Esegui).
* Al termine è possibile fare clic sul singolo **tipo**, ad esempio **SVM**, e visualizzare i risultati.
* È anche possibile confrontare le prestazioni dei modelli nel set di convalida usando la scheda **Evaluate** (Valuta). Ad esempio, la selezione **Error Matrix** (Matrice degli errori) mostra la matrice di confusione, l'errore generale e l'errore medio di classe per ogni modello nel set di convalida.
* È anche possibile tracciare le curve ROC, eseguire analisi di sensibilità e altri tipi di valutazioni del modello.

Al termine della compilazione dei modelli, selezionare la scheda **Log** per visualizzare il codice R eseguito da Rattle durante la sessione. Per salvarlo, è possibile usare il pulsante **Export** (Esporta).

> [!NOTE]
> La versione corrente di Rattle contiene un bug. Per modificare lo script o usarlo per ripetere i passaggi in un secondo momento, è necessario inserire un carattere # davanti a *Export this log ... * (Esporta questo log) nel testo del log.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL e Squirrel SQL
La DSVM viene fornita con PostgreSQL installato. PostgreSQL è un sofisticato database relazionale open source. Questa sezione illustra come eseguire query sul set di dati di posta indesiderata dopo averlo caricato in PostgreSQL.

Prima di caricare i dati è necessario consentire l'autenticazione della password da localhost. Al prompt dei comandi, eseguire:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Verso la fine del file di configurazione sono presenti diverse righe con informazioni dettagliate sulle connessioni consentite:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modificare la riga "IPv4 local connections" per l'uso di md5 anziché ident, per poter eseguire l'accesso con un nome utente e una password:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Riavviare il servizio postgres:

    sudo systemctl restart postgresql

Per avviare psql, il terminale interattivo per PostgreSQL, come l'utente postgres predefinito, eseguire questo comando da un prompt dei comandi:

    sudo -u postgres psql

Creare un nuovo account utente usando lo stesso nome utente dell'account Linux a cui si è connessi e assegnare una password:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Accedere a psql come utente:

    psql

Quindi, importare i dati in un nuovo database:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Si passa ora all'elaborazione dei dati e all'esecuzione di query con **Squirrel SQL**, uno strumento grafico che permette di interagire con i database tramite un driver JDBC.

Per iniziare, avviare Squirrel SQL dal menu delle applicazioni. Per configurare il driver:

* Selezionare **Windows** e quindi **View Drivers** (Visualizza driver).
* Fare clic con il pulsante destro del mouse su **PostgreSQL** e selezionare **Modify Driver** (Modifica driver).
* Selezionare **Extra Class Path** (Percorso classe extra) e quindi **Add** (Aggiungi).
* Immettere ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** per **File Name** (Nome file).
* Scegliere **Open**(Apri).
* Scegliere List Drivers (Elenca driver), selezionare **org.postgresql.Driver** in **Class Name** (Nome classe) e quindi scegliere **OK**.

Per configurare la connessione al server locale:

* Selezionare **Windows** e quindi **View Aliases** (Visualizza alias).
* Fare clic sul pulsante **+** per creare un nuovo alias.
* Denominare l'alias *Spam database* (Database di posta indesiderata) e scegliere **PostgreSQL** nell'elenco a discesa **Driver**.
* Impostare l'URL su *jdbc:postgresql://localhost/spam*.
* Immettere il proprio *nome utente* e la *password*.
* Fare clic su **OK**.
* Per aprire la finestra **Connection** (Connessione), fare doppio clic sull'alias ***Spam database*** (Database di posta indesiderata).
* Selezionare **Connessione**.

Per eseguire alcune query:

* Selezionare la scheda **SQL** .
* Immettere una query semplice, ad esempio `SELECT * from data;` , nella casella di testo della query nella parte superiore della scheda SQL.
* Premere **CTRL + INVIO** per eseguirla. Per impostazione predefinita, Squirrel SQL restituisce le prime 100 righe della query.

È possibile eseguire molte altre query per esplorare questi dati. Ad esempio, in cosa differisce la frequenza della parola *make* tra posta indesiderata e non indesiderata?

    SELECT avg(word_freq_make), spam from data group by spam;

Oppure, quali sono le caratteristiche dei messaggi di posta elettronica che contengono spesso *3d*?

    SELECT * from data order by word_freq_3d desc;

La maggior parte dei messaggi con un'occorrenza elevata della parola *3d* sembra essere posta indesiderata. Questa funzionalità potrebbe quindi essere utile per compilare un modello predittivo per la classificazione dei messaggi di posta elettronica.

Per eseguire l'apprendimento automatico con dati archiviati in un database PostgreSQL, è consigliabile usare [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse è un database basato sul cloud, con possibilità di aumentare il numero di istanze, che può elaborare volumi massivi di dati sia relazionali che non relazionali. Per altre informazioni, vedere [Informazioni su Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Per connettersi al data warehouse e creare la tabella, eseguire questo comando al prompt dei comandi:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Al prompt di sqlcmd, eseguire:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Per copiare dati con bcp, eseguire:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Le terminazioni di riga nel file scaricato sono in stile Windows, mentre bcp prevede lo stile UNIX. È quindi necessario segnalarlo a bcp con il flag -r.
>
>

Eseguire query con sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

È anche possibile eseguire query con Squirrel SQL. Seguire una procedura simile per PostgreSQL usando Microsoft JDBC Driver per SQL Server, disponibile in ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica degli argomenti che forniscono informazioni dettagliate sulle attività che costituiscono il processo di analisi scientifica dei dati in Azure, vedere [Processo di analisi scientifica dei dati per i team](http://aka.ms/datascienceprocess).

Per una descrizione di altre procedure dettagliate complete che illustrano i passaggi del processo per scenari specifici, vedere [Procedure dettagliate del Processo di analisi scientifica dei dati per i team](data-science-process-walkthroughs.md). Le procedure dettagliate illustrano anche come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.



<!--HONumber=Jan17_HO1-->


