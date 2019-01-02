---
title: Acquisizione e comprensione dei dati nel processo di data science per i team
description: Obiettivi, attività e risultati finali per la fase di acquisizione e comprensione dei dati dei progetti di data science
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77c67c491b3157103b267864417678c83326814c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141153"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Fase di acquisizione e comprensione dei dati nel processo di data science per i team

Questo articolo descrive gli obiettivi, le attività e i risultati finali associati alla fase di acquisizione e comprensione dei dati nel processo di data science per i team (TDSP). Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Comprensione delle strategie aziendali**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Produrre un set di dati pulito e di alta qualità, di cui sia compresa la relazione con le variabili di destinazione. Inserire il set di dati nell'ambiente di analisi appropriato in modo da creare un modello.
* Sviluppare un'architettura della soluzione della pipeline di dati per aggiornare e valutare regolarmente i dati.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

   * **Inserire i dati** nell'ambiente di analisi target.
   * **Esplorare i dati** per determinare se la qualità dei dati è sufficiente per rispondere alla domanda. 
   * **Configurare una pipeline di dati** per calcolare dati nuovi o regolarmente aggiornati.

### <a name="ingest-the-data"></a>Inserire i dati
Impostare il processo per spostare i dati dalle posizioni di origine alle posizioni destinazione dove si eseguono operazioni di analisi, ad esempio il training e le stime. Per i dettagli tecnici e per le opzioni su come spostare i dati con vari servizi di dati di Azure, vedere [Caricare i dati in ambienti di archiviazione per l'analisi](ingest-data.md). 

### <a name="explore-the-data"></a>Esplorare i dati
Prima di eseguire il training dei modelli, è necessario sviluppare una buona comprensione dei dati. I set di dati reali sono spesso fastidiosi, mancano di valori o presentano altre discrepanze. La visualizzazione e il riepilogo dei dati consentono di controllare la qualità dei dati e offrono le informazioni necessarie per elaborare i dati prima che siano pronti per la modellazione. Spesso, questo processo è iterativo.

Il processo TDSP include un'utilità automatica, denominata [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), che aiuta a visualizzare i dati e preparare report di riepilogo sui dati. È consigliabile iniziare con IDEAR per esplorare i dati e sviluppare una comprensione iniziale dei dati in modo interattivo, senza codice. È possibile successivamente scrivere codice personalizzato per la visualizzazione e l'esplorazione dei dati. Per istruzioni sulla pulizia dei dati, vedere [Attività per preparare i dati per operazioni avanzate con Machine Learning](prepare-data.md).  

Dopo che si è soddisfatti della qualità dei dati ripuliti, il passaggio successivo consiste nel capire meglio i modelli inerenti nei dati. Questo passaggio aiuta a scegliere e a sviluppare un modello predittivo appropriato per il proprio obiettivo. Cercare la prova della precisa connessione dei dati all'obiettivo. Determinare quindi se i dati sono sufficienti per passare alle successive fasi di modellazione. Anche questo processo è iterativo. Potrebbe essere necessario trovare nuove origini dati con dati più accurati o più pertinenti per aumentare il set di dati identificato nella fase precedente. 

### <a name="set-up-a-data-pipeline"></a>Impostare una pipeline di dati
Oltre all'inserimento iniziale e alla pulizia dei dati, è necessario in genere impostare un processo per valutare nuovi dati o aggiornare regolarmente i dati, come parte di un processo costante di apprendimento. Per ottenere questo risultato, è possibile configurare una pipeline di dati o un flusso di lavoro. L'articolo [Spostare i dati da SQL Server locale a SQL Azure con Azure Data Factory](move-sql-azure-adf.md) offre un esempio di come impostare una pipeline con [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

In questa fase si sviluppa un'architettura della soluzione della pipeline di dati. Si sviluppa la pipeline in parallelo con la fase successiva del progetto di data science. A seconda delle esigenze aziendali e dei vincoli dei sistemi esistenti in questa soluzione deve essere integrata, la pipeline può essere di uno dei tipi seguenti: 

   * Basata su batch
   * In streaming o in tempo reale 
   * Un ibrido 

## <a name="artifacts"></a>Elementi
Di seguito sono descritti i risultati finali di questa fase:

   * [Report sulla qualità dei dati](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): il report include i riepiloghi dei dati, le relazioni tra ogni attributo e destinazione, il ranking delle variabili e altro ancora. Lo strumento [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) viene fornito come parte del processo TDSP e può generare rapidamente questo report su qualsiasi set di dati tabulare, ad esempio un file con estensione CSV o una tabella relazionale. 
   * **Architettura della soluzione**: può trattarsi di un diagramma o della descrizione della pipeline di dati usata per assegnare punteggi o eseguire stime sui nuovi dati dopo aver creato un modello. Include anche la pipeline per ripetere il training del modello basato su nuovi dati. Archiviare il documento nella directory di [progetto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando si usa il modello della struttura di directory del progetto TDSP.
   * **Decisione di checkpoint**: prima di iniziare la progettazione completa delle caratteristiche e la creazione del modello, è possibile riesaminare il progetto per determinare se il valore previsto è sufficiente per continuare la progettazione. È possibile ad esempio che tutto sia predisposto per procedere, che si debbano raccogliere altri dati o abbandonare il progetto, se non esistono dati per rispondere alla domanda.

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Comprensione delle strategie aziendali](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Sono disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Usare il processo di data science per i team con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
