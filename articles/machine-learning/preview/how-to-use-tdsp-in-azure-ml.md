---
title: Strutturare i progetti con un modello di Team Data Science Process | Microsoft Docs
description: Come creare un'istanza dei modelli di Team Data Science Process (TDSP) in Azure Machine Learning per strutturare i progetti di collaborazione.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 0f7a6d0a58e2dcfd94e20a81ae12b42f238016c8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Strutturare i progetti con un modello di Team Data Science Process

Questo documento contiene istruzioni su come creare progetti data science in Azure Machine Learning usando i modelli di Team Data Science Process (TDSP), che consentono di strutturare i progetti di collaborazione e riproducibilità. 


## <a name="what-is-the-team-data-science-process"></a>Introduzione a Team Data Science Process
Team Data Science Process è un processo di data science rapido e iterativo per l'esecuzione e la distribuzione di soluzioni di analisi avanzate. È progettato per migliorare la collaborazione e l'efficienza dei team di data science nelle organizzazioni aziendali. Supporta questi obiettivi grazie a quattro componenti principali:

   * Una definizione del [ciclo di vita di data science](../team-data-science-process/lifecycle.md) standard.
   * Struttura di progetto, [documentazione e modelli di report](https://github.com/Azure/Azure-TDSP-ProjectTemplate) standard.
   * Infrastruttura e risorse per l'esecuzione del progetto, ad esempio infrastruttura di calcolo e archiviazione e repository di codice rispettivamente.
   * [Strumenti e utilità](https://github.com/Azure/Azure-TDSP-Utilities) per le attività del progetto di data science, ad esempio:
      - Controllo della versione di collaborazione
      - Revisione del codice
      - Esplorazione e modellazione dei dati
      - Pianificazione del lavoro

Per una descrizione completa del processo TDSP, vedere la [panoramica su Team Data Science Process](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Motivi per cui è consigliabile usare i modelli e la struttura di TDSP
La standardizzazione della struttura, il ciclo di vita e la documentazione dei progetti di data science sono fondamentali per semplificare una collaborazione efficace nei team di data science. Creare progetti di apprendimento automatico con il modello TDSP per offrire tale framework per un lavoro di squadra coordinato.

In passato è stato reso disponibile un [repository GitHub per la struttura e i modelli del processo TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) per contribuire a raggiungere tali obiettivi. Tuttavia, fino a oggi non è stato mai possibile creare istanze della struttura e dei modelli del processo TDSP all'interno di uno strumento di data science. È ora possibile creare un progetto di apprendimento automatico che crei le istanze per i modelli di documentazione e struttura del processo TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Aspetti da considerare prima di creare un nuovo progetto
*Prima* di creare un nuovo progetto, controllare gli elementi seguenti:
* Controllare il [modello](https://aka.ms/tdspamlgithubrepo) di Machine Learning per il processo TDSP.
* I contenuti, ad eccezione di quelli già presenti nella cartella "documenti", devono avere dimensioni inferiori a 25 MB. Vedere la nota riportata alla fine dell'elenco.
* La cartella sample\_data è destinata solo a file di dati di piccole dimensioni, inferiori a 5 MB, e può essere usata per testare il codice o eseguire attività di sviluppo iniziali.
* L'archiviazione di file quali documenti di Word o PowerPoint può aumentare notevolmente le dimensioni della cartella "documenti". Per archiviare questi file si consiglia di trovare un wiki collaborativo, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) o altre risorse collaborative.
* Per informazioni sulla gestione di output e file di grandi dimensioni in Machine Learning, leggere [Persisting changes and dealing with large files](http://aka.ms/aml-largefiles) (Rendere persistenti le modifiche e gestire file di grandi dimensioni).

> [!NOTE]
> Tutti i contenuti relativi alla documentazione, ovvero testo, markdown, immagini e altri file di documentazione, che *non* devono essere usati durante l'esecuzione del progetto, a eccezione del file readme.md, devono trovarsi nella cartella denominata "documenti", tutto minuscolo. La cartella "documenti" costituisce una cartella speciale ignorata dall'esecuzione di Machine Learning e, quindi, il suo contenuto non viene copiato inutilmente per il calcolo di destinazione. Gli oggetti presenti in questa cartella non vengono considerati per calcolare il limite di 25 MB delle dimensioni del progetto. Nella cartella "documenti", ad esempio, vengono archiviati i file di immagine di grandi dimensioni necessari nella documentazione. Questi file vengono comunque rilevati da Git nella cronologia di esecuzione. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Creare un'istanza per i modelli e la struttura del processo TDSP dalla raccolta di modelli di Machine Learning
Per creare un nuovo progetto con la struttura e i modelli di documentazione di TDSP, completare queste procedure.

### <a name="create-a-new-project"></a>Creare un nuovo progetto
Per creare un nuovo progetto, aprire Azure Machine Learning. In **Progetti** nel riquadro in alto a sinistra fare clic sul segno più (**+**) e quindi selezionare **Nuovo progetto**.

![Nuovo progetto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Creare un nuovo progetto strutturato con TDSP
   1. Specificare i parametri e le informazioni nella casella o nell'elenco appropriato:

      - Project name (Nome progetto)
      - Directory del progetto
      - Descrizione del progetto
      - Un percorso del repository Git vuoto
      - Nome dell'area di lavoro

   2. Nella casella **Cerca** digitare quindi **TDSP**. 
   3. Quando viene visualizzata l'opzione per **strutturare un progetto con TDSP**, selezionare il modello. 
   4. Selezionare il pulsante **Crea** per creare il nuovo progetto con una struttura di TDSP. Se si specifica un repository Git vuoto durante la creazione del progetto (nella casella appropriata), al termine della creazione del progetto il repository viene completato con la struttura del progetto e i relativi contenuti.

![Creare un progetto di TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Esaminare la struttura del progetto TDSP
Dopo aver creato il nuovo progetto, è possibile esaminarne la struttura (vedere il riquadro a sinistra nella figura seguente). Contiene tutti gli aspetti della documentazione standard per poter capire le esigenze aziendali, tra cui le fasi del ciclo di vita del processo TDSP, il percorso dati, le definizioni e l'architettura del modello di documentazione. 

La struttura visualizzata deriva dalla struttura di TDSP pubblicata nell'articolo sulla [struttura del progetto TDSP, i documenti e i modelli di elemento](https://github.com/Azure/Azure-TDSP-ProjectTemplate), con alcune modifiche. Ad esempio, molti modelli di documento vengono uniti in file un markdown, vale a dire, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Struttura della cartella del progetto
Il modello del progetto TDSP contiene le cartelle di primo livello seguenti:
   - **codice**: contiene il codice.
   - **documenti**: contiene la documentazione necessaria sul progetto, ad esempio i file markdown e i file multimediali correlati.
   - **sample_data**: contiene dati **CAMPIONE, di piccole dimensioni** che possono essere usati per le attività iniziali di sviluppo o test. In genere, hanno dimensioni inferiori a 5 MB. Questa cartella, infatti, non è adatta a set di dati completi o di grandi dimensioni.

![Dati di esempio](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Uso della struttura e dei modelli di TDSP
È necessario aggiungere alla struttura e ai modelli informazioni specifiche del progetto, ad esempio il codice e le informazioni necessarie per l'esecuzione e la distribuzione del progetto. Il file [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) è un modello che deve essere modificato con le informazioni rilevanti per il progetto. È corredato da una serie di domande che consentono di inserire le informazioni necessarie per ognuna delle quattro fasi del [ciclo di vita di TDSP](../team-data-science-process/lifecycle.md).

Nel riquadro sinistro della figura seguente è illustrato un esempio di come appare una struttura di progetto durante l'esecuzione o al suo completamento. Questo progetto è tratto dal progetto di esempio TDSP sulla [classificazione dei redditi in base ai dati di censimento degli Stati Uniti in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Struttura di progetto di esempio](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Documentazione del progetto
Fare riferimento ai [modelli di documentazione TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) per informazioni su come documentare il progetto. Nel modello corrente di documentazione TDSP di Machine Learning si consiglia di includere tutte le informazioni del file [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Questo modello deve essere compilato con informazioni specifiche sul progetto. 

È possibile anche usare un modello [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) per inserire le informazioni non incluse nel documento principale del progetto, ma che sono comunque utili per il documento. 

### <a name="example-project-report"></a>Report di progetto di esempio
È possibile ottenere un [report di progetto di esempio](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Questo report relativo al [progetto di esempio sulla classificazione dei redditi](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) illustra come creare istanze e usare un modello di TDSP per un progetto di data science.

## <a name="next-steps"></a>Passaggi successivi
Per capire meglio come poter usare i modelli e la struttura di TDSP in progetti di Machine Learning, nella documentazione di Azure Machine Learning sono disponibili diversi esempi di progetto completi.

- Per un esempio che mostra come creare un progetto TDSP in Machine Learning, vedere il progetto di esempio TDSP sulla [classificazione dei redditi in base ai dati di censimento degli Stati Uniti in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Per un esempio in cui si usa l'elaborazione in linguaggio naturale con l'apprendimento avanzato in un'istanza di progetto creata con TDSP in Machine Learning, vedere l'articolo sul [riconoscimento di entità biomediche tramite l'elaborazione in linguaggio naturale con l'apprendimento avanzato](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

