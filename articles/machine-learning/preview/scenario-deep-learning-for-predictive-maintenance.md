---
title: Apprendimento avanzato per scenari reali di manutenzione predittiva - Azure| Microsoft Docs
description: "Si apprenderà come replicare l'esercitazione sull'apprendimento avanzato per la manutenzione predittiva con Azure Machine Learning Workbench."
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Apprendimento avanzato per scenari reali di manutenzione predittiva

L'apprendimento avanzato costituisce una delle tendenze più diffuse nell'ambito dell'apprendimento automatico. Viene usato in diversi campi e in svariate applicazioni, tra cui automobili senza pilota, riconoscimento vocale e delle immagini, robotica e finanza. L'apprendimento avanzato è costituito da un set di algoritmi ispirato alla forma del cervello (reti neurali biologiche) e all'apprendimento automatico. In genere, gli scienziati cognitivi fanno riferimento all'apprendimento avanzato come a reti neurali artificiali (ANN, Artificial Neural Network).

Anche la manutenzione predittiva è ampiamente diffusa. Nell'ambito della manutenzione predittiva vengono usate molte tecniche diverse per consentire di determinare le condizioni delle apparecchiature e per prevedere quando deve essere eseguita la manutenzione. Alcuni usi comuni della manutenzione predittiva comprendono la stima dei guasti, la diagnosi degli errori (analisi della causa radice), il rilevamento degli errori, la classificazione dei tipi di errori e la raccomandazione di azioni di mitigazione o manutenzione dopo un guasto.

Negli scenari di manutenzione predittiva i dati vengono raccolti nel corso del tempo per monitorare lo stato delle apparecchiature. L'obiettivo è quello di individuare modelli in grado di stimare e, in ultima analisi, evitare che si verifichino errori. L'uso di reti [Long Short Term Memory (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) è un metodo di apprendimento avanzato particolarmente interessante nell'ambito della manutenzione predittiva. Le reti LSTM sono efficaci nell'apprendimento da sequenze. I dati delle serie temporali possono essere usati per analizzare periodi di tempo più lunghi in modo da rilevare modelli di errore.

In questa esercitazione viene creata una rete LSTM per il set di dati e lo scenario descritti nel modello di [manutenzione predittiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). La rete verrà usata per stimare la vita utile rimanente dei motori di un aereo. Il modello usa valori simulati dei sensori dell'aereo per prevedere quando si verificherà un guasto del motore. Sulla base di questa stima la manutenzione può essere pianificata in anticipo, in modo da evitare il guasto.

Questa esercitazione usa la libreria di apprendimento avanzato [Keras](https://keras.io/) e il Microsoft Cognitive Toolkit [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) come back-end.

Il repository GitHub pubblico con gli esempi per questa esercitazione è disponibile all'indirizzo [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Panoramica del caso d'uso

Questa esercitazione usa l'esempio di eventi "run-to-failure" simulati dei motori di un aereo per illustrare il processo di modellazione per la manutenzione predittiva. 

Il presupposto implicito dei dati di modellazione descritti in questo articolo è che la risorsa presenta un modello di riduzione delle prestazioni in progressione. Il modello si riflette nelle misure dei sensori della risorsa. Esaminando i valori dei sensori della risorsa nel tempo, l'algoritmo di apprendimento automatico è in grado di comprendere la relazione tra i valori dei sensori, i cambiamenti che subentrano in tali valori e la cronologia dei guasti. Questa relazione viene usata per prevedere gli errori che potrebbero verificarsi in futuro. 

È consigliabile esaminare il formato dei dati e completare tutti e tre i passaggi del modello prima di sostituire i dati di esempio con quelli personalizzati.

## <a name="prerequisites"></a>Prerequisiti

- Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
- Azure Machine Learning Workbench, con un'area di lavoro creata.
- Per l'operazionalizzazione del modello: operazionalizzazione di Azure Machine Learning con un ambiente di distribuzione locale configurato e un [account di Gestione modelli di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:

1. Aprire Machine Learning Workbench.
2. Nella pagina **Projects** (Progetti) selezionare **+** e quindi selezionare **New Project** (Nuovo progetto).
3. Nel riquadro **Create New Project** (Crea nuovo progetto) immettere le informazioni per il nuovo progetto.
4. Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) immettere **Predictive Maintenance** (Manutenzione predittiva) e quindi selezionare il modello.
5. Selezionare **Create**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparare la destinazione di calcolo del server del notebook

Nel computer locale dal menu **File** di Machine Learning Workbench scegliere **Open Command Prompt** (Apri prompt dei comandi) o **Open PowerShell** (Apri PowerShell). Nella finestra del prompt dei comandi dell'opzione scelta eseguire i comandi seguenti:

`az ml experiment prepare --target docker --run-configuration docker`

È consigliabile eseguire il server del notebook in una [macchina virtuale di data science (DSVM) per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) DS4_V2 standard. Dopo avere configurato la macchina virtuale di data science, eseguire i comandi seguenti per preparare le immagini Docker:

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Con le immagini Docker preparate, aprire il server del notebook di Jupyter. Per aprire il server del notebook di Jupyter, passare alla scheda **Notebooks** (Notebook) di Machine Learning Workbench o avviare un server basato su browser: `az ml notebook start`

I notebook vengono archiviati nella directory Code nell'ambiente Jupyter. Eseguire questi notebook in sequenza in base alla numerazione, partendo da Code\1_data_ingestion_and_preparation.ipynb.

Selezionare il kernel corrispondente a [project_name]_Template [connection_name] e quindi selezionare **Set Kernel** (Imposta kernel).

## <a name="data-description"></a>Descrizione dei dati

Il modello usa tre set di dati come input, nei file PM_train.txt, PM_test.txt e PM_truth.txt.

-  **Dati di training**: dati "run-to-failure" dei motori dell'aereo. I dati di training (PM_train.txt) sono costituiti da più serie temporali multivariate, con il *ciclo* come unità di tempo. Includono 21 letture dei sensori per ogni ciclo. 

    Ogni serie temporale può essere considerata generata da un motore diverso dello stesso tipo. Si presuppone che ogni motore parta con gradi diversi di usura iniziale e variazione di produzione. Queste informazioni non sono note all'utente. 

    In questi dati simulati si presuppone che il motore funzioni normalmente all'inizio di ogni serie temporale e che parta per ridurre le prestazioni in un determinato punto durante la serie di cicli operativi. La riduzione delle prestazioni avanza e aumenta in termini di grandezza. 

    Quando viene raggiunta una soglia predefinita, il motore è considerato non sicuro per un ulteriore funzionamento. L'ultimo ciclo in ogni serie temporale può essere considerato il punto di guasto del motore corrispondente.

-   **Dati di test**: dati di funzionamento dei motori dell'aereo, senza eventi di errore registrati. I dati di test (PM_test.txt) hanno lo stesso schema di dati di quelli di training. L'unica differenza è che i dati non indicano quando si verifica l'errore (l'ultimo periodo di tempo *non* rappresenta il punto di guasto). Non è possibile sapere quanti altri cicli può durare il motore prima che si verifichi il guasto.

- **Dati reali**: informazioni sugli effettivi cicli restanti per ogni motore nei dati di test. I dati reali forniscono il numero dei cicli operativi rimanenti per i motori nei dati di test.

## <a name="scenario-structure"></a>Struttura dello scenario

Il contenuto per lo scenario è disponibile nel [repository GitHub] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

Nel repository un file [Leggimi] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) illustra i processi, dalla preparazione dei dati alla creazione e all'operazionalizzazione del modello. I tre notebook di Jupyter sono disponibili nella cartella [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) nel repository. 

Di seguito viene descritto il flusso di lavoro dettagliato dello scenario.

### <a name="task-1-data-ingestion-and-preparation"></a>Attività 1. Inserimento e preparazione dei dati

Il notebook di Jupyter per l'inserimento dati in Code/1_data_ingestion_and_preparation.ipnyb carica i tre set di dati di input nel formato del frame di dati Pandas. Quindi prepara i dati per la modellazione ed esegue alcune visualizzazioni dei dati preliminari. I dati vengono infine trasformati nel formato PySpark e archiviati in un contenitore di archiviazione BLOB di Azure nella sottoscrizione di Azure per essere usati nell'attività di modellazione successiva.

### <a name="task-2-model-building-and-evaluation"></a>Attività 2. Creazione e valutazione del modello

Il notebook di Jupyter per la creazione del modello in Code/2_model_building_and_evaluation.ipnyb legge i set di dati di training e di test PySpark dall'archivio BLOB. Quindi una rete LSTM viene compilata con i training set. Le prestazioni del modello vengono misurate nel set di test. Il modello risultante è serializzato e archiviato nel contesto di calcolo locale per essere usato nell'attività di operazionalizzazione.

### <a name="task-3-operationalization"></a>Attività 3. Operazionalizzazione

Il notebook di Jupyter per l'operazionalizzazione in Code/3_operationalization.ipnyb usa il modello archiviato per compilare le funzioni e lo schema richiesti per chiamare il modello in un servizio Web ospitato in Azure. Il notebook testa le funzioni e quindi comprime le risorse di operazionalizzazione in un file ZIP.

Il file compresso contiene i file seguenti:

- **modellstm.json**: file di definizione dello schema per la distribuzione. 
- **lstmscore.py**: funzioni **init()** e **run()** richieste dal servizio Web di Azure.
- **lstm.model**: directory di definizione del modello.

Il notebook verifica le funzioni usando la definizione del modello prima di creare il pacchetto delle risorse di operazionalizzazione per la distribuzione. Le istruzioni per la distribuzione sono incluse alla fine del blocco appunti.


## <a name="conclusion"></a>Conclusioni

Questa esercitazione si basa su uno scenario semplice in cui viene usata solo un'origine dati (valori dei sensori) per le stime. Negli scenari di manutenzione predittiva più avanzati, ad esempio [Predictive Maintenance Modeling Guide R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) (Notebook R di guida alla modellazione per la manutenzione predittiva), potrebbero essere usate molte origini dati. Altre origini dati possono includere record di manutenzione cronologici, log degli errori e funzioni relative a macchine e operatore. Ulteriori origini dati potrebbero richiedere diversi tipi di trattamento per l'uso nell'ambito delle reti di apprendimento avanzato. È anche importante ottimizzare i modelli sulla base dei parametri corretti, ad esempio le dimensioni della finestra. 

È possibile modificare le parti pertinenti di questo scenario e provare diversi scenari dei problemi, come quelli descritti in [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva), che interessa diverse altre origini dati.


## <a name="references"></a>Riferimenti

- [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
- [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
- [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)

