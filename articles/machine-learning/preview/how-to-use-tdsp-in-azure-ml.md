---
title: Strutturare i progetti con il modello di Team Data Science Process | Microsoft Docs
description: Come creare i modelli di Team Data Science Process in Azure ML per strutturare i progetti di collaborazione.
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>Strutturare i progetti con il modello di Team Data Science Process

Questo documento contiene istruzioni su come creare i progetti data science in Azure Machine Learning con i modelli di Team Data Science Process, ovvero TDSP, che strutturano i progetti di collaborazione e riproducibilità. 


## <a name="what-is-team-data-science-process"></a>Definizione di Team Data Science Process
Team Data Science Process è un processo di data science rapido e iterativo per l'esecuzione e la distribuzione di soluzioni di analisi avanzate. È progettato per migliorare la collaborazione e l'efficienza dei team di data science nelle organizzazioni aziendali. Supporta questi obiettivi grazie a quattro componenti principali:

1. Una definizione del [ciclo di vita di data science](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) standard.
2. Struttura di progetto, [documentazione e modelli di report](https://github.com/Azure/Azure-TDSP-ProjectTemplate) standard
3. Infrastruttura e risorse per l'esecuzione del progetto, ad esempio infrastruttura di calcolo e archiviazione e repository di codice.
4. [Strumenti e utilità](https://github.com/Azure/Azure-TDSP-Utilities) per le attività del progetto di data science, ad esempio controllo della versione e revisione del codice, esplorazione e modellazione dei dati e pianificazione di lavoro collaborativi.

Per una descrizione completa del processo TDSP, vedere la [panoramica su Team Data Science Process](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md).

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>Motivi per cui è consigliabile usare i modelli e le strutture del processo TDSP
La standardizzazione della struttura, il ciclo di vita e la documentazione dei progetti di data science sono fondamentali per semplificare una collaborazione efficace nei team di data science. La creazione di progetti di Azure Machine Learning con il processo TDSP offre un framework per un lavoro di squadra coordinato.

In passato è stato reso disponibile un [repository GitHub per la struttura e i modelli del progetto di TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) per contribuire a raggiungere tali obiettivi. Tuttavia, fino a oggi non è stato mai possibile creare istanze della struttura e dei modelli del processo TDSP all'interno di uno strumento di data science. È ora possibile creare un progetto di Azure Machine Learning che crei le istanze per i modelli di documentazione e struttura del processo TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Aspetti da considerare *prima* di creare un nuovo progetto
Ecco una serie di aspetti da tenere presente o controllare *prima* di creare un nuovo progetto:
* [Modello](https://aka.ms/tdspamlgithubrepo) di Azure Machine Learning per il processo TDSP.
* I contenuti, ad eccezione di quelli nella cartella "documenti" devono avere dimensioni inferiori a 25 MB. Vedere la **NOTA** seguente.
* La cartella sample\_data è destinata solo a file di dati di piccole dimensioni, inferiori a 5 MB, e può essere usata per testare il codice o eseguire attività iniziali di sviluppo.
* L'archiviazione di file quali quelli di Office Word, PowerPoint e così via, può aumentare notevolmente le dimensioni della cartella "documenti". Per archiviare questi file si consiglia di trovare un wiki collaborativo, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), o altre risorse collaborative.
* Per la gestione di file di grandi dimensioni e di output in Azure Machine Learning, leggere [questo articolo](http://aka.ms/aml-largefiles).

> [!NOTE]
> Assicurarsi che un file diverso da readme.md, tutto il contenuto della documentazione correlata, ad esempio file di testo, file markdown, file di immagini e altri documenti, non usati durante l'esecuzione del progetto si trovino nella cartella denominata "documenti", in minuscolo. Si tratta di una cartella speciale ignorata dall'esecuzione di Azure Machine Learning, pertanto il contenuto di questa cartella non viene copiato inutilmente per il calcolo di destinazione. In aggiunta, gli oggetti in questa cartella non contano per il limite di 25 MB delle dimensioni del progetto, pertanto è possibile archiviare, ad esempio, file di immagini di grandi dimensioni necessari nella documentazione. Questi vengono comunque rilevati da Git nella Cronologia di esecuzione. 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>Creazione di un'istanza per i modelli e la struttura del processo TDSP dalla raccolta di modelli di Azure Machine Learning
Per creare un nuovo progetto con la struttura e i modelli di documentazione di Team Data Science Process, completare le procedure seguenti: 

### <a name="click-on-new-project"></a>Fare clic su "Nuovo progetto"
Aprire Azure Machine Learning. In **Progetti** in alto a sinistra fare clic su **+** e selezionare **Nuovo progetto** per creare un nuovo progetto.

![Nuovo progetto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>Creazione di un nuovo progetto strutturato con TDSP
Specificare i parametri e le informazioni nelle rispettive caselle:

- Project name (Nome progetto)
- Directory del progetto
- Descrizione del progetto
- Un percorso del repository Git vuoto
- Nome dell'area di lavoro

Nella casella **Cerca** digitare quindi *TDSP*. Quando viene visualizzata l'icona per **strutturare un progetto con TDSP**, fare clic su di essa per selezionare il modello. Fare quindi clic sul pulsante **Crea** per creare il nuovo progetto con la struttura di TDSP. Se si specifica un repository Git vuoto durante la creazione del progetto, nella casella appropriata, il repository verrà completato con la struttura del progetto e il contenuto dopo la creazione del progetto.

![Creare un progetto di TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Esaminare la struttura del progetto di TDSP
Dopo aver creato il nuovo progetto, è possibile esaminarne la struttura, nel riquadro a sinistra della figura seguente. Contiene tutti gli aspetti della documentazione standard per poter capire le esigenze aziendali, le fasi del ciclo di vita del processo TDSP, il percorso dati, la definizione e l'architettura del modello di documentazione. Questa struttura deriva dalla struttura TDSP pubblicata [qui](https://github.com/Azure/Azure-TDSP-ProjectTemplate), con alcune modifiche. Ad esempio, molti modelli di documento vengono uniti in file un markdown, vale a dire, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Struttura della cartella del progetto
Il modello del progetto TDSP contiene le cartelle di primo livello seguenti:
1. **codice**: contiene il codice
2. **documenti**: contiene la documentazione necessaria sul progetto, ad esempio file markdown, file multimediali correlati e così via.
3. **sample_data**: contiene dati **CAMPIONE, di piccole dimensioni** che possono essere usati per le attività iniziali di sviluppo o test. In genere, non più di pochi MB, ovvero 5. Non adatta a set di dati completi o di grandi dimensioni.

![Dati di esempio](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>Uso di modelli e strutture del processo TDSP
È necessario popolare i modelli e la struttura con informazioni specifiche del progetto, ovvero codice e informazioni necessarie per l'esecuzione e la distribuzione del progetto. Il file [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) è un modello che deve essere modificato direttamente con le informazioni rilevanti per il progetto. È dotato di una serie di domande che consentono di inserire le informazioni per ognuna delle quattro fasi del [ciclo di vita di Team Data Science Process](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md).

Per un esempio di come appare una struttura di progetto durante l'esecuzione o al suo completamento è possibile guardare la figura di seguito nel riquadro a sinistra. È tratta dall'articolo [Progetto di esempio di Team Data Science Process: classificare i ricavi in base ai dati di censimento degli Stati Uniti in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Struttura di progetto di esempio](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>Documentazione del progetto
Fare riferimento ai [modelli di documentazione TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) per la documentazione del progetto. Nel modello corrente di documentazione TDSP di Azure Machine Learning si consiglia di includere tutte le informazioni del file [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Questo modello deve essere compilato con informazioni specifiche sul progetto. 

È anche possibile usare un altro modello [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) per inserire le informazioni non incluse nel documento principale del progetto, ma che sono comunque utili per il documento. 

### <a name="example-project-report"></a>Report del progetto di esempio
Un esempio di report del progetto è reperibile [qui](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md). Si tratta del report di progetto per il [progetto di esempio relativo alla classificazione dei ricavi](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome), che illustra come creare istanze e usare il modello TDSP per un progetto di data science.

## <a name="next-steps"></a>Passaggi successivi
Per semplificare l'uso dei modelli e della struttura TDSP nei progetti di Azure Machine Learning sono disponibili diversi esempi di progetto funzionanti nella documentazione di Azure Machine Learning.

- Per un'esercitazione che illustri come creare un progetto TDSP in Azure Machine Learning, vedere [Progetto di esempio di Team Data Science Process: classificare i ricavi in base ai dati di censimento degli Stati Uniti in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) 
- Per un esempio che usi l'apprendimento avanzato in NLP in un'istanza di progetto creata con TDSP in Azure Machine Learning, vedere [Riconoscimento di entità biomediche tramite l'elaborazione del linguaggio naturale con l'apprendimento avanzato](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)

