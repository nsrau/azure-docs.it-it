---
title: Fase di distribuzione del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di distribuzione dei progetti di data science"
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
ms.openlocfilehash: c498e51316ba0f1dacb876479c2941e26c22a328
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="deployment"></a>Distribuzione

Questo articolo descrive gli obiettivi, le attività e i risultati finali associati alla distribuzione del processo di data science per i team. Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Comprensione del business**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Obiettivo
Distribuire modelli con una pipeline di dati in un ambiente di produzione o di simil-produzione per l'accettazione da parte di un utente finale. 

## <a name="how-to-do-it"></a>Procedura
Attività principali descritte in questa fase:

**Rendere operativo il modello**: distribuire il modello e la pipeline in un ambiente di produzione o di simil-produzione per l'uso da parte dell'applicazione.

### <a name="operationalize-a-model"></a>Rendere operativo un modello
Quando si ottiene un set di modelli con prestazioni ottimali, è possibile renderli operativi per l'uso da parte di altre applicazioni. In base ai requisiti aziendali, le previsioni vengono eseguite in tempo reale o in batch. Per distribuire i modelli, è possibile esporli con un'interfaccia API aperta. L'interfaccia consente al modello di essere usato facilmente da diverse applicazioni, ad esempio:

   * Siti Web online
   * Fogli di calcolo 
   * Dashboard
   * Applicazioni line-of-business 
   * Applicazioni back-end 

Per esempi su come rendere operativi i modelli con un servizio Web di Azure Machine Learning, vedere [Pubblicare un servizio Web di Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). È anche opportuno creare dati di telemetria e monitorare il modello di produzione e la pipeline di dati distribuiti. Questa procedura consente allo stato del sistema successivo di segnalare eventuali problemi e risolverli.  

## <a name="artifacts"></a>Elementi

* Dashboard di stato che mostra l'integrità del sistema e le metriche chiave
* Report di modellazione finale con informazioni dettagliate sulla distribuzione
* Documento finale sull'architettura della soluzione


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Comprensione del business](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Sono disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning](http://aka.ms/datascienceprocess).
