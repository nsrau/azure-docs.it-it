---
title: Fase di accettazione del cliente del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
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
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: e150b3e7c7e98443264dd5b8aaeda1bfe6047b2c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="customer-acceptance"></a>Accettazione del cliente

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla fase di accettazione del cliente del processo TDSP. Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Comprensione del business**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Obiettivo
**Finalizzare i risultati del progetto**: verificare che la pipeline, il modello e la relativa distribuzione nell'ambiente di produzione soddisfino gli obiettivi del cliente.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende due attività principali:

   * **Convalida del sistema**: verificare che il modello e la pipeline distribuiti soddisfino le esigenze del cliente.
   * **Consegna del progetto**: consegnare il progetto all'entità che eseguirà il sistema nell'ambiente di produzione.

Il cliente deve verificare che il sistema soddisfi le esigenze aziendali e che risponda alle domande con un'accuratezza accettabile per la distribuzione del sistema nell'ambiente di produzione per l'utilizzo da parte delle applicazioni client. Tutta la documentazione viene finalizzata ed esaminata. Il progetto viene consegnato all'entità responsabile delle operazioni. Questa entità potrebbe essere, ad esempio, un team IT, un team di data science o un agente del cliente responsabile dell'esecuzione del sistema nell'ambiente di produzione. 

## <a name="artifacts"></a>Elementi
Il principale elemento prodotto in questa fase finale è il **Report di uscita del progetto per il cliente**. Questo rapporto tecnico contiene tutti i dettagli del progetto che sono utili per apprendere come usare il sistema. Il processo TDSP include un modello di [report di uscita](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). È possibile usare il modello così com'è o personalizzarlo in base alle specifiche esigenze del cliente. 


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Comprensione del business](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Sono disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning](http://aka.ms/datascienceprocess).
