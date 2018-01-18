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
ms.openlocfilehash: 0299e73aecca3b3e5714b37c8b0b776ec8561e29
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="predictive-maintenance-real-world-scenario"></a>Scenario reale di manutenzione predittiva.

L'impatto del tempo di inattività delle apparecchiature non pianificato può danneggiare un'azienda. È quindi fondamentale mantenere in funzione le apparecchiature sul campo per ottimizzare l'uso e le prestazioni, riducendo al minimo i costosi tempi di inattività non pianificati. L'identificazione preventiva dei problemi può aiutare ad allocare una quantità limitata di risorse di manutenzione in modo più conveniente e migliorare la qualità e i processi della supply chain. 

Questo scenario esplora un [set di dati simulato relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) per illustrare un progetto di data science per la manutenzione predittiva che comprende inserimento dati, progettazione delle funzionalità, creazione di un modello e operazionalizzazione e distribuzione del modello. Il codice per l'intero processo è scritto in notebook Jupyter usando PySpark all'interno di Azure Machine Learning Workbench. Il modello finale viene distribuito con Gestione modelli di Azure Machine Learning per eseguire stime dei guasti in tempo reale delle apparecchiature.   

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte

Il collegamento al repository GitHub pubblico è il seguente: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Uno dei principali problemi che le industrie che fanno largo uso di asset devono affrontare sono i costi significativi associati a ritardi dovuti a problemi meccanici. La maggior parte delle aziende è interessata a prevedere il verificarsi di tali problemi, in modo da prevenirli proattivamente prima che si verifichino. L'obiettivo è ridurre i costi riducendo i tempi di inattività e, possibilmente, incrementare la sicurezza. 

Questo scenario si ispira al [playbook per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) per illustrare la creazione di un modello predittivo per un set di dati simulato. I dati di esempio sono derivati da elementi comuni osservati in molti casi d'uso di manutenzione predittiva.

Il problema aziendale per questi dati simulati consiste nel prevedere i problemi causati dai guasti dei componenti. La domanda che l'azienda si pone è quindi "*Qual è la probabilità che una macchina si arresti a causa di un guasto di un componente*?" Questo problema viene formattato come problema di classificazione multiclasse (più componenti per macchina) e viene usato un algoritmo di apprendimento automatico per creare il modello predittivo. Viene eseguito il training del modello sui dati cronologici raccolti dalle macchine. In questo scenario l'utente esegue i diversi passaggi di implementazione di tale modello nell'ambiente di Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/en-us/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
* L'operazionalizzazione di Azure Machine Learning richiede un ambiente di distribuzione locale e un [account di gestione del modello](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

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

Per l'esecuzione nel computer locale, dal menu `File` del Workbench AML, selezionare `Open Command Prompt` o `Open PowerShell CLI`. L'interfaccia della riga di comando consente di accedere ai servizi di Azure usando i comandi `az`. Per prima cosa, accedere al proprio account di Azure con il comando:

```
az login
``` 

Questo comando fornisce una chiave di autenticazione da usare con l'URL `https:\\aka.ms\devicelogin`. L'interfaccia della riga di comando attende fino a quando viene restituita l'operazione di accesso al dispositivo e vengono fornite alcune informazioni di connessione. Quindi, se si dispone di un'installazione [docker](https://www.docker.com/get-docker) locale, preparare l'ambiente di calcolo locale con i comandi seguenti:

```
az ml experiment prepare --target docker --run-configuration docker
```

È preferibile procedere all'esecuzione in una [macchina virtuale di data science per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) per i requisiti di memoria e disco. Dopo avere configurato la macchina virtuale di data science, preparare l'ambiente docker remoto con i due comandi seguenti:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una volta eseguita la connessione al contenitore docker remoto, preparare l'ambiente di calcolo docker per la macchina virtuale di data science usando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Con l'ambiente di calcolo docker preparato, aprire il server del notebook di Jupyter all'interno della scheda Notebooks (Notebook) di Azure Machine Learning Workbench o avviare un server basato su browser con: 
```
az ml notebook start
```

I notebook di esempio sono archiviati nella directory `Code`. I notebook sono configurati per essere eseguiti in sequenza, a partire dal primo notebook (`Code\1_data_ingestion.ipynb`). Quando si apre ciascun notebook, viene chiesto di selezionare il kernel di calcolo. Scegliere il kernel `[Project_Name]_Template [Connection_Name]` da eseguire nella macchina virtuale di data science configurata in precedenza.

## <a name="data-description"></a>Descrizione dei dati

I [dati simulati](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sono costituiti da cinque file con valori delimitati da virgole (CSV). Seguire i collegamenti per ottenere una descrizione ancora più dettagliata dei set di dati.

* [Macchine](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): caratteristiche che differenziano ogni macchina. Ad esempio, età e modello.
* [Errore](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): il log degli errori contiene errori che non causano l'arresto, generati mentre la macchina è ancora in funzione. Questi errori non vengono considerati guasti, anche se potrebbero indicare un evento di guasto futuro. Data e ora dell'errore sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Manutenzione](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): il log di manutenzione contiene i record per la manutenzione pianificata e non pianificata. La manutenzione pianificata corrisponde alla regolare ispezione dei componenti, la manutenzione non pianificata potrebbe essere necessaria in seguito a guasti meccanici o a una riduzione delle prestazioni. Data e ora della manutenzione sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): i dati di telemetria sono costituiti dalle misurazioni delle serie temporali provenienti da più sensori all'interno di ogni macchina. I dati sono registrati calcolando la media dei valori dei sensori in ogni intervallo di un'ora.
* [Guasti](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): i guasti corrispondono alle sostituzioni dei componenti nel log di manutenzione. Ogni record contiene l'ID della macchina, il tipo di componente e la data e l'ora della sostituzione. Questi record vengono usati per creare le etichette di apprendimento automatico che il modello sta cercando di stimare.

Vedere lo scenario di [inserimento di dati](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) nei notebook Jupyter nella sezione relativa al codice per scaricare i set di dati non elaborati dal repository GitHub e creare i set di dati PySpark per l'analisi.

## <a name="scenario-structure"></a>Struttura dello scenario
Il contenuto per lo scenario è disponibile nel [repository GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Il file [Leggimi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) illustra il flusso di lavoro, dalla preparazione dei dati alla creazione di un modello e quindi alla distribuzione di una soluzione per la produzione. Ogni passaggio del flusso di lavoro è incapsulato in un notebook di Jupyter nella cartella [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) all'interno del repository.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Questo notebook scarica i cinque file di input con estensione csv, esegue alcune pulizie preliminari e visualizzazioni dei dati. Il notebook converte ogni set di dati nel formato PySpark e li archivia in un contenitore BLOB di Azure per l'uso nel notebook di progettazione della funzionalità.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): usando i set di dati non elaborati del BLOB di Azure, le funzionalità del modello vengono costruite con un approccio di serie temporali standard per la telemetria, gli errori e i dati di manutenzione. Le sostituzioni dei componenti correlati al guasto vengono usate per costruire le etichette del modello che descrivono quale componente non ha funzionato. I dati della funzionalità dell'etichetta vengono salvati in un BLOB di Azure per il notebook di creazione del modello.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Usando il set di dati della funzionalità con etichette, il notebook di modellazione suddivide i dati in set di dati di training e di sviluppo in data-timestamp. Il notebook è l'esperimento del set di installazione con i modelli `pyspark.ml.classification`. I dati di training sono vettorizzati e l'utente può sperimentare con un `DecisionTreeClassifier` o un `RandomForestClassifier`, manipolando gli iperparametri per individuare il modello migliore per le prestazioni. Le prestazioni sono determinate valutando le statistiche di misura sul set di dati di sviluppo. Queste statistiche vengono registrate nella schermata della fase di esecuzione di Workbench AML per il rilevamento. A ogni esecuzione, il notebook salva il modello risultante nel disco locale eseguendo il kernel Jupyter notebook. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): usando l'ultimo modello salvato in un disco locale (kernel del notebook di Jupyter), questo notebook compila i componenti per la distribuzione del modello in un servizio Web di Azure. Gli asset operativi vengono compattati nel file `o16n.zip` archiviato in un altro contenitore BLOB di Azure. Il file compresso contiene:

* `service_schema.json` Il file di definizione dello schema per la distribuzione. 
* `pdmscore.py` Le funzioni init() e run() necessarie per il servizio web di Azure
* `pdmrfull.model` La directory di definizione del modello.
    
 Il notebook verifica le funzioni con la definizione del modello prima di creare il pacchetto degli asset di operazionalizzazione per la distribuzione. Le istruzioni per la distribuzione sono incluse alla fine del blocco appunti.

## <a name="conclusion"></a>Conclusioni

Questo scenario fornisce al lettore una panoramica di come creare una soluzione end-to-end di manutenzione predittiva usando PySpark nell'ambiente notebook Jupyter in Azure ML Workbench. Questo scenario di esempio illustra inoltre in modo dettagliato la distribuzione del modello tramite l'ambiente di Gestione modelli di Azure Machine Learning per stime in tempo reale dei guasti delle apparecchiature.

## <a name="references"></a>Riferimenti

Questo caso d'uso è stato sviluppato in precedenza su più piattaforme:

* [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Guida alla modellazione per la manutenzione predittiva con SQL R Services)
* [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili molti altri scenari di esempio all'interno del workbench di Azure Machine Learning che dimostrano le funzionalità aggiuntive del prodotto. 