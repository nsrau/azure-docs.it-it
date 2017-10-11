---
title: Fase di accettazione da parte del cliente del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di accettazione da parte del cliente dei progetti di data science."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="customer-acceptance"></a>Accettazione del cliente

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla **fase di accettazione da parte del cliente** del processo di data science per i team. Questo processo fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita del processo di data science per i team 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Obiettivo
* **Finalizzare i risultati del progetto**: verificare che la pipeline, il modello e la relativa distribuzione nell'ambiente di produzione soddisfino gli obiettivi del cliente.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende due attività principali:

* **Convalida del sistema**: verificare che il modello e la pipeline distribuiti soddisfino le esigenze del cliente.
* **Consegna del progetto**: all'entità che eseguirà il sistema nell'ambiente di produzione.

Il cliente deve verificare che il sistema soddisfi le esigenze aziendali e che risponda alle domande con un'accuratezza accettabile per la distribuzione nell'ambiente di produzione e l'utilizzo da parte delle applicazioni client. Tutta la documentazione viene finalizzata ed esaminata. La consegna del progetto all'entità responsabile delle operazioni è completa. Questa entità potrebbe essere, ad esempio, un team IT, un team di data science o un agente del cliente responsabile dell'esecuzione del sistema nell'ambiente di produzione. 

## <a name="artifacts"></a>Elementi
Il principale elemento prodotto in questa fase finale è il **Report di uscita del progetto per il cliente**. Si tratta del report tecnico contenente tutti i dettagli del progetto utili per conoscere e usare il sistema. TDSP fornisce un modello di [report di uscita](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) da usare così com'è o da personalizzare secondo le esigenze specifiche del cliente. 


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

* [1. Comprensione del business](lifecycle-business-understanding.md)
* [2. Acquisizione e comprensione dei dati](lifecycle-data.md)
* [3. Modellazione](lifecycle-modeling.md)
* [4. Distribuzione](lifecycle-deployment.md)
* [5. Accettazione del cliente](lifecycle-acceptance.md)

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Procedure dettagliate di esempio](walkthroughs.md). Viene spiegato come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess).
