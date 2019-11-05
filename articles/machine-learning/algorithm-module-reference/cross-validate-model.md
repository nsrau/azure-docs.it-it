---
title: 'Modello Cross Validate: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Cross-Validate Model nel servizio Azure Machine Learning per eseguire la convalida incrociata delle stime dei parametri per i modelli di classificazione o di regressione partizionando i dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512847"
---
# <a name="cross-validate-model"></a>Modello Cross Validate

Questo articolo descrive come usare il modulo **Cross Validate Model** in Azure Machine Learning Designer (anteprima). La *convalida incrociata* è una tecnica importante usata spesso in machine learning per valutare la variabilità di un set di dati e l'affidabilità di qualsiasi modello sottoposto a training usando tali dati.  

Il modulo **Cross Validate Model** accetta come input un set di dati con etichetta, insieme a un modello di classificazione o regressione senza training. Divide il set di dati in un certo numero di subset (*riduzioni*), compila un modello a ogni riduzioni e quindi restituisce un set di statistiche di accuratezza per ogni sezione. Confrontando le statistiche di accuratezza per tutte le riduzioni, è possibile interpretare la qualità del set di dati e determinare se il modello è soggetto a variazioni nei dati.  

Cross-Validate restituisce inoltre i risultati stimati e le probabilità per il set di dati, in modo da poter valutare l'affidabilità delle stime.  

### <a name="how-cross-validation-works"></a>Funzionamento della convalida incrociata

1. La convalida incrociata divide in modo casuale i dati di training in un numero di partizioni, denominate anche *riduzioni*. 

    + Se il set di dati non è stato partizionato in precedenza, per impostazione predefinita l'algoritmo è 10 riduzioni. 
    + Per dividere il set di dati in un numero diverso di riduzioni, è possibile usare il modulo [Partition and Sample](partition-and-sample.md) e indicare il numero di riduzioni da usare.  

2.  Il modulo riserva i dati nella sezione 1 da usare per la convalida (a volte è detta *riduzioni*di dati di mantenimento) e usa le riduzioni rimanenti per eseguire il training di un modello. 

    Se, ad esempio, si creano cinque riduzioni, il modulo genererà cinque modelli durante la convalida incrociata, ogni modello sottoposto a training utilizzando 4/5 dei dati e testato nei 1/5 rimanenti.  

3.  Durante il test del modello per ogni sezione, vengono valutate più statistiche di accuratezza. Le statistiche utilizzate dipendono dal tipo di modello che si sta valutando. Vengono usate statistiche diverse per valutare i modelli di classificazione e i modelli di regressione.  

4.  Quando il processo di compilazione e valutazione è completo per tutte le riduzioni, **Cross Validate Model** genera un set di metriche delle prestazioni e risultati con punteggi per tutti i dati. È consigliabile esaminare queste metriche per verificare se una singola riduzione ha una precisione particolarmente elevata o ridotta 

### <a name="advantages-of-cross-validation"></a>Vantaggi della convalida incrociata

Un metodo diverso e comune per la valutazione di un modello consiste nel dividere i dati in un set di training e di test usando [Split data](split-data.md), quindi convalidare il modello sui dati di training. Tuttavia, la convalida incrociata offre alcuni vantaggi:  

-   La convalida incrociata usa più dati di test.

     La convalida incrociata misura le prestazioni del modello con i parametri specificati in uno spazio dati più grande. Ciò significa che la convalida incrociata usa l'intero set di dati di training sia per il training sia per la valutazione, anziché una parte. Al contrario, se si convalida un modello utilizzando i dati generati da una suddivisione casuale, in genere si valuta il modello solo sul 30% o meno dei dati disponibili.  

     Tuttavia, poiché la convalida incrociata esegue il training e la convalida del modello più volte su un set di dati più grande, è molto più impegnativo a livello di calcolo e richiede molto più tempo rispetto alla convalida su una suddivisione casuale.  

-   La convalida incrociata valuta il set di dati e il modello.

     Con la convalida incrociata non viene semplicemente misurata l'accuratezza di un modello, ma viene anche illustrata la modalità di rappresentazione del set di dati e la sensibilità del modello alle variazioni nei dati.  

## <a name="how-to-use-cross-validate-model"></a>Come usare Cross Validate Model

L'esecuzione della convalida incrociata può richiedere molto tempo se il set di dati è di grandi dimensioni.  Pertanto, è possibile utilizzare **Cross Validate Model** nella fase iniziale della compilazione e del testing del modello, per valutare la bontà dei parametri del modello (presupponendo che il tempo di calcolo sia tollerabile), quindi eseguire il training e la valutazione del modello utilizzando il parametri stabiliti con i moduli [Train Model](train-model.md) e [Evaluate Model](evaluate-model.md) .

In questo scenario è necessario eseguire il training e il test del modello usando **Cross Validate Model**.

1. Aggiungere il modulo **Cross Validate Model** alla pipeline. È possibile trovarlo in Azure Machine Learning Designer, nella categoria **punteggio & valutazione del modello** . 

2. Connettere l'output di un modello di **classificazione** o di **regressione** . 

    Se ad esempio si usa una macchina a **due punti di Bayes** per la classificazione, configurare il modello con i parametri desiderati, quindi trascinare un connettore dalla porta del **modello senza training** del classificatore alla porta corrispondente della **convalida incrociata Modello**. 

    > [!TIP] 
    > Non è necessario eseguire il training del modello perché **Cross-Validate Model** addestra automaticamente il modello come parte della valutazione.  
3.  Sulla porta del **set di dati** di **Cross Validate Model**, connettere qualsiasi set di dati di training con etichetta.  

4.  Nel riquadro **Proprietà** di **Cross Validate Model**fare clic su **Launch Column Selector** e scegliere la singola colonna che contiene l'etichetta Class o il valore stimabile. 

5. Impostare un valore per il parametro **seed casuale** se si desidera essere in grado di ripetere i risultati della convalida incrociata in esecuzioni successive sugli stessi dati.  

6.  Eseguire la pipeline.

7. Per una descrizione dei report, vedere la sezione relativa ai [risultati](#results) .

    Per ottenere una copia del modello da riutilizzare in un secondo momento, fare clic con il pulsante destro del mouse sull'output del modulo che contiene l'algoritmo, ad esempio la **due classi Bayes Point Machine**, e scegliere **Salva come modello con training**.

## <a name="results"></a>Risultati

Una volta completate tutte le iterazioni, **Cross Validate Model** crea i punteggi per l'intero set di dati, nonché le metriche delle prestazioni che è possibile utilizzare per valutare la qualità del modello.

### <a name="scored-results"></a>Risultati con Punteggio

Il primo output del modulo fornisce i dati di origine per ogni riga, insieme ad alcuni valori stimati e a probabilità correlate. 

Per visualizzare questi risultati, nella pipeline fare clic con il pulsante destro del mouse sul modulo **Cross Validate Model** , selezionare **risultati punteggiati**e fare clic su **Visualizza**.

| Nome nuova colonna      | Descrizione                              |
| -------------------- | ---------------------------------------- |
| Etichette con Punteggio        | Questa colonna viene aggiunta alla fine del set di dati e contiene il valore stimato per ogni riga |
| Probabilità con Punteggio | Questa colonna viene aggiunta alla fine del set di dati e indica la probabilità stimata del valore nelle **etichette con punteggio**. |
| Numero di riduzioni          | Indica l'indice in base zero della sezione a cui è stata assegnata ogni riga di dati durante la convalida incrociata. |

 ### <a name="evaluation-results"></a>Valutazione dei risultati

Il secondo report è raggruppato per riduzioni. Tenere presente che, durante l'esecuzione, **Cross Validate Model** suddivide in modo casuale i dati di training in *n* riduzioni (per impostazione predefinita, 10). In ogni iterazione sul set di dati, **Cross Validate Model** usa una riduzioni come set di dati di convalida e usa le riduzioni *n-1* rimanenti per eseguire il training di un modello. Ognuno dei modelli *n* viene testato rispetto ai dati in tutte le altre riduzioni.

In questo report, le riduzioni sono elencate in base al valore di indice in ordine crescente.  Per eseguire l'ordinamento in base a qualsiasi altra colonna, è possibile salvare i risultati come set di dati.

Per visualizzare questi risultati, nella pipeline fare clic con il pulsante destro del mouse sul modulo **Cross Validate Model** , selezionare **Evaluation results by fold**, quindi fare clic su **Visualize**.


|Nome colonna| Descrizione|
|----|----|
|Numero di riduzioni| Identificatore per ogni riduzioni. Se sono state create 5 riduzioni, saranno presenti 5 subset di dati, numerati da 0 a 4.
|Numero di esempi in riduzioni|Numero di righe assegnate a ogni riduzioni. Devono essere approssimativamente uguali. |


Inoltre, le metriche seguenti sono incluse per ogni sezione, a seconda del tipo di modello che si sta valutando. 

+ **Modelli di classificazione**: precisione, richiamo, Punteggio F, AUC, accuratezza  

+ **Modelli di regressione**: errore assoluto medio, radice errore quadratico medio, errore assoluto relativo, errore quadratico relativo e coefficiente di determinazione


## <a name="technical-notes"></a>Note tecniche  

+ È consigliabile normalizzare i set di elementi prima di usarli per la convalida incrociata. 

+ Poiché **Cross Validate Model** esegue il training e la convalida del modello più volte, è molto più impegnativo a livello di calcolo e richiede più tempo rispetto a quando il modello viene convalidato usando un set di dati suddiviso in modo casuale. 

+ Quando si usa la convalida incrociata per misurare l'accuratezza del modello, non è necessario suddividere il set di dati in set di training e di testing. 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 

