--- 
title: Scenario reale di manutenzione predittiva| Microsoft Docs
description: Scenario reale di manutenzione predittiva con PySpark
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>Scenario reale di manutenzione predittiva


L'impatto del tempo di inattività delle apparecchiature non pianificato può danneggiare un'azienda. È quindi fondamentale mantenere in funzione le apparecchiature sul campo per ottimizzare l'uso e le prestazioni, riducendo al minimo i costosi tempi di inattività non pianificati. L'identificazione preventiva dei problemi può aiutare ad allocare una quantità limitata di risorse di manutenzione in modo più conveniente e migliorare la qualità e i processi della supply chain. 

Per questo scenario, viene usato un [set di dati relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) per guidare l'utente nell'esecuzione dei passaggi principali, dall'inserimento dei dati, alla progettazione delle funzionalità, alla creazione dei modelli, fino all'operazionalizzazione e alla distribuzione del modello. Il codice per l'intero processo è scritto in PySpark e implementato usando notebook Jupyter in Azure ML Workbench. Il modello migliore viene infine operazionalizzato usando Gestione modelli di Azure Machine Learning per l'uso in un ambiente di produzione per l'esecuzione di stime dei guasti in tempo reale.   

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte

Il collegamento al repository GitHub pubblico è il seguente: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Uno dei principali problemi che le industrie che fanno largo uso di asset devono affrontare sono i costi significativi associati a ritardi dovuti a problemi meccanici. La maggior parte delle aziende è interessata a prevedere il verificarsi di tali problemi, in modo da prevenirli proattivamente prima che si verifichino. Ciò riduce i costi, riducendo i tempi di inattività e, in alcuni casi, aumentando la sicurezza. Per una spiegazione dettagliata dei casi d'uso comuni e dell'approccio alla modellazione per la manutenzione predittiva, vedere lo [studio sulla manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance).

Questo scenario sfrutta le idee dello studio allo scopo di fornire i passaggi per implementare un modello predittivo per uno scenario basato su una sintesi di diversi problemi aziendali reali. L'esempio riunisce elementi di dati comuni osservati tra molti casi d'uso di manutenzione predittiva.

Il problema aziendale per questi dati simulati consiste nel prevedere i problemi causati dai guasti dei componenti. La domanda che l'azienda si pone è quindi "*Qual è la probabilità che una macchina si arresti a causa di un guasto di un componente*?" Questo problema viene formattato come problema di classificazione multiclasse (più componenti per macchina) e viene usato un algoritmo di apprendimento automatico per creare il modello predittivo. Viene eseguito il training del modello sui dati cronologici raccolti dalle macchine. In questo scenario l'utente esegue i diversi passaggi di implementazione di tale modello nell'ambiente di Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/en-us/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
* I risultati intermedi per l'uso nei notebook Jupyter in questo scenario vengono archiviati in un contenitore di archiviazione BLOB di Azure. Le istruzioni per configurare un account di archiviazione di Azure sono disponibili tramite questo [collegamento](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage). 
* Per l'[operazionalizzazione](https://github.com/Azure/Machine-Learning-Operationalization) del modello, è preferibile che l'utente esegua un [motore Docker](https://www.docker.com/), installato e in esecuzione in locale. In caso contrario, è possibile usare l'opzione cluster, ma tenere presente che l'esecuzione di un [servizio contenitore di Azure](https://azure.microsoft.com/en-us/services/container-service/) è spesso costosa.
* Questo scenario presuppone che Azure ML Workbench sia in esecuzione in un computer Windows 10 con il motore Docker installato in locale. 
* Lo scenario è stato compilato e testato in un computer Windows 10 con la specifica seguente: CPU Intel Core i7-4600U a 2,10 GHz, 8 GB di RAM, sistema operativo a 64 bit, processore basato su x64 con versione Docker 17.06.0-ce-win19 (12801). 
* L'operazionalizzazione del modello è stata eseguita usando questa versione dell'interfaccia della riga di comando di Azure ML: azure-cli-ml==0.1.0a22

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Predictive Maintenance" (Manutenzione predittiva) e selezionare il modello
5.  Fare clic su **Crea**

## <a name="data-description"></a>Descrizione dei dati

I [dati simulati](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sono costituiti da cinque file con valori delimitati da virgole (CSV). 

* [Macchine](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): caratteristiche che differenziano ogni macchina. Ad esempio, età e modello.
* [Errore](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): il log degli errori contiene errori che non causano l'arresto, generati mentre la macchina è ancora in funzione. Questi errori non vengono considerati guasti, anche se potrebbero indicare un evento di guasto futuro. Data e ora dell'errore sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Manutenzione](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): il log di manutenzione contiene i record per la manutenzione pianificata e non pianificata. La manutenzione pianificata corrisponde alla regolare ispezione dei componenti, la manutenzione non pianificata potrebbe essere necessaria in seguito a guasti meccanici o a una riduzione delle prestazioni. Data e ora della manutenzione sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): i dati delle serie temporali di telemetria sono costituiti da misurazioni di tensione, rotazione, pressione e sensori di vibrazione, raccolte da ogni macchina in tempo reale. Viene calcolata la media dei dati nel corso di un'ora e i dati vengono archiviati nei log di telemetria.
* [Guasti](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): i guasti corrispondono alle sostituzioni dei componenti nel log di manutenzione. Ogni record contiene l'ID della macchina, il tipo di componente e la data e l'ora della sostituzione. Questi record vengono usati per creare le etichette di apprendimento automatico che il modello sta cercando di stimare.

Vedere lo scenario di [inserimento di dati](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) nei notebook Jupyter nella sezione relativa al codice per scaricare i set di dati non elaborati dal repository GitHub e creare i set di dati PySpark per l'analisi.

## <a name="scenario-structure"></a>Struttura dello scenario
Il contenuto per lo scenario è disponibile nel [repository GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Nel repository c'è un file [leggimi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) che descrive i processi, dalla preparazione dei dati fino alla creazione di alcuni modelli e infine all'operazionalizzazione di uno dei modelli migliori. I quattro notebook Jupyter sono disponibili nella cartella [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) nel repository.   

Di seguito viene descritto il flusso di lavoro dettagliato dello scenario. Lo scenario end-to-end è scritto in PySpark e suddiviso in quattro notebook, come illustrato di seguito:

* [Inserimento di dati](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): questo notebook gestisce l'inserimento di dati dei cinque file CSV di input, esegue una pulizia preliminare, crea alcuni grafici di riepilogo per verificare il download dei dati e infine archivia i set di dati risultanti in un contenitore BLOB di Azure per l'uso nel notebook successivo.

* [Progettazione delle funzionalità](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): usando il set di dati pulito nel passaggio precedente, vengono create funzioni di ritardo per i sensori di telemetria e vengono progettate funzionalità aggiuntive per creare variabili come i giorni dall'ultima sostituzione. Infine, i guasti vengono contrassegnati associandoli ai record rilevanti, per creare un set di dati finale salvato in un BLOB di Azure per il passaggio successivo. 

* [Creazione dei modelli](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): il set di dati finale con progettazione delle funzionalità viene quindi diviso in due, ovvero un set di dati di training e un set di dati di test, in base a un indicatore di data e ora. Vengono quindi creati due modelli, ovvero un classificatore di foreste casuali e un classificatore dell'albero delle decisioni in base al set di dati di training e vengono assegnati i punteggi nel set di dati di test. 

* [Operazionalizzazione e distribuzione del modello](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): il modello migliore creato nel passaggio precedente viene quindi salvato come file con estensione model insieme al file di schema JSON rilevante per la distribuzione. Le funzioni init() e run() vengono testate localmente prima di operazionalizzare il modello usando l'ambiente di Gestione modelli di Azure Machine Learning per l'uso in un ambiente di produzione per l'esecuzione di stime dei guasti in tempo reale.  

## <a name="conclusion"></a>Conclusioni

Questo scenario fornisce al lettore una panoramica di come creare una soluzione end-to-end di manutenzione predittiva usando PySpark nell'ambiente notebook Jupyter in Azure ML Workbench. Lo scenario fornisce al lettore anche indicazioni per eseguire in modo semplice l'operazionalizzazione e la distribuzione usando l'ambiente di Gestione modelli di Azure Machine Learning per l'uso in un ambiente di produzione per l'esecuzione di stime dei guasti in tempo reale. Il lettore può quindi modificare le parti pertinenti dello scenario in base alle specifiche esigenze aziendali.  

## <a name="references"></a>Riferimenti

Questo caso d'uso è stato sviluppato in precedenza su più piattaforme come indicato di seguito:

* [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Guida alla modellazione per la manutenzione predittiva con SQL R Services)
* [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)



