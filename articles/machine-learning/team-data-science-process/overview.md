---
title: Introduzione a Team Data Science Process | Microsoft Docs
description: Fornisce una metodologia di data science per offrire soluzioni di analisi predittiva e applicazioni intelligenti.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: bradsev;
ms.openlocfilehash: 75e170f29f9a6abeeb3393e43e6a4c355749044d
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="what-is-the-team-data-science-process"></a>Introduzione a Team Data Science Process

Il processo di data science per i team (TDSP) è una metodologia di data science agile e iterativa mirata a offrire soluzioni di analisi predittiva e applicazioni intelligenti in modo efficiente. Il TDSP consente di migliorare la collaborazione tra team e l'apprendimento. Contiene procedure consigliate e strutture messe a punto da Microsoft e altre aziende del settore che facilitano la corretta implementazione delle iniziative di data science. L'obiettivo è aiutare le aziende a realizzare completamente i vantaggi del proprio programma di analisi.

Questo articolo riporta una panoramica del TDSP e dei suoi componenti principali. Viene fornita una descrizione generica del processo che può essere implementata con un'ampia gamma di strumenti. Altri argomenti collegati riportano descrizioni più dettagliate delle attività di progetto e dei ruoli coinvolti nel ciclo di vita del processo. Inoltre vengono fornite istruzioni su come implementare il TDSP mediante uno specifico set di strumenti e infrastruttura di Microsoft utilizzato per implementare il TDSP nei nostri team.

## <a name="key-components-of-the-tdsp"></a>Componenti principali del TDSP

Il TDSP è formato dai componenti chiave seguenti:

- Una definizione del **ciclo di vita del data science**
- Una **struttura di progetto standardizzata**
- **Infrastruttura e risorse** per i progetti di data science
- **Strumenti e utilità** per l'esecuzione dei progetti


## <a name="data-science-lifecycle"></a>Ciclo di vita del data science

Il processo di data science per i team (TDSP) fornisce un ciclo di vita per strutturare lo sviluppo dei progetti di data science. Il ciclo di vita descrive tutti i passaggi generalmente seguiti dai progetti in fase di esecuzione.

Se si usa un altro ciclo di vita per l'analisi scientifica dei dati, ad esempio [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) o un processo personalizzato dell'organizzazione, è comunque possibile usare il TDSP basato sulle attività nel contesto di tali cicli di vita di sviluppo. In generale queste diverse metodologie hanno molto in comune. 

Questo ciclo di vita è stato messo a punto per i progetti di data science inclusi in applicazioni intelligenti. Queste applicazioni distribuiscono modelli di Machine Learning o intelligenza artificiale per l'analisi predittiva. Anche i progetti di data science esplorativi o i progetti analitici ad hoc possono trarre vantaggio dall'utilizzo di questo processo. Ma in questi casi alcuni dei passaggi descritti possono non essere necessari.    

Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita del processo di data science per i team 2](./media/overview/tdsp-lifecycle2.png) 

Gli obiettivi, le attività e gli elementi di documentazione per ogni fase del ciclo di vita del TDSP sono descritti nell'argomento [Ciclo di vita del processo di data science per i team](lifecycle.md). Queste attività ed elementi sono associati a ruoli di progetto:

- Architetto di soluzioni
- Project manager
- Data scientist
- Responsabile di progetto 

Il diagramma seguente offre una visualizzazione a griglia delle attività (in blu) e degli elementi (in verde) associati a ogni fase del ciclo di vita (sull'asse orizzontale) per questi ruoli (sull'asse verticale). 

![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Struttura di progetto standardizzata

Poiché tutti i progetti condividono la stessa struttura di directory e usano modelli per i documenti di progetto è più facile per i membri del team trovare informazioni relative ai propri progetti. Tutto il codice e i documenti vengono archiviati in un sistema di controllo di versione (VCS) come Git, TFS o Subversion per consentire la collaborazione tra team. Monitorare le attività e funzionalità in un sistema di monitoraggio di progetto agile come Jira, Rally o Visual Studio Team Services consente un monitoraggio più stretto delle singole funzionalità. Tale monitoraggio permette inoltre ai team di ottenere stime dei costi migliori. Il TDSP consiglia di creare un repository separato per ogni progetto del VCS per il controllo delle versioni, la protezione delle informazioni e la collaborazione. La struttura standard per tutti i progetti consente di creare conoscenze istituzionali nell'intera organizzazione.

Sono forniti modelli per la struttura di cartelle e i documenti richiesti in posizioni standard. Questa struttura di cartelle consente di organizzare i file che contengono codice per l'esplorazione dei dati e l'estrazione delle funzioni e che registrano le iterazioni del modello. Questi modelli semplificano ai membri del team la comprensione delle attività completate da altri utenti e l'aggiunta di nuovi membri ai team. È facile visualizzare e aggiornare i modelli di documento in formato markdown. Utilizzare modelli per fornire elenchi di controllo con domande chiave per ogni progetto per assicurarsi che il problema sia ben definito e che i risultati finali soddisfino la qualità prevista. Tra gli esempi sono inclusi:

- un atto costitutivo di progetto per documentare il problema aziendale e l'ambito del progetto stesso
- report di dati per documentare la struttura e le statistiche dei dati non elaborati
- report di modello per documentare le funzionalità derivate
- metriche di prestazioni del modello come curve ROC o MSE


![TDSP-directories](./media/overview/tdsp-dir-structure.png)

La struttura di directory può essere clonata da [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruttura e risorse per i progetti di data science  

Il TDSP fornisce suggerimenti per la gestione dell'infrastruttura di analisi e archiviazione, ad esempio:

- file system su cloud per l'archiviazione dei set di dati, 
- database
- cluster Big Data (Hadoop o Spark) 
- servizi di Machine Learning. 

L'infrastruttura di analisi e archiviazione può essere su cloud o in locale. Si tratta della posizione in cui sono archiviati i set di dati non elaborati ed elaborati. Questa infrastruttura consente di eseguire analisi riproducibili. Inoltre evita la duplicazione, che può causare incoerenze e costi di infrastruttura non necessari. Vengono forniti strumenti per eseguire il provisioning di risorse condivise, monitorarle e consentire a ogni membro del team di connettersi a quelle risorse in modo sicuro. È inoltre consigliabile che i membri del progetto creino un ambiente di calcolo coerente. Diversi membri del team possono quindi replicare e convalidare gli esperimenti.

Di seguito è riportato un esempio di un team che lavora su più progetti e condivide diversi componenti dell'infrastruttura di analisi su cloud.

![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Strumenti e utilità per l'esecuzione dei progetti

Introdurre processi nella maggior parte delle organizzazioni è complesso. Gli strumenti disponibili per implementare il processo e il ciclo di vita del data science contribuiscono a ridurre le barriere e ad aumentare la coerenza dell'adozione. Il TDSP fornisce un set iniziale di strumenti e script per una veloce adozione del TDSP stesso all'interno di un team. Consente inoltre di automatizzare alcune delle attività comuni nel ciclo di vita del data science, ad esempio l'esplorazione dei dati e la modellazione iniziale. Viene fornita una struttura ben definita in base alla quale i singoli possono contribuire con strumenti e utilità condivise nel repository di codice condiviso del team. Queste risorse possono quindi essere sfruttate da altri progetti all'interno di team o dell'organizzazione. Il TDSP prevede anche una pianificazione per elargire strumenti e utilità all'intera community. Le utilità TDSP possono essere clonate da [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Passaggi successivi

[Processo di data science per i team: ruoli e attività](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) descrive i ruoli del personale chiave e le relative attività associate per un team di data science che standardizza il processo. 
