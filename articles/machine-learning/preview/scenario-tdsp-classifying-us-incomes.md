---
title: Classificazione del reddito - Team Data Science Process - Azure Machine Learning | Microsoft Docs
description: Come usare il modello di Team Data Science Process per creare un progetto in Azure Machine Learning per la classificazione dei redditi negli Stati Uniti.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: 3e92687657b4e80e75fd869da454970622f7178c
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Classificazione dei redditi con un progetto di Team Data Science Process (TDSP)

## <a name="introduction"></a>Introduzione

La standardizzazione della struttura e della documentazione dei progetti di data science, che è associata a un [ciclo di vita di data science](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) prestabilito, è fondamentale per favorire una collaborazione efficace nei team di data science. La creazione di progetti di Azure Machine Learning con il modello di [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) offre un framework per questo tipo di standardizzazione.

In passato è stato reso disponibile un [repository GitHub per la struttura e i modelli del progetto di TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Tuttavia, fino a oggi non è stato mai possibile creare istanze della struttura e dei modelli di TDSP all'interno di uno strumento di data science. Oggi è possibile creare progetti di Azure Machine Learning di cui creare istanze con la [struttura e i modelli di documentazione di TDSP per Azure Machine Learning](https://github.com/amlsamples/tdsp). Le istruzioni su come usare la struttura e i modelli di TDSP in Azure Machine Learning sono disponibili [qui](https://aka.ms/how-to-use-tdsp-in-aml). In questo articolo viene fornito un esempio di come creare un progetto di apprendimento automatico usando la struttura di TDSP, come completarlo con codice, elementi e documenti specifici del progetto e come eseguirlo in Azure Machine Learning.

## <a name="link-to-github-repository"></a>Collegamento al repository GitHub
La documentazione di riepilogo relativa all'esempio è disponibile [qui](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome). La documentazione più completa è disponibile nel sito GitHub.

### <a name="purpose"></a>Scopo
Lo scopo principale di questo esempio è mostrare come creare un'istanza di un progetto di apprendimento automatico ed eseguire il progetto usando la struttura e i modelli di [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) in Azure Machine Learning. A questo scopo, vengono usati i ben noti [dati del censimento degli Stati Uniti del 1994 dal repository UCI per l'apprendimento automatico](https://archive.ics.uci.edu/ml/datasets/adult). L'attività di modellazione consiste nello stimare le classi di reddito annuale dalle informazioni del censimento degli Stati Uniti, ad esempio età, razza, livello di istruzione, paese di origine e così via.

### <a name="scope"></a>Scope
 * Esplorazione, training e distribuzione dei dati di un modello di apprendimento automatico che risolve il problema di stima descritto nella panoramica del caso d'uso. 
 * Esecuzione del progetto in Azure Machine Learning usando il modello di Team Data Science Process (TDSP) per questo progetto. Per l'esecuzione del progetto e la creazione del report, si userà il ciclo di vita di TDSP.
 * Operazionalizzazione della soluzione direttamente da Azure Machine Learning in servizi contenitore di Azure.

 Il progetto evidenzia diverse funzionalità di Azure Machine Learning, tra cui la creazione di istanze e l'uso della struttura di TDSP, l'esecuzione di codice in notebook di Jupyter e in file Python e la semplice operazionalizzazione in servizi contenitore di Azure con Docker e Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Ciclo di vita di Team Data Science Process (TDSP)
Vedere [Team Data Science Process (TDSP) Lifecycle](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) (Ciclo di vita di Team Data Science Process - TDSP)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Prerequisiti
### <a name="required-subscription-hardware-software"></a>Requisiti obbligatori: sottoscrizione, hardware, software
1. Una [sottoscrizione di Azure](https://azure.microsoft.com). È anche possibile ottenere una [sottoscrizione gratuita](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) per eseguire questo esempio.
2. Una [macchina virtuale di data science di Azure con Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm). Dimensioni della macchina virtuale: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), con 4 CPU virtuali e 14 GB di RAM. Benché questo esempio sia stato testato su una macchina virtuale di data science di Azure, è probabile che funzionerà su qualsiasi computer Windows 10.
3. Esaminare la documentazione di Azure Machine Learning e dei servizi correlati. Vedere di seguito per i collegamenti.
4. Assicurarsi di aver installato correttamente Azure Machine Learning in base a quando indicato nella [guida introduttiva all'installazione](quickstart-installation.md).

Il set di dati per questo esempio è stato ottenuto dal repository UCI per l'apprendimento automatico [[collegamento]](https://archive.ics.uci.edu/ml/datasets/adult). Il set di dati è stato tratto dal database del censimento degli Stati Uniti del 1994 e contiene informazioni su censimento e reddito di circa 50.000 persone. Si tratta di un set di dati strutturato con caratteristiche numeriche e di categoria e una destinazione costituita da due categorie di reddito (">50.000" e " <=50.000"). 

### <a name="optional-version-control-repository"></a>Facoltativo: repository per il controllo delle versioni
Per salvare e sottoporre al controllo delle versioni il progetto e il suo contenuto, è necessario usare un repository per il controllo delle versioni in cui eseguire queste operazioni. È possibile immettere il percorso del repository Git durante la creazione del nuovo progetto usando il modello di TDSP in Azure Machine Learning. Per altre informazioni, vedere [How to use Git in Azure Machine Learning](using-git-ml-project.md) (Come usare Git in Azure Machine Learning).

### <a name="informational-about-azure-machine-learning"></a>Informazioni su Azure Machine Learning
* [FAQ - How to get started](frequently-asked-questions.md) (Domande frequenti - Come iniziare)
* [Panoramica](overview-what-is-azure-ml.md)
* [Installazione](quickstart-installation.md)
* [Execution](experimentation-service-configuration.md) (Esecuzione)
* [Uso di TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Leggere e scrivere file](how-to-read-write-files.md)
* [Using Git with Azure Machine Learning](using-git-ml-project.md) (Uso di Git con Azure Machine Learning)
* [Deploying an ML model as a web service](model-management-service-deploy.md) (Distribuzione di un modello di apprendimento automatico come servizio Web)

### <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Classify US incomes - TDSP project" (Classificazione redditi Stati Uniti - Progetto di TDSP) e selezionare il modello
5.  Fare clic su **Crea**

Se si specifica un percorso di repository Git vuoto durante la creazione del progetto (nella casella appropriata), il repository verrà completato con la struttura del progetto e il contenuto dopo la creazione del progetto.

## <a name="use-case-overview"></a>Panoramica del caso d'uso
Il problema consiste nel determinare in che modo i dati socioeconomici acquisiti durante il censimento degli Stati Uniti possono aiutare a stimare il reddito annuale dei cittadini negli Stati Uniti. In base a queste caratteristiche del censimento, l'attività di apprendimento automatico è stimare se il reddito di una persona superi o meno 50.000 dollari (attività di classificazione binaria).

## <a name="data-description"></a>Descrizione dei dati
Per informazioni dettagliate sui dati, vedere la [descrizione](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) nel repository UCI. 

Questi dati sono stati estratti dal database dell'ufficio del censimento all'indirizzo https://www.census.gov/en.html. 


* In totale, sono presenti 48.842 istanze (prima di qualsiasi filtro), in una combinazione di istanze continue e discrete (training = 32,.561, test = 16.281)
* Probabilità per l'etichetta ">50.000" : 23,93% / 24,78% (senza incognite)
* Probabilità per l'etichetta "<=50.000": 76,07% / 75,22% (senza incognite)  

* **DESTINAZIONE**: classe di reddito ">50.000", "<=50.000". Questi valori vengono sostituiti rispettivamente da 1 e 0 in fase di preparazione dei dati.
* **CARATTERISTICHE**: età, classe lavorativa, livello di istruzione, razza, sesso, ore di lavoro a settimana e così via.


## <a name="project-structure-execution-and-reporting"></a>Struttura del progetto, esecuzione e creazione di report

### <a name="structure"></a>Structure
Per questo progetto, sono stati usati la struttura di cartelle e i modelli di documentazione di TDSP (di seguito), che seguono il [ciclo di vita di TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Il progetto viene creato in base alle istruzioni fornite [qui](https://aka.ms/how-to-use-tdsp-in-aml). Dopo aver immesso il codice e gli elementi del progetto, la struttura è simile alla seguente (vedere la struttura del progetto all'interno del riquadro rosso nella figura).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Esecuzione
In questo esempio il codice viene eseguito nell'**ambiente di calcolo locale**. Per altre informazioni sulle [opzioni di esecuzione](experimentation-service-configuration.md), fare riferimento alla documentazione di Azure Machine Learning.

L'esecuzione di uno script Python in un runtime Python locale è semplice:

    az ml experiment submit -c local my_script.py

È possibile fare doppio clic sui file di notebook di IPython dalla struttura del progetto a sinistra nell'interfaccia utente di Azure Machine Learning ed eseguirli nel server notebook di Jupyter.


Il flusso di lavoro di data science dettagliato è il seguente:

* [**Acquisizione e comprensione dei dati**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

I dati sono stati scaricati in formato CSV dagli URL del repository UCI per l'apprendimento automatico [[collegamento]](https://archive.ics.uci.edu/ml/datasets/adult). Le caratteristiche, la destinazione e le rispettive trasformazioni vengono descritte in modo dettagliato nel file ProjectReport.md.

Il codice per l'acquisizione e la comprensione dei dati si trova in: /code/01_data_acquisition_and_understanding.

L'esplorazione dei dati viene eseguita usando l'[utilità IDEAR (Interactive Data Exploration and Reporting)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) di Python 3 pubblicata come parte della [famiglia di strumenti di data science di TDSP](https://github.com/Azure/Azure-TDSP-Utilities). Questa utilità permette di generare report standardizzati di esplorazione dei dati per i dati che contengono caratteristiche numeriche e di categoria e la destinazione. I dettagli relativi all'uso dell'utilità IDEAR di Python 3 vengono forniti di seguito. 

il report finale di esplorazione dei dati è [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). Una visualizzazione del report IDEAR è mostrata di seguito:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modellazione**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Sono stati creati due modelli con convalida incrociata in tre sezioni: Elastic Net e Random Forest. È stato usato il [campionamento a 59 punti](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) per la ricerca casuale su griglia come strategia per la convalida incrociata e l'ottimizzazione dei parametri del modello. L'accuratezza dei modelli è stata misurata utilizzando l'area sottesa alla curva (AUC, Area Under Curve) nel set di dati di test. 

Il codice per la modellazione si trova in: /code/02_modeling.

L'area sottesa alla curva dei modelli Elastic Net e Random Forest era >0,85. Entrambi i modelli vengono salvati nei file pickled.pkl e vengono restituiti i tracciati ROC per entrambi i modelli. L'area sottesa alla curva del modello Random Forest è 0,92, mentre quella del modello Elastic Net è 0,90. Inoltre, per l'interpretazione dei modelli, l'importanza delle caratteristiche per il modello Random Forest viene restituita in un file CSV e tracciata in un PDF (solo le prime 20 caratteristiche predittive).

La curva ROC del **modello Random Forest** sui dati di test viene mostrata di seguito. Questo è il modello che è stato distribuito:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

L'importanza delle caratteristiche (prime 20) del modello Random Forest viene mostrata di seguito. Mostra le caratteristiche di guadagno di capitale, istruzione, stato civile e così via, indicando quelle che hanno l'importanza maggiore.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Distribuzione**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Il modello Random Forest è stato distribuito come servizio Web in un cluster nel [servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/). L'ambiente di operazionalizzazione effettua il provisioning di Docker e Kubernetes nel cluster per gestire la distribuzione del servizio Web. Altre informazioni sul processo di operazionalizzazione sono disponibili [qui](model-management-service-deploy.md). 

Il codice per la distribuzione si trova in: /code/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Report finale del progetto](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Informazioni dettagliate su ognuna delle sezioni precedenti vengono fornite nel report finale del progetto compilato [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Il report del progetto contiene anche altri dettagli sul caso d'uso, le metriche delle prestazioni del modello, la distribuzione e l'infrastruttura su cui è stato sviluppato e distribuito il progetto.

Il report del progetto, insieme al contenuto dell'intera cartella del progetto e il repository per il controllo delle versioni possono essere inviati al client.


## <a name="conclusion"></a>Conclusioni

In questo esempio è stato mostrato come usare la struttura e i modelli di TDSP in Azure Machine Learning. Tramite i modelli di documento ed elemento è possibile:
1. Definire correttamente scopo e ambito di un progetto
2. Creare un team del progetto con responsabilità e ruoli distribuiti
3. Strutturare ed eseguire progetti in base alle fasi del ciclo di vita di TDSP
4. Sviluppare report standardizzati tramite le utilità di data science di TDSP, tra cui il report di esplorazione e visualizzazione dei dati IDEAR.
5. Preparare un report finale del progetto di data science che può essere inviato a un client

Questa funzionalità di Azure Machine Learning può semplificare la standardizzazione e la collaborazione all'interno dei team di data science.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere i riferimenti seguenti:

[How to use Team Data Science Process (TDSP) in Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml) (Come usare Team Data Science Process, TDSP, in Azure Machine Learning)

[Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[TDSP project template for Azure Machine Learning](https://aka.ms/tdspamlgithubrepo) (Modello di progetto di TDSP per Azure Machine Learning)

[Set di dati sul reddito degli Stati Uniti del repository UCI per l'apprendimento automatico](https://archive.ics.uci.edu/ml/datasets/adult)