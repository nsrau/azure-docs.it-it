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
ms.openlocfilehash: d8e34924cb29e2e6469d009e40b04d5cee8930a6
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>Scenario reale di manutenzione predittiva.

L'impatto del tempo di inattività delle apparecchiature non pianificato può danneggiare un'azienda. È quindi fondamentale mantenere in funzione le apparecchiature sul campo per ottimizzare l'uso e le prestazioni, riducendo al minimo i costosi tempi di inattività non pianificati. L'identificazione preventiva dei problemi può aiutare ad allocare una quantità limitata di risorse di manutenzione in modo più conveniente e migliorare la qualità e i processi della supply chain. 

Questo scenario vengono illustrati un relativamente [set di dati su larga scala simulato](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) per analizzare un progetto di analisi scientifica dei dati di manutenzione predittiva dall'inserimento di dati, funzionalità di progettazione, la compilazione del modello e rendere operativo il modello e distribuzione. Il codice per l'intero processo viene scritto in notebook Jupyter utilizzando PySpark all'interno di Azure ML Workbench. Il modello finale viene distribuito utilizzando Gestione modelli di Azure Machine Learning per fare delle stime di un errore hardware in tempo reale.   

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte

Il collegamento al repository GitHub pubblico è il seguente: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Uno dei principali problemi che le industrie che fanno largo uso di asset devono affrontare sono i costi significativi associati a ritardi dovuti a problemi meccanici. La maggior parte delle aziende è interessata a prevedere il verificarsi di tali problemi, in modo da prevenirli proattivamente prima che si verifichino. L'obiettivo è ridurre i costi riducendo i tempi di inattività e, possibilmente, incrementare la sicurezza. 

Questo scenario richiede idee dal [playbook manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) per illustrare la creazione di un modello di stima per un set di dati simulato. I dati di esempio sono derivati da elementi comuni osservati in molti casi di utilizzo di manutenzione predittiva.

Il problema aziendale per questi dati simulati consiste nel prevedere i problemi causati dai guasti dei componenti. La domanda che l'azienda si pone è quindi "*Qual è la probabilità che una macchina si arresti a causa di un guasto di un componente*?" Questo problema viene formattato come problema di classificazione multiclasse (più componenti per macchina) e viene usato un algoritmo di apprendimento automatico per creare il modello predittivo. Viene eseguito il training del modello sui dati cronologici raccolti dalle macchine. In questo scenario l'utente esegue i diversi passaggi di implementazione di tale modello nell'ambiente di Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/en-us/free/) (sono disponibili versioni di valutazione gratuite).
* Installata una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguente il [Guida all'installazione di avvio rapido](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
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

Per l'esecuzione nel computer locale, dal menu `File` del Workbench AML, selezionare `Open Command Prompt` o `Open PowerShell CLI`. L'interfaccia CLI consente di accedere ai servizi di Azure utilizzando il `az` comandi. Prima di tutto, accedere al proprio account Azure con il comando:

```
az login
``` 

Questo comando fornisce una chiave di autenticazione da utilizzare con il `https:\\aka.ms\devicelogin` URL. L'interfaccia CLI attende fino a quando l'operazione di accesso dispositivo restituisce e fornisce alcune informazioni di connessione. Quindi, se si dispone di una variabile locale [docker](https://www.docker.com/get-docker) installare, preparare l'ambiente di calcolo locale con i comandi seguenti:

```
az ml experiment prepare --target docker --run-configuration docker
```

È preferibile eseguire su un [macchina virtuale di analisi scientifica dei dati per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) per i requisiti di memoria e disco. Dopo aver configurato il DSVM, preparare l'ambiente di docker remoto con i due comandi seguenti:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una volta connessi al contenitore docker remota, preparare il DSVM docker calcolo ambiente utilizzando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Calcolo ambiente preparato con docker, aprire il server Jupyter notebook all'interno della scheda notebook AML Workbench o avviare un server basato su browser con: 
```
az ml notebook start
```

I blocchi appunti di esempio vengono archiviati nel `Code` directory. I blocchi appunti vengono impostati per l'esecuzione sequenziale, partire dal primo (`Code\1_data_ingestion.ipynb`) notebook. Quando si apre ogni notebook, viene chiesto di selezionare il kernel di calcolo. Scegliere il `[Project_Name]_Template [Connection_Name]` kernel da eseguire sul DSVM preconfigurati.

## <a name="data-description"></a>Descrizione dei dati

I [dati simulati](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sono costituiti da cinque file con valori delimitati da virgole (CSV). Seguire i collegamenti per ottenere una descrizione ancora più dettagliata dei set di dati.

* [Macchine](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): caratteristiche che differenziano ogni macchina. Ad esempio, età e modello.
* [Errore](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): il log degli errori contiene errori che non causano l'arresto, generati mentre la macchina è ancora in funzione. Questi errori non vengono considerati guasti, anche se potrebbero indicare un evento di guasto futuro. Data e ora dell'errore sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Manutenzione](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): il log di manutenzione contiene i record per la manutenzione pianificata e non pianificata. La manutenzione pianificata corrisponde alla regolare ispezione dei componenti, la manutenzione non pianificata potrebbe essere necessaria in seguito a guasti meccanici o a una riduzione delle prestazioni. Data e ora della manutenzione sono arrotondate all'ora più vicina perché i dati di telemetria vengono raccolti con una frequenza oraria.
* [Dati di telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): dati di telemetria costituito da dati di serie temporale misure dal più sensori all'interno di ogni computer. I dati registrati, calcolando la media dei valori dei sensori in ogni intervallo di un'ora.
* [Guasti](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): i guasti corrispondono alle sostituzioni dei componenti nel log di manutenzione. Ogni record contiene l'ID della macchina, il tipo di componente e la data e l'ora della sostituzione. Questi record vengono usati per creare le etichette di apprendimento automatico che il modello sta cercando di stimare.

Vedere lo scenario di [inserimento di dati](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) nei notebook Jupyter nella sezione relativa al codice per scaricare i set di dati non elaborati dal repository GitHub e creare i set di dati PySpark per l'analisi.

## <a name="scenario-structure"></a>Struttura dello scenario
Il contenuto per lo scenario è disponibile nel [repository GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Il [Leggimi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) file descrive il flusso di lavoro di preparazione dei dati, la creazione di un modello e quindi distribuire una soluzione per la produzione. Ogni passaggio del flusso di lavoro è incapsulato in un server Jupyter notebook nel [codice](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) cartella all'interno del repository.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Questo notebook scarica i cinque file di input con estensione csv, esegue alcune pulizie preliminari e visualizzazioni dei dati. Il blocco note converte ogni set di dati in formato PySpark e viene archiviato in un contenitore blob di Azure per l'utilizzo del blocco per Appunti Progettazione funzionalità.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Utilizzando il set di dati non elaborati dal blob di Azure, le funzionalità vengono costruite utilizzando ora solare approccio di serie per i dati di telemetria, errori e la manutenzione di modello. Le sostituzioni relative a errore componente vengono utilizzate per costruire le etichette di modello che descrive il componente non è riuscita. I dati della funzionalità di etichetta viene salvati in un blob di Azure per il modello di compilazione notebook.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Usando il set di dati della funzionalità con etichette, il notebook di modellazione suddivide i dati in set di dati di training e di sviluppo in data-timestamp. Il notebook è l'esperimento del set di installazione con i modelli `pyspark.ml.classification`. I dati di training sono vettorizzati e l'utente può sperimentare con un `DecisionTreeClassifier` o un `RandomForestClassifier`, manipolando gli iperparametri per individuare il modello migliore per le prestazioni. Le prestazioni sono determinate valutando le statistiche di misura sul set di dati di sviluppo. Queste statistiche vengono registrate nella schermata della fase di esecuzione di Workbench AML per il rilevamento. A ogni esecuzione, il notebook salva il modello risultante nel disco locale eseguendo il kernel Jupyter notebook. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Utilizzando l'ultimo modello salvato in un disco locale (kernel notebook Jupyter), il blocco compila i componenti per la distribuzione del modello in un servizio web di Azure. Gli asset operativi vengono compattati nel file `o16n.zip` archiviato in un altro contenitore BLOB di Azure. Il file compresso contiene:

* `service_schema.json` Il file di definizione dello schema per la distribuzione. 
* `pdmscore.py` Le funzioni init() e run() necessarie per il servizio web di Azure
* `pdmrfull.model` La directory di definizione del modello.
    
 Il notebook verifica le funzioni con la definizione del modello prima di creare il pacchetto degli asset di operazionalizzazione per la distribuzione. Le istruzioni per la distribuzione sono incluse alla fine del blocco appunti.

## <a name="conclusion"></a>Conclusioni

Questo scenario fornisce al lettore una panoramica di come creare una soluzione end-to-end di manutenzione predittiva usando PySpark nell'ambiente notebook Jupyter in Azure ML Workbench. Questo scenario di esempio vengono inoltre descritte la distribuzione di modelli tramite l'ambiente di gestione di modelli di Azure Machine Learning stime in tempo reale apparecchiature errore.

## <a name="references"></a>Riferimenti

Questo caso d'uso è stato sviluppato in precedenza su più piattaforme:

* [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Guida alla modellazione per la manutenzione predittiva con SQL R Services)
* [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili molti altri scenari di esempio all'interno del workbench di Azure Machine Learning che dimostrano le funzionalità aggiuntive del prodotto. 