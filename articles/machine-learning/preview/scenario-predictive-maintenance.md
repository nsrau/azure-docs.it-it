---
title: Manutenzione predittiva per scenari reali | Microsoft Docs
description: Manutenzione predittiva per scenari reali mediante PySpark
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
ms.openlocfilehash: 81e227194ff64d7b7af842a208349ccc63528ab8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Manutenzione predittiva per scenari reali

L'impatto del tempo di inattività delle apparecchiature non pianificato può danneggiare un'azienda. È fondamentale mantenere in funzione le attrezzature sul campo per massimizzare l'uso e le prestazioni, riducendo il costoso tempo di inattività non pianificato. L'identificazione preventiva dei problemi può aiutare ad allocare una quantità limitata di risorse di manutenzione in modo più conveniente e migliorare la qualità e i processi della supply chain. 

Questo scenario esplora un [set di dati simulato relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) per illustrare un progetto di data science per la manutenzione predittiva che comprende inserimento dati, progettazione delle funzionalità, creazione di un modello e operazionalizzazione e distribuzione del modello. Il codice per l'intero processo è scritto nel notebook di Jupyter usando PySpark all'interno di Azure Machine Learning Workbench. Il modello finale viene distribuito usando Gestione modelli di Azure Machine Learning per eseguire stime in tempo reale dei guasti delle apparecchiature.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Repository GitHub di Cortana Intelligence Gallery

L'esercitazione su Cortana Intelligence Gallery per la manutenzione predittiva è un repository GitHub pubblico ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) in cui è possibile contribuire e segnalare problemi.


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Uno dei principali problemi che le industrie che fanno largo uso di asset devono affrontare sono i costi significativi associati a ritardi dovuti a problemi meccanici. La maggior parte delle aziende è interessata a prevedere il verificarsi di tali problemi, in modo da prevenirli proattivamente prima che si verifichino. L'obiettivo è ridurre i costi riducendo i tempi di inattività e, possibilmente, incrementare la sicurezza. 

Questo scenario si ispira al [playbook per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) per illustrare la compilazione di un modello predittivo per un set di dati simulato. I dati di esempio sono derivati da elementi comuni osservati in molti casi d'uso di manutenzione predittiva.

Il problema aziendale per questi dati simulati consiste nel prevedere i problemi causati dai guasti dei componenti. La domanda che l'azienda si pone è "*Qual è la probabilità che una macchina si arresti a causa di un guasto di un componente?*" Questo problema viene formattato come problema di classificazione multiclasse (più componenti per macchina). Per creare il modello predittivo viene usato un algoritmo di Machine Learning. Il training del modello viene eseguito usando i dati cronologici raccolti dalle macchine. In questo scenario l'utente esegue diversi passaggi per implementare il modello nell'ambiente di Azure Machine Learning Workbench.

## <a name="prerequisites"></a>prerequisiti

* Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia installata di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Per installare il programma e creare un'area di lavoro, vedere la [guida introduttiva all'installazione](./quickstart-installation.md).
* L'operazionalizzazione di Azure Machine Learning richiede un ambiente di distribuzione locale e un [account di Gestione modelli di Azure Machine Learning](model-management-overview.md).

Questo esempio funziona in qualsiasi contesto di calcolo di Machine Learning Workbench. Tuttavia, è consigliabile eseguire l'esempio con almeno 16 GB di memoria. Questo scenario è stato compilato e testato in un computer Windows 10 che esegue uno standard DS4_V2 remoto [Macchina virtuale di data science per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

L'operazionalizzazione del modello è stata eseguita usando la versione 0.1.0a22 dell'interfaccia della riga di comando di Microsoft Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Machine Learning Workbench.
2.  Nella pagina **Projects** (Progetti) selezionare **+** e quindi selezionare **New Project** (Nuovo progetto).
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto.
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Predictive Maintenance" (Manutenzione predittiva) e selezionare il modello **Predictive Maintenance** (Manutenzione predittiva).
5.  Selezionare **Create**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparare la destinazione di calcolo del server del notebook

Per l'esecuzione nel computer locale, dal menu **File** di Machine Learning Workbench scegliere **Open Command Prompt** (Apri prompt dei comandi) o **Open PowerShell CLI** (Apri interfaccia della riga di comando di PowerShell). L'interfaccia della riga di comando consente di accedere ai servizi di Azure usando i comandi `az`. Per prima cosa, accedere al proprio account di Azure con il comando:

```
az login
``` 

Questo comando fornisce una chiave di autenticazione da usare con l'URL https:\\aka.ms\devicelogin. L'interfaccia della riga di comando attende fino a quando viene restituita l'operazione di accesso al dispositivo e vengono fornite alcune informazioni di connessione. Quindi, se si dispone di un'installazione [Docker](https://www.docker.com/get-docker) locale, preparare l'ambiente di calcolo locale con il comando seguente:

```
az ml experiment prepare --target docker --run-configuration docker
```

È preferibile procedere all'esecuzione in una [macchina virtuale di data science per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) per i requisiti di memoria e disco. Dopo avere configurato la macchina virtuale di data science, preparare l'ambiente Docker remoto con i due comandi seguenti:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una volta eseguita la connessione al contenitore Docker remoto, preparare l'ambiente di calcolo Docker per la macchina virtuale di data science usando il comando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Con l'ambiente di calcolo Docker preparato, aprire il server del notebook di Jupyter all'interno della scheda **Notebooks** (Notebook) di Azure Machine Learning Workbench o avviare un server basato su browser usando il comando seguente: 

```
az ml notebook start
```

I notebook di esempio sono archiviati nella directory Code. I notebook sono configurati per essere eseguiti in sequenza, a partire dal primo notebook (Code\1_data_ingestion.ipynb). Quando si apre ciascun notebook, viene chiesto di selezionare il kernel di calcolo. Scegliere il kernel [Project_Name]_Template [Connection_Name] da eseguire nella macchina virtuale di data science configurata in precedenza.

## <a name="data-description"></a>Descrizione dei dati

I [dati simulati](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sono costituiti da cinque file con valori delimitati da virgole (CSV). Usare i collegamenti seguenti per ottenere descrizioni dettagliate sui set di dati.

* [Macchine](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): caratteristiche che differenziano ogni macchina. Ad esempio, età e modello.
* [Errori](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): il log degli errori contiene errori che non causano l'arresto, generati mentre la macchina è ancora in funzione. Questi errori non vengono considerati guasti, anche se potrebbero indicare un evento di guasto futuro. I valori di data e ora degli errori sono arrotondati all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Manutenzione](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): il log di manutenzione contiene i record per la manutenzione pianificata e non pianificata. La manutenzione pianificata corrisponde alla regolare ispezione dei componenti. La manutenzione non pianificata potrebbe essere necessaria in seguito a guasti meccanici o a una riduzione delle prestazioni. I valori di data e ora per la manutenzione sono arrotondati all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): i dati di telemetria sono costituiti dalle misure delle serie temporali provenienti da più sensori all'interno di ogni macchina. I dati sono registrati calcolando la media dei valori dei sensori in ogni intervallo di un'ora.
* [Guasti](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): i guasti corrispondono alle sostituzioni dei componenti nel log di manutenzione. Ogni record contiene l'ID della macchina, il tipo di componente e la data e l'ora della sostituzione. Questi record vengono usati per creare le etichette di apprendimento automatico che il modello sta cercando di stimare.

Per scaricare i set di dati non elaborati dal repository GitHub e creare i set di dati PySpark per l'analisi, vedere lo scenario di [inserimento di dati](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) nei notebook Jupyter nella cartella Code.

## <a name="scenario-structure"></a>Struttura dello scenario
Il contenuto per lo scenario è disponibile nel [repository GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Il file [Leggimi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) illustra il flusso di lavoro, dalla preparazione dei dati alla creazione di un modello e quindi alla distribuzione di una soluzione per la produzione. Ogni passaggio del flusso di lavoro è incapsulato in un notebook di Jupyter nella cartella [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) all'interno del repository.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): questo notebook scarica i cinque file di input con estensione csv ed esegue alcune pulizie preliminari e visualizzazioni dei dati. Il notebook converte ogni set di dati nel formato PySpark e li archivia in un contenitore BLOB di Azure per l'uso nel notebook di progettazione della funzionalità.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): le funzionalità del modello vengono costruite dal set di dati non elaborati dall'archivio BLOB di Azure mediante un approccio di serie temporali standard per la telemetria, gli errori e i dati di manutenzione. Le sostituzioni dei componenti correlate al guasto vengono usate per costruire le etichette del modello che descrivono quale componente non ha funzionato. I dati della funzionalità dell'etichetta vengono salvati in un BLOB di Azure per il notebook di creazione del modello.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): il notebook di compilazione del modello usa il set di dati della funzionalità con etichette e suddivide i dati in set di dati di training e di sviluppo in base a un indicatore di data e ora. Il notebook è configurato come esperimento con i modelli pyspark.ml.classification. I dati di training sono vettorizzati. L'utente può sperimentare con un **DecisionTreeClassifier** o un **RandomForestClassifier** manipolando gli iperparametri per individuare il modello con le prestazioni migliori. Le prestazioni sono determinate valutando le statistiche di misura sul set di dati di sviluppo. Queste statistiche vengono registrate nella schermata della fase di esecuzione di Machine Learning Workbench per il rilevamento. A ogni esecuzione, il notebook salva il modello risultante nel disco locale che esegue il kernel del notebook di Jupyter. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): questo notebook usa l'ultimo modello salvato nel disco locale (kernel del notebook di Jupyter) per compilare i componenti per la distribuzione del modello in un servizio Web di Azure. Gli asset operativi vengono compattati nel file o16n.zip archiviato in un altro contenitore BLOB di Azure. Il file compresso contiene:

* **service_schema.json**: file di definizione dello schema per la distribuzione. 
* **pdmscore.py**: funzioni **init()** e **run()** richieste dal servizio Web di Azure.
* **pdmrfull.model**: directory di definizione del modello.
    
Il notebook verifica le funzioni con la definizione del modello prima di creare il pacchetto degli asset di operazionalizzazione per la distribuzione. Le istruzioni per la distribuzione sono incluse alla fine del blocco appunti.

## <a name="conclusion"></a>Conclusioni

Questo scenario fornisce una panoramica di come creare una soluzione end-to-end di manutenzione predittiva usando PySpark nell'ambiente notebook Jupyter in Azure Machine Learning Workbench. Questo scenario di esempio illustra inoltre in modo dettagliato la distribuzione del modello tramite l'ambiente di Gestione modelli di Azure Machine Learning per stime in tempo reale dei guasti delle apparecchiature.

## <a name="references"></a>Riferimenti

I riferimenti seguenti offrono altri esempi di casi d'uso di manutenzione predittiva per varie piattaforme:

* [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Guida alla modellazione per la manutenzione predittiva con SQL R Services)
* [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)
* [Apprendimento avanzato per la manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altri scenari di esempio all'interno di Azure Machine Learning Workbench che dimostrano le funzionalità aggiuntive del prodotto. 
