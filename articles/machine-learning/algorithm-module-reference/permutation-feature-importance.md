---
title: 'Importanza della funzionalità di permutazione: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Permutation feature Importance nel servizio Azure Machine Learning per calcolare i punteggi di importanza della funzionalità di permutazione delle variabili di funzionalità date un modello sottoposto a training e un set di dati di
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517943"
---
# <a name="permutation-feature-importance"></a>Importanza della funzionalità di permutazione

Questo articolo descrive come usare il modulo [Permutation feature](permutation-feature-importance.md) importance in Azure Machine Learning Designer (Preview) per calcolare un set di punteggi di importanza della funzionalità per il set di dati. Questi punteggi vengono utilizzati per determinare le funzionalità migliori da utilizzare in un modello.

In questo modulo i valori delle funzionalità vengono mescolati in modo casuale, una colonna alla volta e le prestazioni del modello vengono misurate prima e dopo. È possibile scegliere una delle metriche standard fornite per misurare le prestazioni.

I punteggi restituiti dal modulo rappresentano la **modifica** delle prestazioni di un modello sottoposto a training, dopo la permutazione. Le funzionalità importanti sono in genere più sensibili al processo di shuffle e pertanto comportano punteggi di importanza maggiore. 

Questo articolo fornisce una panoramica generale dell'importanza della funzionalità di permutazione, della sua base teorica e delle relative applicazioni in Machine Learning: [importanza della funzionalità di permutazione](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Come usare l'importanza della funzionalità di permutazione

Per generare un set di punteggi di funzionalità, è necessario disporre di un modello già sottoposto a training, oltre a un set di dati di test.  

1.  Aggiungere il modulo **Permutation feature** Importance alla pipeline. È possibile trovare questo modulo nella categoria **Selezione funzionalità** . 

2.  Connettere un modello sottoposto a training all'input di sinistra. Il modello deve essere un modello di regressione o un modello di classificazione.  

3.  Nell'input di destra, connettere un set di dati, preferibilmente uno diverso dal set di dati usato per il training del modello. Questo set di dati viene utilizzato per l'assegnazione dei punteggi in base al modello sottoposto a training e per la valutazione del modello dopo la modifica dei valori delle funzionalità.  

4.  Per il valore di **inizializzazione casuale**, digitare un valore da usare come valore di inizializzazione per la sequenza casuale. Se si specifica 0 (impostazione predefinita), viene generato un numero in base al clock di sistema.

     Un valore di inizializzazione è facoltativo, ma è necessario fornire un valore se si desidera la riproducibilità tra le esecuzioni della stessa pipeline.  

5.  Per la **metrica per la misurazione delle prestazioni**, selezionare una singola metrica da usare quando si calcola la qualità del modello dopo la permutazione.  

     Azure Machine Learning Designer supporta le metriche seguenti, a seconda che si stia valutando un modello di classificazione o di regressione:  

    -   **Classificazione**

        Accuratezza, precisione, richiamo, perdita di log media  

    -   **Regressionee**

        Precisione, richiamo, errore assoluto medio, radice errore quadratico medio, errore assoluto relativo, errore quadratico relativo, coefficiente di determinazione  

     Per una descrizione più dettagliata di queste metriche di valutazione e il modo in cui vengono calcolate, vedere [Evaluate Model](evaluate-model.md).  

6.  Eseguire la pipeline.  

7.  Il modulo restituisce un elenco di colonne di funzionalità e i punteggi associati, classificati in ordine dei punteggi, decrescente.  


##  <a name="technical-notes"></a>Note tecniche

Questa sezione fornisce informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>In che modo viene confrontato con altri metodi di selezione delle funzioni?

L'importanza della funzionalità di permutazione funziona modificando in modo casuale i valori di ogni colonna della funzionalità, una colonna alla volta e quindi valutando il modello. 

Le classificazioni fornite dall'importanza della funzionalità di permutazione sono spesso diverse da quelle ottenute dalla [selezione delle funzioni basata su filtro](filter-based-feature-selection.md), che calcola i punteggi **prima** della creazione di un modello. 

Ciò è dovuto al fatto che l'importanza della funzionalità di permutazione non misura l'associazione tra una funzionalità e un valore di destinazione, ma acquisisce la quantità di influenza di ogni funzionalità sulle stime del modello.
  
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
