---
title: Fase di modellazione del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di modellazione dei progetti di data science"
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
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: a7fc5f2128e9c13182ca5bd7a6bd61ae41ef775c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="modeling"></a>Modellazione

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla fase di modellazione del processo TDSP. Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Comprensione del business**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP:

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Determinare le funzionalità ottimali dei dati per il modello di Machine Learning.
* Creare un modello di Machine Learning informativo che consente di stimare il target in modo più accurato.
* Creare un modello di Machine Learning adatto alla produzione.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

  * **Progettazione delle funzioni**: creare le funzionalità dei dati dai dati non elaborati per facilitare il training del modello.
  * **Training del modello**: trovare il modello che risponde alla domanda in modo più accurato rispetto alle metriche di riuscita.
  * Stabilire se il modello è **adatto alla produzione**.

### <a name="feature-engineering"></a>Progettazione delle funzioni
La progettazione di funzionalità prevede l'aggiunta, l'aggregazione e la trasformazione delle variabili non elaborate per creare funzionalità usate nell'analisi. Se si vuole analizzare i risultati di un modello, è necessario comprendere in che modo le funzioni sono correlate tra loro e come gli algoritmi di Machine Learning vengono usati da tali funzionalità. 

Questo passaggio richiede una combinazione creativa di competenze a livello di dominio e di informazioni approfondite ottenute dal passaggio di esplorazione dei dati. La progettazione di funzionalità è un compromesso di ricerca e inclusione di variabili informative, evitando al contempo troppe variabili non correlate. Le variabili informative migliorano il risultato, mentre le variabili non correlate disturbano inutilmente il modello. È anche necessario generare le seguenti funzionalità per i nuovi dati ottenuti durante la valutazione. La generazione di queste funzionalità, quindi, può dipendere esclusivamente dai dati disponibili al momento della valutazione. 

Per indicazioni tecniche sulla progettazione delle funzionalità quando si usano varie tecnologie di dati di Azure, vedere [Progettazione di funzionalità nell'analisi scientifica dei dati](create-features.md). 

### <a name="model-training"></a>Training dei modelli
A seconda del tipo di domanda a cui si tenta di rispondere, sono disponibili numerosi algoritmi di modellazione. Per istruzioni sulla scelta dell'algoritmo, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Sebbene questo articolo sia stato scritto per Microsoft Azure Machine Learning, le informazioni fornite possono essere usate per qualsiasi progetto Machine Learning. 

Il processo di training del modello include i passaggi seguenti: 

   * **Suddividere i dati di input** in modo casuale per la modellazione in un set di dati di training e un set di dati di test.
   * **Compilare i modelli** usando il set di dati di training.
   * **Valutare** il set di dati di test e di training. Usare una serie di algoritmi di Machine Learning concorrenti insieme a diversi parametri di regolazione associati (noti come *sweep parametrico*), pensati per rispondere alle domande di interesse con i dati correnti.
   * **Determinare la soluzione "migliore"** per rispondere alla domanda confrontando la metrica di riuscita tra metodi alternativi.

> [!NOTE]
> **Evitare perdite**: la perdita di dati può essere causata dall'inclusione di dati dall'esterno del set di dati di training che consente a un modello o a un algoritmo di Machine Learning di eseguire stime estremamente valide. La perdita è il motivo comune che infastidisce gli esperti di dati quando ottengono risultati predittivi che appaiono troppo validi per essere reali. Queste dipendenze possono essere difficili da rilevare. Per evitare la perdita, spesso è necessaria l'iterazione della compilazione di un set di dati di analisi, della creazione di un modello e della valutazione dell'accuratezza dei risultati. 
> 
> 

Con il processo TDSP è disponibile uno [strumento di creazione di report e modellazione automatizzato](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling), da eseguire in diversi algoritmi e sweep parametrici per produrre un modello di base. Lo strumento produce anche un report di modellazione di base che riepiloga le prestazioni di ogni combinazione di modello e parametro, inclusa l'importanza delle variabili. Anche questo processo è iterativo poiché potrebbe risultare in una nuova progettazione di funzionalità. 

## <a name="artifacts"></a>Elementi
Gli elementi generati in questa fase includono:

   * [Set di funzionalità](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): le funzionalità sviluppate per la modellazione sono descritte nella sezione **Set di funzionalità** del report di **definizione dei dati**. Il report contiene i puntatori al codice per generare le funzionalità e una descrizione della modalità di generazione della funzionalità.
   * [Report dei modelli](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): per ogni modello provato, viene generato un report standard basato su modelli che fornisce dettagli su ogni esperimento.
   * **Decisione di checkpoint**: valutare se le prestazioni del modello sono sufficienti per la distribuzione in un sistema di produzione. Alcune domande chiave da porsi:
     * Il modello risponde alla domanda con sufficiente certezza rispetto ai dati del test? 
     * È consigliabile tentare approcci alternativi? È consigliabile raccogliere dati aggiuntivi, eseguire nuove progettazioni di funzionalità o provare con altri algoritmi?

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Comprensione del business](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Sono disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning](http://aka.ms/datascienceprocess). 
