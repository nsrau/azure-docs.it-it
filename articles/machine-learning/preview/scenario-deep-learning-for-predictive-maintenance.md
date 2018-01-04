---
title: Formazione per gli scenari reali manutenzione predittiva - Azure | Documenti Microsoft
description: Imparare a replicare l'esercitazione sull'apprendimento completa per la manutenzione predittiva con Azure Machine Learning Workbench.
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
ms.openlocfilehash: 6019437763f82fa14b8677b6c7ec7f0c6938fda7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Formazione per gli scenari reali manutenzione predittiva

Formazione è una delle tendenze più diffusi di apprendimento. Formazione viene usata in diversi campi e applicazioni, inclusi meccanismi automobili, riconoscimento vocale e di immagine, robotica e finance. Formazione è un set di algoritmi che è ispirato dalla forma del cervello (reti neurali biologiche) e l'apprendimento automatico. Gli esperti cognitivi fare riferimento in genere a deep learning come reti neurali artificiali (ANNs).

Manutenzione predittiva viene inoltre utilizzata. In modalità manutenzione predittiva, diverse tecniche sono progettate per consentire di determinare la condizione di attrezzatura e prevedere la manutenzione deve essere eseguita. Ecco alcuni usi comuni di manutenzione predittiva sono stima di un errore, la diagnosi di errori (analisi della causa principale), il rilevamento degli errori, classificazione di tipo errore e indicazione di attenuazione o manutenzione azioni dopo un errore.

In scenari di manutenzione predittiva, i dati vengono raccolti nel corso del tempo per monitorare lo stato dell'hardware. L'obiettivo consiste nell'individuare modelli che consentono di stimare e alla fine di evitare errori. Utilizzando [molto breve termine memoria (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) reti è un metodo che è particolarmente interessante nel manutenzione predittiva di apprendimento di profondità. Reti LSTM sono efficaci per l'apprendimento dalle sequenze. Dati della serie temporale è utilizzabile per analizzare di nuovo lunghi periodi di tempo per rilevare modelli di errore.

In questa esercitazione, si compila una rete LSTM per il set di dati e di un scenario in cui sono descritte nel [manutenzione predittiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). È utilizzare la rete per stimare la durata utile rimanente del motori. Il modello utilizza aereo simulato sensore valori per la stima per un motore aereo verrà avere esito negativo in futuro. Utilizzando questa stima, manutenzione può essere pianificata in anticipo, evitare l'errore.

Questa esercitazione viene utilizzato il [Keras](https://keras.io/) deep learning libreria e il Toolkit cognitivi [CNTK](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras) come un back-end.

Il repository GitHub pubblico con gli esempi per questa esercitazione si trova [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Panoramica del caso d'uso

Questa esercitazione viene utilizzato l'esempio di eventi di run-a-errore del motore di aereo simulato per illustrare il processo di modellazione di manutenzione predittiva. 

Il presupposto implicito di modellazione dati descritti di seguito è bene con un modello di riduzione di avanzamento. Il modello si riflette nelle misure sensore dell'asset. Algoritmo di apprendimento automatico informazioni esaminando i valori del sensore dell'asset nel tempo, la relazione tra i valori del sensore, le modifiche ai valori sensore e cronologici errori. Questa relazione viene utilizzata per stimare errori in futuro. 

È consigliabile esaminare il formato dei dati e di completare tutti i tre passaggi del modello prima di sostituire i dati di esempio con la propria data.

## <a name="prerequisites"></a>Prerequisiti

- Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
- Azure Machine Learning area di lavoro, con un'area di lavoro creato.
- Per rendere operativo il modello: rendere operativo Learning macchina Azure, con un ambiente di distribuzione locale configurato e un [account di gestione di Azure Machine Learning modello](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:

1. Aprire Machine Learning Workbench.
2. Nel **progetti** selezionare  **+** , quindi selezionare **nuovo progetto**.
3. Nel **Crea nuovo progetto** riquadro, immettere le informazioni per il nuovo progetto.
4. Nel **modelli di progetto di ricerca** casella di ricerca, immettere **manutenzione predittiva**e quindi selezionare il modello.
5. Selezionare **Create**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparare la destinazione di calcolo del server del notebook

Nel computer locale, in Machine Learning Workbench **File** menu, selezionare l'opzione **prompt dei comandi aperta** o **aprire PowerShell**. Nella finestra prompt dei comandi dell'opzione che si sceglie di eseguire i comandi seguenti:

`az ml experiment prepare --target docker --run-configuration docker`

È consigliabile in esecuzione il server notebook in standard DS4_V2 [macchina virtuale di analisi scientifica dei dati (DSVM) per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu). Dopo il DSVM è configurato, eseguire i comandi seguenti per preparare le immagini Docker:

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Con le immagini Docker preparate, aprire il server Jupyter notebook. Per aprire il server Jupyter notebook, uno Vai a Machine Learning Workbench **notebook** scheda o avviare un server basato su browser:`az ml notebook start`

Archiviati nella directory del codice nell'ambiente di server Jupyter notebook. Eseguire questi notebook in sequenza come numerate, partendo da Code\1_data_ingestion_and_preparation.ipynb.

Selezionare il kernel di corrispondenza del _Template [project_name] [connection_name] e quindi selezionare **impostare Kernel**.

## <a name="data-description"></a>Descrizione dei dati

Il modello utilizza tre set di dati come input, nei file di PM_train.txt PM_test.txt e PM_truth.txt.

-  **Dati di training**: aereo motore dati di errore di esecuzione. I dati di training (PM_train.txt) è costituito da più tempo multivariata serie, con *ciclo* come l'unità di tempo. Include letture dei sensori 21 per ogni ciclo. 

    Ogni serie temporale può essere considerato generato da un motore diverso dello stesso tipo. Ogni motore si presume che iniziano con diversi gradi di usura iniziale e produzione variazione. Queste informazioni sono sconosciute all'utente. 

    In questi dati simulati, il motore si presuppone che funzionamento all'inizio di ogni serie temporale. Iniziano a diminuire a un certo punto durante la serie di cicli operativi. La riduzione di avanzamento e aumento delle dimensioni in termini di grandezza. 

    Quando viene raggiunta una soglia predefinita, il motore è considerato non sicuro per un'ulteriore operazione. L'ultimo ciclo di ogni serie temporale può essere considerato il punto di errore del motore di corrispondente.

-   **Dati di test**: aereo motore dati operativi, senza gli eventi di errore registrati. I dati di test (PM_test.txt) con lo stesso schema di dati come dati di training. L'unica differenza è che i dati non indicano quando si verifica l'errore (ultimo periodo di tempo *non* rappresentano il punto di errore). Non è noto il numero più cicli questo motore può durare prima che si verifica un errore.

- **Dati verificati**: le informazioni di rimanente true cicli per ogni motore nei dati di test. I dati verificati forniscono il numero di cicli di lavoro rimanenti per i motori nei dati di test.

## <a name="scenario-structure"></a>Struttura dello scenario

Il contenuto per lo scenario è disponibile in [repository GitHub] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

Nel repository del file [Leggimi] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) vengono descritti i processi, dalla preparazione dei dati, per la compilazione e il modello di operatività. I tre server Jupyter notebook sono disponibili nella cartella [codice] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) nel repository. 

Verrà quindi descritto il flusso di lavoro scenario dettagliate.

### <a name="task-1-data-ingestion-and-preparation"></a>Attività 1: Inserimento di dati e preparazione

L'inserimento di dati server Jupyter Notebook in Code/1_data_ingestion_and_preparation.ipnyb carica i tre set di dati di input nel formato di frame di dati Pandas. Quindi, esegua prepara i dati per la modellazione e passi visualizzazione alcuni dati preliminari. I dati vengono quindi trasformati nel formato PySpark e archiviati in un contenitore di archiviazione Blob di Azure nella sottoscrizione di Azure per l'utilizzo nell'attività successiva di modellazione.

### <a name="task-2-model-building-and-evaluation"></a>Attività 2: Compilazione del modello e valutazione

Il modello di compilazione server Jupyter Notebook in Code/2_model_building_and_evaluation.ipnyb legge il PySpark eseguire il training e set di dati dall'archiviazione Blob di test. Quindi, una rete LSTM è compilata con i set di dati di training. Le prestazioni del modello viene misurata nel set di test. Il modello risulta viene serializzato e archiviato nel contesto di calcolo locale per l'utilizzo nell'attività di rendere operativo.

### <a name="task-3-operationalization"></a>Attività 3: rendere operativo

Il server Jupyter Notebook in Code/3_operationalization.ipnyb rendere operativo Usa il modello stored per compilare le funzioni e schema in cui sono richieste per chiamare il modello in un servizio web ospitato in Azure. Blocco note le funzioni di test e quindi passa l'asset di rendere operativo in un file ZIP (comprime) file.

Il file compresso contiene i file:

- **modellstm.JSON**: il file di definizione dello schema per la distribuzione. 
- **lstmscore.py**: il **Init ()** e **Run ()** funzioni, che sono necessarie per il servizio web di Azure.
- **lstm.Model**: la directory di definizione del modello.

Il blocco appunti verifica le funzioni con la definizione del modello prima che le risorse di rendere operativo per la distribuzione dei pacchetti. Le istruzioni per la distribuzione sono incluse alla fine del blocco appunti.


## <a name="conclusion"></a>Conclusioni

Questa esercitazione viene utilizzato uno scenario semplice in un solo dati origine (valori sensore) viene utilizzata per eseguire stime. In più manutenzione predittiva scenari avanzati, ad esempio il [predittiva manutenzione modellazione R blocco appunti Guida](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), molte origini dati potrebbero essere utilizzate. Altre origini dati possono includere i record di cronologia di manutenzione, log degli errori e le funzionalità di computer e l'operatore. Origini dati aggiuntive potrebbero richiedere diversi tipi di trattamento per essere utilizzato nelle reti di formazione. È anche importante ottimizzare i modelli per i parametri corretti, come per le dimensioni di finestra. 

È possibile modificare le parti pertinenti di questo scenario e provare a scenari diversi problemi, ad esempio quelli descritti nella [Guida modellazione predittiva di manutenzione](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1), che prevede più altre origini dati.


## <a name="references"></a>Riferimenti

- [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
- [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
- [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)

