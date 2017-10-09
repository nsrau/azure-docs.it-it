---
title: Ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: Procedura necessaria per eseguire i progetti di analisi scientifica dei dati.
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
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="team-data-science-process-lifecycle"></a>Ciclo di vita del processo di analisi scientifica dei dati per i team

Il processo di analisi scientifica dei dati per i team (TDSP) fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive tutti i passaggi generalmente seguiti dai progetti in fase di esecuzione. Se si usa un altro ciclo di vita per l'analisi scientifica dei dati, ad esempio [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) o un processo personalizzato dell'organizzazione, è comunque possibile usare il TDSP basato sulle attività con questi cicli di vita di sviluppo. 

Questo ciclo di vita è stato messo a punto per i progetti di analisi scientifica dei dati destinati a far parte di applicazioni intelligenti. Queste applicazioni distribuiscono modelli di Machine Learning o intelligenza artificiale per l'analisi predittiva. Anche i progetti di data science esplorativi e i progetti analitici ad hoc possono trarre vantaggio dall'utilizzo di questo processo. Ma in questi casi alcuni dei passaggi descritti possono non essere necessari.    

Il ciclo di vita TDSP è composto da cinque fasi principali che vengono eseguite in modo iterativo. incluse le seguenti:

* [1. Comprensione del business](lifecycle-business-understanding.md)
* [2. Acquisizione e comprensione dei dati](lifecycle-data.md)
* [3. Modellazione](lifecycle-modeling.md)
* [4. Distribuzione](lifecycle-deployment.md)
* [5. Accettazione del cliente](lifecycle-acceptance.md)

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Il ciclo di vita del processo di data science per i team viene modellato come sequenza di passaggi iterati che offrono indicazioni sulle attività necessarie per usare modelli predittivi. Questi modelli possono essere distribuiti in un ambiente di produzione e usati per compilare applicazioni intelligenti. L'obiettivo di questo ciclo di vita del processo consiste nel procedere con il progetto di analisi scientifica di dati verso un punto finale di evidente coinvolgimento. Sebbene sia vero che l'analisi scientifica dei dati è un esercizio di ricerca e scoperta, essere in grado di comunicare queste attività chiaramente al team e ai clienti che usano un set ben definito di elementi che si servono di modelli standard consente di evitare malintesi e aumenta le probabilità di esito positivo di un progetto complesso di analisi scientifica dei dati.

Per ogni fase, specificare le informazioni seguenti:

* **Obiettivi**: gli obiettivi specifici.
* **Procedura**: le attività specifiche descritte e le istruzioni fornite per il completamento delle attività.
* **Elementi**: i risultati finali e il supporto durante la produzione degli stessi.

## <a name="next-steps"></a>Passaggi successivi
Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Procedure dettagliate di esempio](walkthroughs.md). Viene spiegato come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess).


