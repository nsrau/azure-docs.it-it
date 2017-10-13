---
title: Fase di acquisizione e comprensione dei dati del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di acquisizione e comprensione dei dati dei progetti di data science."
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
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>Acquisizione e comprensione dei dati

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla **fase di acquisizione e comprensione dei dati** del processo di data science per i team. Questo processo fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita del processo di data science per i team 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Un set di dati pulito e di alta qualità, le cui relazioni con le variabili target vengono comprese e che si trova nell'ambiente di analisi appropriato, pronto per la modellazione.
* È stata sviluppata un'architettura della soluzione della pipeline di dati per aggiornare e valutare regolarmente i dati.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

* **Inserire i dati** nell'ambiente di analisi target.
* **Esplorare i dati** per determinare se la qualità dei dati è sufficiente per rispondere alla domanda. 
* **Configurare una pipeline di dati** per calcolare dati nuovi o regolarmente aggiornati.

### <a name="21-ingest-the-data"></a>2.1 Inserire i dati
Impostare il processo per spostare i dati da posizioni di origine a posizioni destinazione in cui devono essere eseguite operazioni di analisi come il training e le stime. Per informazioni tecniche e per le opzioni su come eseguire questa operazione con diversi servizi di dati di Azure, vedere [Caricare i dati in ambienti di archiviazione per l'analisi](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 Esplorare i dati
Prima di eseguire il training dei modelli, è necessario sviluppare una buona comprensione dei dati. I set di dati reali sono spesso fastidiosi, mancano di valori, sono numerosi o presentano altre discrepanze. La visualizzazione e il riepilogo dei dati consente di controllare la qualità dei dati e fornire le informazioni necessarie per elaborare i dati prima che siano pronti per la modellazione. Spesso, questo processo è iterativo.

TDSP fornisce un'utilità automatica denominata [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) per visualizzare i dati e preparare report di riepilogo sui dati. È consigliabile iniziare con IDEAR per esplorare i dati e sviluppare una comprensione iniziale sui dati in modo interattivo, senza codice, e quindi scrivere il codice personalizzato per la visualizzazione e l'esplorazione dei dati. Per istruzioni sulla pilizia dei dati, vedere [Attività per preparare i dati per operazioni avanzate con Machine Learning](prepare-data.md).  

Quando si è soddisfatti della qualità dei dati puliti, il passaggio successivo consiste nel comprendere meglio i modelli presenti nei dati, che consentono di scegliere e sviluppare un modello predittivo appropriato per il target. Verificare la qualità della connessione dei dati al target e scoprire se sono presenti dati sufficienti per procedere con i passaggi successivi di modellazione. Anche questo processo è iterativo. Potrebbe essere necessario trovare nuove origini dati con dati più accurati o più pertinenti per aumentare il set di dati identificato nella fase precedente.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 Impostare una pipeline di dati
Oltre all'inserimento iniziale e alla pulizia dei dati, è necessario in genere impostare un processo per valutare nuovi dati o aggiornare regolarmente i dati, come parte di un processo costante di apprendimento. Per ottenere questo risultato, è possibile configurare una pipeline di dati o un flusso di lavoro. Ecco un [esempio](move-sql-azure-adf.md) su come configurare una pipeline con [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

In questa fase viene sviluppata un'architettura della soluzione della pipeline di dati. La pipeline viene sviluppata parallelamente alle seguenti fasi del progetto di analisi scientifica dei dati. La pipeline può essere basata su batch, su un flusso, in tempo reale o ibrida, a seconda delle esigenze aziendali e delle restrizioni dei sistemi esistenti in cui la soluzione è integrata. 

## <a name="artifacts"></a>Elementi
Ecco i risultati finali di questa fase:

* [**Report sulla qualità dei dati**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): il report contiene riepiloghi dei dati, relazioni tra ogni attributo e il target, valutazione della variabile e così via. Lo strumento [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) viene fornito come parte di TDSP e può generare rapidamente questo report su qualsiasi set di dati tabulare, ad esempio un file CSV o una tabella relazionale. 
* **Architettura della soluzione**: può trattarsi di un diagramma o della descrizione della pipeline di dati usata per eseguire valutazioni o stime sui nuovi dati dopo aver creato un modello. Include anche la pipeline per ripetere il training del modello basato su nuovi dati. Il documento viene archiviato in questa directory di [progetto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando si usa il modello della struttura di directory di TDSP.
* **Decisione di checkpoint**: prima di iniziare la progettazione completa delle funzionalità e la compilazione del modello, è possibile riesaminare il progetto per determinare se il valore previsto è sufficiente per continuare la progettazione. È possibile, ad esempio, che tutto sia predisposto per procedere, che si debbano raccogliere altri dati o abbandonare il progetto, se non esistono dati per rispondere alla domanda.

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

* [1. Comprensione del business](lifecycle-business-understanding.md)
* [2. Acquisizione e comprensione dei dati](lifecycle-data.md)
* [3. Modellazione](lifecycle-modeling.md)
* [4. Distribuzione](lifecycle-deployment.md)
* [5. Accettazione del cliente](lifecycle-acceptance.md)

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Example walkthroughs](walkthroughs.md) (Procedure dettagliate di esempio). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.  

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess).