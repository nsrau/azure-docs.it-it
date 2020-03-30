---
title: 'Cross Validate Model: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Cross-Validate Model in Azure Machine Learning per convalidare in modo incrociato le stime dei parametri per i modelli di classificazione o regressione partizionando i dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477647"
---
# <a name="cross-validate-model"></a>Cross Validate Model

Questo articolo descrive come usare il modulo Cross Validate Model nella finestra di progettazione di Azure Machine Learning (anteprima). *La convalida incrociata* è una tecnica spesso utilizzata nell'apprendimento automatico per valutare sia la variabilità di un set di dati che l'affidabilità di qualsiasi modello sottoposto a training tramite tali dati.  

Il modulo Cross Validate Model accetta come input un set di dati con etichetta, insieme a un modello di classificazione o regressione non sottoposto a training. Divide il set di dati in un certo numero di subset (*piega*), compila un modello su ogni piega e quindi restituisce un set di statistiche di accuratezza per ogni piega. Confrontando le statistiche di precisione per tutte le pieghe, è possibile interpretare la qualità del set di dati. È quindi possibile comprendere se il modello è suscettibile di variazioni nei dati.  

Cross Validate Model restituisce anche i risultati e le probabilità stimati per il set di dati, in modo da poter valutare l'affidabilità delle stime.  

### <a name="how-cross-validation-works"></a>Funzionamento della convalida incrociata

1. La convalida incrociata divide in modo casuale i dati di training in pieghe. 

   L'algoritmo prevede 10 sezioni per impostazione predefinita se il set di dati non è stato partizionato in precedenza. Per dividere il set di dati in un numero diverso di pieghe, è possibile utilizzare il modulo [Partizione e Campione](partition-and-sample.md) e indicare il numero di pieghe da utilizzare.  

2.  Il modulo accantonato i dati nella piega 1 da utilizzare per la convalida. (Questo è a volte chiamato la *piega holdout*.) Il modulo utilizza le pieghe rimanenti per eseguire il training di un modello. 

    Ad esempio, se si creano cinque pieghe, il modulo genera cinque modelli durante la convalida incrociata. Il modulo esegue il training di ogni modello utilizzando quattro quinti dei dati. Testa ogni modello sul restante quinto.  

3.  Durante il test del modello per ogni piega, il modulo valuta statistiche di accuratezza multiple. Le statistiche utilizzate dal modulo dipendono dal tipo di modello che si sta valutando. Vengono utilizzate statistiche diverse per valutare i modelli di classificazione rispetto ai modelli di regressione.  

4.  Al termine del processo di compilazione e valutazione per tutte le pieghe, Cross Validate Model genera un set di metriche delle prestazioni e risultati con punteggio per tutti i dati. Esamina queste metriche per vedere se una piega singola ha una precisione alta o bassa. 

### <a name="advantages-of-cross-validation"></a>Vantaggi della convalida incrociata

Un modo diverso e comune di valutare un modello consiste nel dividere i dati in un set di training e test usando [Dividi dati](split-data.md), quindi convalidare il modello nei dati di training. Ma la convalida incrociata offre alcuni vantaggi:  

-   La convalida incrociata utilizza più dati di test.

    La convalida incrociata misura le prestazioni del modello con i parametri specificati in uno spazio dati più ampio. In altre parte, la convalida incrociata usa l'intero set di dati di training sia per il training che per la valutazione, anziché per una parte. Al contrario, se si convalida un modello utilizzando i dati generati da una divisione casuale, in genere si valuta il modello solo per il 30% o meno dei dati disponibili.  

    Tuttavia, poiché la convalida incrociata eseguo il training e convalida il modello più volte in un set di dati più grande, è molto più intensivo dal punto di vista del calcolo. Richiede molto più tempo rispetto alla convalida su una divisione casuale.  

-   La convalida incrociata valuta sia il set di dati che il modello.

    La convalida incrociata non misura semplicemente l'accuratezza di un modello. Fornisce inoltre un'idea di quanto sia rappresentativo il set di dati e di quanto sia sensibile il modello alle variazioni nei dati.  

## <a name="how-to-use-cross-validate-model"></a>Come utilizzare Cross Validate Model

L'esecuzione della convalida incrociata può richiedere molto tempo se il set di dati è di grandi dimensioni.  Pertanto, è possibile utilizzare Cross Validate Model nella fase iniziale di compilazione e test del modello. In questa fase, è possibile valutare la bontà dei parametri del modello (presupponendo che il tempo di calcolo sia tollerabile). È quindi possibile eseguire il training e valutare il modello utilizzando i parametri stabiliti con i moduli [Modello di training](train-model.md) e Valuta [modello.](evaluate-model.md)

In questo scenario, è necessario eseguire il training e il test del modello utilizzando Cross Validate Model.

1. Aggiungere il modulo Cross Validate Model alla pipeline. È possibile trovarlo nella finestra di progettazione di Azure Machine Learning, nella categoria **Valutazione & punteggio modello.** 

2. Connettere l'output di qualsiasi modello di classificazione o regressione. 

    Ad esempio, se si utilizza **Albero decisionale potenziato** a due classi per la classificazione, configurare il modello con i parametri desiderati. Trascinare quindi un connettore dalla porta modello non **sottoposto** a training del classificatore alla porta corrispondente di Cross Validate Model. 

    > [!TIP] 
    > Non è necessario eseguire il training del modello, perché il modello di convalida incrociata esegue automaticamente il training del modello come parte della valutazione.  
3.  Nella porta **Dataset** di Cross Validate Model connettere qualsiasi set di dati di training con etichetta.  

4.  Nel riquadro destro di Convalida incrociata modello, fare clic su **Modifica colonna**. Selezionare la singola colonna che contiene l'etichetta della classe o il valore stimabile. 

5. Impostare un valore per il parametro **Seme casuale** se si desidera ripetere i risultati della convalida incrociata tra esecuzioni successive sugli stessi dati.  

6. Inviare la pipeline.

7. Vedere la sezione [Risultati](#results) per una descrizione dei report.

## <a name="results"></a>Risultati

Al termine di tutte le iterazioni, Cross Validate Model crea punteggi per l'intero set di dati. Crea inoltre metriche sulle prestazioni che è possibile utilizzare per valutare la qualità del modello.

### <a name="scored-results"></a>Scored results

Il primo output del modulo fornisce i dati di origine per ogni riga, insieme ad alcuni valori stimati e probabilità correlate. 

Per visualizzare i risultati, nella pipeline fare clic con il pulsante destro del mouse sul modulo Convalida incrociata del modello. Selezionare **Visualizza risultati score.**

| Nome nuova colonna      | Descrizione                              |
| -------------------- | ---------------------------------------- |
| Scored Labels        | Questa colonna viene aggiunta alla fine del set di dati. Contiene il valore stimato per ogni riga. |
| Probabilità con punteggio | Questa colonna viene aggiunta alla fine del set di dati. Indica la probabilità stimata del valore in **Etichette con punteggio**. |
| Numero di piegatura          | Indica l'indice in base zero della piega a cui è stata assegnata ogni riga di dati durante la convalida incrociata. |

 ### <a name="evaluation-results"></a>Valutazione dei risultati

La seconda relazione è raggruppata per pieghe. Tenere presente che durante l'esecuzione, Cross Validate Model suddivide casualmente i dati di training in *n* pieghe (per impostazione predefinita, 10). In ogni iterazione del set di dati, Cross Validate Model usa una piega come set di dati di convalida. Utilizza le restanti pieghe *n-1* per eseguire il training di un modello. Ognuno dei *modelli n* viene testato rispetto ai dati in tutte le altre pieghe.

In questo report, le pieghe sono elencate in base al valore di indice, in ordine crescente.  Per ordinare in qualsiasi altra colonna, è possibile salvare i risultati come set di dati.

Per visualizzare i risultati, nella pipeline fare clic con il pulsante destro del mouse sul modulo Convalida incrociata del modello. Selezionare **Visualizza risultati valutazione per piega**.


|Nome colonna| Descrizione|
|----|----|
|Numero di piegatura| Identificatore per ogni piegatura. Se sono state create cinque pieghe, sarebbero presenti cinque sottoinsiemi di dati, numerati da 0 a 4.
|Numero di esempi in piega|Il numero di righe assegnate a ogni piegatura. Dovrebbero essere più o meno uguali. |


Il modulo include anche le seguenti metriche per ogni piega, a seconda del tipo di modello che stai valutando: 

+ **Modelli di classificazione**: Precisione, richiamo, Punteggio F, AUC, precisione  

+ **Modelli di regressione**: errore assoluto medio, errore al quadrato della media radice, errore assoluto relativo, errore al quadrato relativo e coefficiente di determinazione


## <a name="technical-notes"></a>Note tecniche  

+ È consigliabile normalizzare i set di dati prima di utilizzarli per la convalida incrociata. 

+ La convalida incrociata del modello è molto più dispendiosa dal punto di vista del calcolo e richiede più tempo rispetto alla convalida del modello tramite un set di dati diviso in modo casuale. Il motivo è che Cross Validate Model esegue il training e convalida il modello più volte.

+ Non è necessario suddividere il set di dati in set di training e test quando si usa la convalida incrociata per misurare l'accuratezza del modello. 


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

