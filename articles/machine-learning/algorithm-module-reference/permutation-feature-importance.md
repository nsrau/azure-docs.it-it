---
title: 'Permutazione Caratteristica Importanza: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Permutazione importanza funzionalità in Azure Machine Learning per calcolare i punteggi di importanza della funzionalità di permutazione delle variabili di funzionalità, dati un modello sottoposto a training e un set di dati di test.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456064"
---
# <a name="permutation-feature-importance"></a>Permutation Feature Importance

Questo articolo descrive come usare il modulo Permutazione importanza funzionalità nella finestra di progettazione di Azure Machine Learning (anteprima) per calcolare un set di punteggi di importanza delle funzionalità per il set di dati. Questi punteggi consentono di determinare le funzioni migliori da utilizzare in un modello.

In questo modulo, i valori delle funzionalità vengono mescolati in modo casuale, una colonna alla volta. Le prestazioni del modello vengono misurate prima e dopo. È possibile scegliere una delle metriche standard per misurare le prestazioni.

I punteggi restituiti dal modulo rappresentano la *modifica* delle prestazioni di un modello sottoposto a training, dopo la permutazione. Le caratteristiche importanti sono in genere più sensibili al processo di rimescolamento, quindi si tradurranno in punteggi di importanza più alta. 

In questo articolo viene fornita una panoramica della funzionalità di permutazione, della relativa base teorica e delle relative applicazioni nell'apprendimento automatico: [Permutation Feature Importance](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Come utilizzare permutazione L'importanza della funzione

La generazione di un set di punteggi di funzionalità richiede un modello già sottoposto a training e un set di dati di test.  

1.  Aggiungere il modulo Permutazione importanza funzionalità alla pipeline. È possibile trovare questo modulo nella categoria **Selezione funzionalità.** 

2.  Collegare un modello sottoposto a training all'input a sinistra. Il modello deve essere un modello di regressione o un modello di classificazione.  

3.  Nell'input a destra, collegare un set di dati. È preferibile scegliere uno diverso dal set di dati usato per il training del modello. Questo set di dati viene usato per il punteggio in base al modello sottoposto a training. Viene inoltre utilizzato per la valutazione del modello dopo che i valori delle entità geografiche sono stati modificati.  

4.  In **Valore di serie casuale**, immettere un valore da utilizzare come valore di serie per la randomizzazione. Se si specifica 0 (impostazione predefinita), viene generato un numero in base all'orologio di sistema.

     Un valore di valore di serie è facoltativo, ma è necessario fornire un valore se si desidera riproducibilità tra le esecuzioni della stessa pipeline.  

5.  Per **Metrica per la misurazione delle prestazioni,** selezionare una singola metrica da utilizzare quando si calcola la qualità del modello dopo la permutazione.  

     La finestra di progettazione di Azure Machine Learning supporta le metriche seguenti, a seconda che si stia valutando un modello di classificazione o regressione:Azure Machine Learning designer supports the following metrics, depending on whether you're evaluating a classification or regression model:  

    -   **Classificazione**

        Precisione, Precisione, Richiamo  

    -   **Regressione**

        Precisione, richiamo, Errore assoluto medio, Errore al quadrato radice, Errore assoluto relativo, Errore al quadrato relativo, Coefficiente di determinazione  

     Per una descrizione più dettagliata di queste metriche di valutazione e del modo in cui vengono calcolate, vedere [Valutare il modello](evaluate-model.md).  

6.  Inviare la pipeline.  

7.  Il modulo restituisce un elenco di colonne di entità geografiche e i punteggi ad esse associati. L'elenco è classificato in ordine decrescente dei punteggi.  


##  <a name="technical-notes"></a>Note tecniche

Permutazione Importanza funzionalità funziona modificando in modo casuale i valori di ogni colonna di funzionalità, una colonna alla volta. Viene quindi valutato il modello. 

Le classificazioni fornite dal modulo sono spesso diverse da quelle ottenute da [Selezione funzionalità basata su filtro.](filter-based-feature-selection.md) Selezione feature basata su filtro calcola i punteggi *prima* della creazione di un modello. 

Il motivo della differenza è che permutazione l'importanza della funzionalità non misura l'associazione tra una feature e un valore di destinazione. Al contrario, acquisisce l'influenza di ogni funzionalità sulle stime dal modello.
  
## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
