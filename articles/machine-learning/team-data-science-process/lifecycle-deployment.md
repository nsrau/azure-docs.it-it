---
title: Fase di distribuzione del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di distribuzione dei progetti di data science."
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="deployment"></a>Distribuzione

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla **distribuzione** del processo di data science per i team. Questo processo fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita del processo di data science per i team 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Obiettivo
* Distribuire modelli con una pipeline di dati in un ambiente di produzione o di simil-produzione per l'accettazione da parte di un utente finale. 

## <a name="how-to-do-it"></a>Procedura
Attività principali descritte in questa fase:

* **Rendere operativo il modello**: distribuire il modello e la pipeline in un ambiente di produzione o di simil-produzione per l'uso da parte dell'applicazione.

### <a name="41-operationalize-a-model"></a>4.1 Rendere operativo il modello
Quando si ottiene un set di modelli con prestazioni ottimali, è possibile renderli operativi per l'uso da parte di altre applicazioni. In base ai requisiti aziendali, le previsioni vengono eseguite in tempo reale o in batch. I modelli vengono distribuiti esponendoli con un'interfaccia API aperta. L'interfaccia consente al modello di essere facilmente utilizzato da varie applicazioni, ad esempio siti Web online, fogli di calcolo, dashboard o applicazioni line-of-business e back-end. Per esempi su come rendere operativi i modelli con un servizio Web di Azure Machine Learning, vedere [Pubblicare un servizio Web di Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). È anche opportuno creare dati di telemetria e monitorare il modello di produzione e la pipeline di dati distribuiti. Questa procedura consente allo stato del sistema successivo di segnalare eventuali problemi e risolverli.  

## <a name="artifacts"></a>Elementi
* Dashboard di stato sull'integrità del sistema e metriche principali.
* Report di modellazione finale con informazioni dettagliate sulla distribuzione.
* Documento finale sull'architettura della soluzione.


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

* [1. Comprensione del business](lifecycle-business-understanding.md)
* [2. Acquisizione e comprensione dei dati](lifecycle-data.md)
* [3. Modellazione](lifecycle-modeling.md)
* [4. Distribuzione](lifecycle-deployment.md)
* [5. Accettazione del cliente](lifecycle-acceptance.md)

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Procedure dettagliate di esempio](walkthroughs.md). Viene spiegato come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess).
