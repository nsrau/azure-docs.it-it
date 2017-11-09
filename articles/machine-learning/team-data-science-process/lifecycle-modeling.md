---
title: Fase di modellazione del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di modellazione dei progetti di data science."
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
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>Modellazione

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla **fase di modellazione** del processo di data science. Questo processo fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita del processo di data science per i team 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Funzionalità ottimali dei dati per il modello di Machine Learning.
* Un modello ML informativo che consente di stimare la destinazione in modo più accurato.
* Un modello ML adatto per la produzione.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

* **Progettazione delle funzioni**: creare le funzionalità dei dati dai dati non elaborati per facilitare il training del modello.
* **Training del modello**: trovare il modello che risponde alla domanda in modo più accurato rispetto alle metriche di riuscita.
* Stabilire se il modello è **adatto alla produzione**.

### <a name="31-feature-engineering"></a>3.1 Progettazione delle funzioni
La progettazione di funzionalità prevede l'inclusione, l'aggregazione e la trasformazione delle variabili non elaborate per creare funzionalità usate nell'analisi. Se si desidera comprendere i risultati di un modello, è necessario comprendere in che modo le funzioni sono correlate tra loro e come gli algoritmi di Machine Learning vengono usati da tali funzionalità. Questo passaggio richiede una combinazione creativa di competenze a livello di dominio e di informazioni approfondite ottenute dal passaggio di esplorazione dei dati. Si tratta di un compromesso di ricerca e inclusione di variabili informative evitando troppe variabili non correlate. Le variabili informative migliorano il risultato, mentre le variabili senza relazione introducono rumore inutile nel modello. È anche necessario generare le seguenti funzionalità per i nuovi dati ottenuti durante la valutazione. La generazione di queste funzionalità, quindi, può dipendere esclusivamente dai dati disponibili al momento della valutazione. Per indicazioni tecniche nella progettazione delle funzionalità quando si usano diverse tecnologie di dati di Azure, vedere [Progettazione di funzionalità in Cortana Analytics Process](create-features.md). 

### <a name="32-model-training"></a>3.2 Training dei modelli
A seconda del tipo di domanda a cui si sta tentando di rispondere, sono disponibili numerosi algoritmi di modellazione. Per istruzioni sulla scelta dell'algoritmo, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Sebbene questo articolo sia stato scritto per Azure Machine Learning, le informazioni fornite possono essere usate per qualsiasi progetto Machine Learning. 

Il processo di training del modello include i passaggi seguenti: 

* **Suddividere i dati di input** in modo casuale per la modellazione in un set di dati di training e un set di dati di test.
* **Compilare i modelli** tramite il set di dati di training.
* **Valutare** (set di dati di training e set di dati di test) una serie di algoritmi di Machine Learning concorrenti insieme a diversi parametri di regolazione associati (noto come sweep parametrico), pensati per rispondere alle domande di interesse con i dati correnti.
* **Determinare la soluzione "migliore"** per rispondere alla domanda confrontando la metrica di riuscita tra metodi alternativi.

> [!NOTE]
> **Evitare perdite**: la perdita di dati può essere causata dall'inclusione di dati dall'esterno del set di dati di training che consente a un modello o a un algoritmo di Machine Learning di eseguire stime estremamente valide. La perdita è il motivo comune che infastidisce gli esperti di dati quando ottengono risultati predittivi che appaiono troppo validi per essere reali. Queste dipendenze possono essere difficili da rilevare. Per evitare la perdita, spesso è necessaria l'iterazione della compilazione di un set di dati di analisi, della creazione di un modello e della valutazione dell'accuratezza. 
> 
> 

Con TDSP è disponibile uno [strumento di creazione di report e modellazione automatizzati](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling), da eseguire in diversi algoritmi e sweep parametrici per produrre un modello di base. Lo strumento produce anche un report di base sulla modellazione che riepiloga le prestazioni di ogni combinazione di modello e parametro, inclusa l'importanza della variabile. Anche questo processo è iterativo poiché potrebbe risultare in una nuova progettazione di funzionalità. 

## <a name="artifacts"></a>Elementi
Gli elementi generati in questa fase includono:

* [**Set di funzionalità**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): le funzionalità sviluppate per la modellazione sono descritte nella sezione **Set di funzionalità** del report di **definizione dei dati**. Il report contiene i puntatori al codice per generare le funzionalità e una descrizione sul metodo di generazione della funzionalità.
* [**Report dei modelli**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): per ogni modello provato viene generato un report standard basato su modelli che fornisce dettagli su ogni esperimento.
* **Decisione di Checkpoint**: valutare se le prestazioni del modello sono sufficientemente adatte per la distribuzione in un sistema di produzione. Alcune domande chiave da porsi:
  * Il modello risponde alla domanda con sufficiente certezza rispetto ai dati del test? 
  * È necessario provare soluzioni alternative, come raccogliere dati aggiuntivi, eseguire nuova progettazione di funzionalità o provare con altri algoritmi?

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

* [1. Comprensione del business](lifecycle-business-understanding.md)
* [2. Acquisizione e comprensione dei dati](lifecycle-data.md)
* [3. Modellazione](lifecycle-modeling.md)
* [4. Distribuzione](lifecycle-deployment.md)
* [5. Accettazione del cliente](lifecycle-acceptance.md)

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Example walkthroughs](walkthroughs.md) (Procedure dettagliate di esempio). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess). 