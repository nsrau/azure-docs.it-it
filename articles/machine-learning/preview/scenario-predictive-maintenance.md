---
title: Scenario reale di manutenzione predittiva| Microsoft Docs
description: Scenario reale di manutenzione predittiva con PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: dc73c052ad9e0fe12af5042289f304a0e48ae413
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>Scenario reale di manutenzione predittiva.

L'impatto del tempo di inattività delle apparecchiature non pianificato può danneggiare un'azienda. È quindi fondamentale mantenere in funzione le apparecchiature sul campo per ottimizzare l'uso e le prestazioni, riducendo al minimo i costosi tempi di inattività non pianificati. L'identificazione preventiva dei problemi può aiutare ad allocare una quantità limitata di risorse di manutenzione in modo più conveniente e migliorare la qualità e i processi della supply chain. 

Per questo scenario, viene usato un [set di dati relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) per guidare l'utente nell'esecuzione dei passaggi principali, dall'inserimento dei dati, alla progettazione delle funzionalità, alla creazione dei modelli, fino all'operazionalizzazione e alla distribuzione del modello. Il codice per l'intero processo è scritto in PySpark e implementato usando notebook Jupyter in Azure ML Workbench. Il modello migliore viene infine operazionalizzato usando Gestione modelli di Azure Machine Learning per l'uso in un ambiente di produzione per l'esecuzione di stime dei guasti in tempo reale.   

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte

Il collegamento al repository GitHub pubblico è il seguente: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Uno dei principali problemi che le industrie che fanno largo uso di asset devono affrontare sono i costi significativi associati a ritardi dovuti a problemi meccanici. La maggior parte delle aziende è interessata a prevedere il verificarsi di tali problemi, in modo da prevenirli proattivamente prima che si verifichino. L'obiettivo è ridurre i costi riducendo i tempi di inattività e, possibilmente, incrementare la sicurezza. Per una spiegazione dettagliata dei casi d'uso comuni e dell'approccio alla modellazione usato per la manutenzione predittiva, vedere lo [studio sulla manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance).

Questo scenario sfrutta le idee dello studio allo scopo di fornire i passaggi per implementare un modello predittivo per uno scenario basato su una sintesi di diversi problemi aziendali reali. L'esempio riunisce elementi di dati comuni osservati tra molti casi d'uso di manutenzione predittiva.

Il problema aziendale per questi dati simulati consiste nel prevedere i problemi causati dai guasti dei componenti. La domanda che l'azienda si pone è quindi "*Qual è la probabilità che una macchina si arresti a causa di un guasto di un componente*?" Questo problema viene formattato come problema di classificazione multiclasse (più componenti per macchina) e viene usato un algoritmo di apprendimento automatico per creare il modello predittivo. Viene eseguito il training del modello sui dati cronologici raccolti dalle macchine. In questo scenario l'utente esegue i diversi passaggi di implementazione di tale modello nell'ambiente di Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/en-us/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
* L'operazionalizzazione di Azure Machine Learning richiede un ambiente di distribuzione locale e un [account di gestione del modello](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

Questo esempio può essere eseguito in qualsiasi contesto di calcolo di Azure Machine Learning Workbench. Tuttavia, è consigliabile eseguirlo con almeno 16 GB di memoria. Questo scenario è stato compilato e testato in un computer Windows 10 che esegue uno standard DS4_V2 remoto [Macchina virtuale di data science per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

L'operazionalizzazione del modello è stata eseguita usando la versione 0.1.0 a 22 dell'interfaccia della riga di comando di Azure ML.

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Predictive Maintenance" (Manutenzione predittiva) e selezionare il modello
5.  Fare clic su **Crea**

## <a name="prepare-the-notebook-server-computation-target"></a>Preparare la destinazione di calcolo del server del notebook

Per l'esecuzione nel computer locale, dal menu `File` del Workbench AML, selezionare `Open Command Prompt` o `Open PowerShell CLI`. All'interno della finestra dell'infrastruttura della riga di comando eseguire i comandi seguenti:

`az ml experiment prepare --target docker --run-configuration docker`

È consigliabile l'esecuzione in una macchina virtuale per l'analisi scientifica dei dati per Linux (Ubuntu). Dopo aver configurato il DSVM, eseguire i due comandi seguenti:

`az ml computetarget attach --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword] --type remotedocker`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Con le immagini docker preparate, aprire il server Jupyter notebook o all'interno della scheda del notebook Workbench AML o per avviare un server basato sul browser, eseguire: `az ml notebook start`.

I Notebook vengono archiviati nella directory `Code` all'interno dell'ambiente Jupyter. I notebook vengono eseguiti in sequenza, a partire il primo (`Code\1_data_ingestion.ipynb`) notebook. Quando si apre ciascun notebook, viene chiesto di selezionare il kernel di calcolo. Scegliere [Project_Name]_Template [Desired_Connection_Name] e fare clic su Imposta Kernel.

## <a name="data-description"></a>Descrizione dei dati

I [dati simulati](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sono costituiti da cinque file con valori delimitati da virgole (CSV). Seguire i collegamenti per ottenere una descrizione ancora più dettagliata dei set di dati.

* [Macchine](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): caratteristiche che differenziano ogni macchina. Ad esempio, età e modello.
* [Errore](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): il log degli errori contiene errori che non causano l'arresto, generati mentre la macchina è ancora in funzione. Questi errori non vengono considerati guasti, anche se potrebbero indicare un evento di guasto futuro. Data e ora dell'errore sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Manutenzione](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): il log di manutenzione contiene i record per la manutenzione pianificata e non pianificata. La manutenzione pianificata corrisponde alla regolare ispezione dei componenti, la manutenzione non pianificata potrebbe essere necessaria in seguito a guasti meccanici o a una riduzione delle prestazioni. Data e ora della manutenzione sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): i dati delle serie temporali di telemetria sono costituiti da misurazioni di tensione, rotazione, pressione e sensori di vibrazione, raccolte da ogni macchina in tempo reale. Viene calcolata la media dei dati nel corso di un'ora e i dati vengono archiviati nei log di telemetria.
* [Guasti](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): i guasti corrispondono alle sostituzioni dei componenti nel log di manutenzione. Ogni record contiene l'ID della macchina, il tipo di componente e la data e l'ora della sostituzione. Questi record vengono usati per creare le etichette di apprendimento automatico che il modello sta cercando di stimare.

Vedere lo scenario di [inserimento di dati](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) nei notebook Jupyter nella sezione relativa al codice per scaricare i set di dati non elaborati dal repository GitHub e creare i set di dati PySpark per l'analisi.

## <a name="scenario-structure"></a>Struttura dello scenario
Il contenuto per lo scenario è disponibile nel [repository GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Nel repository c'è un file [leggimi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) che descrive i processi, dalla preparazione dei dati fino alla creazione di alcuni modelli e infine all'operazionalizzazione di uno dei modelli migliori. I quattro notebook Jupyter sono disponibili nella cartella [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) nel repository.   

Di seguito viene descritto il flusso di lavoro dettagliato dello scenario. Lo scenario end-to-end è scritto in PySpark e suddiviso in quattro notebook:

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Questo notebook scarica i cinque file di input con estensione csv, esegue alcune pulizie preliminari e visualizzazioni dei dati. Il notebook converte i dati nel formato PySpark e archivia i risultati in un contenitore BLOB di Azure per l'uso nell'attività di progettazione della funzionalità.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Usando il set di dati puliti dal passaggio precedente vengono creati il lag e le funzionalità aggregate per i sensori di telemetria. I conteggi degli errori, le sostituzioni dei componenti, le informazioni sul computer vengono unite ai dati di telemetria. Le sostituzioni dei componenti relative all'errore vengono usate per costruire le etichette che descrivono quale componente non ha funzionato. I dati della funzionalità dell'etichetta vengono salvati in un BLOB di Azure per l'attività di compilazione del modello.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Usando il set di dati della funzionalità con etichette, il notebook di modellazione suddivide i dati in set di dati di training e di sviluppo in data-timestamp. Il notebook è l'esperimento del set di installazione con i modelli `pyspark.ml.classification`. I dati di training sono vettorizzati e l'utente può sperimentare con un `DecisionTreeClassifier` o un `RandomForestClassifier`, manipolando gli iperparametri per individuare il modello migliore per le prestazioni. Le prestazioni sono determinate valutando le statistiche di misura sul set di dati di sviluppo. Queste statistiche vengono registrate nella schermata della fase di esecuzione di Workbench AML per il rilevamento. A ogni esecuzione, il notebook salva il modello risultante nel disco locale eseguendo il kernel Jupyter notebook. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Usando l'ultimo modello salvato in un disco locale (kernel Jupyter notebook), questo notebook compila i componenti per l'operazionalizzazione del modello in un servizio web di Azure. Gli asset operativi vengono compattati nel file `o16n.zip` archiviato in un altro contenitore BLOB di Azure. Il file compresso contiene:

* `service_schema.json` Il file di definizione dello schema per la distribuzione. 
* `pdmscore.py` Le funzioni init() e run() necessarie per il servizio web di Azure
* `pdmrfull.model` La directory di definizione del modello.
    
 Il notebook verifica le funzioni con la definizione del modello prima di creare il pacchetto degli asset di operazionalizzazione per la distribuzione. Le istruzioni per la distribuzione sono incluse alla fine del blocco appunti.

## <a name="conclusion"></a>Conclusioni

Questo scenario fornisce al lettore una panoramica di come creare una soluzione end-to-end di manutenzione predittiva usando PySpark nell'ambiente notebook Jupyter in Azure ML Workbench. Lo scenario fornisce al lettore anche indicazioni per eseguire in modo semplice l'operazionalizzazione e la distribuzione usando l'ambiente di Gestione modelli di Azure Machine Learning per l'uso in un ambiente di produzione per l'esecuzione di stime dei guasti in tempo reale. Il lettore può quindi modificare le parti pertinenti dello scenario in base alle specifiche esigenze aziendali.  

## <a name="references"></a>Riferimenti

Questo caso d'uso è stato sviluppato in precedenza su più piattaforme:

* [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Guida alla modellazione per la manutenzione predittiva con SQL R Services)
* [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)

# <a name="next-steps"></a>Passaggi successivi

Sono disponibili molti altri scenari di esempio all'interno del workbench di Azure Machine Learning che dimostrano le funzionalità aggiuntive del prodotto. 