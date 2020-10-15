---
title: 'Albero delle decisioni con boosting Two-Class: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di albero delle decisioni con boosting Two-Class in Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo degli alberi delle decisioni con boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 833caa0cf264fb49d8b32255f429132cd888c6c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907732"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modulo di albero delle decisioni con boosting Two-Class

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo degli alberi delle decisioni con boosting. 

Un albero delle decisioni con boosting è un metodo di apprendimento dell'insieme in cui il secondo albero corregge gli errori del primo albero, il terzo albero corregge gli errori del primo e del secondo albero e così via. Le stime si basano sull'intero insieme di alberi che esegue la stima.
  
In genere, quando sono configurati correttamente, gli alberi delle decisioni con boosting sono i metodi più semplici per ottenere prestazioni ottimali in un'ampia gamma di attività di machine learning. Tuttavia, sono anche uno degli strumenti di apprendimento più intensivo di memoria e l'implementazione corrente contiene tutti gli elementi in memoria. Pertanto, un modello di albero delle decisioni con boosting potrebbe non essere in grado di elaborare i set di impostazioni di grandi dimensioni che alcuni elementi di apprendimento lineari possono gestire.

Questo modulo è basato sull'algoritmo LightGBM.

## <a name="how-to-configure"></a>Modalità di configurazione

Questo modulo crea un modello di classificazione non sottoposto a training. Poiché la classificazione è un metodo di apprendimento supervisionato, per eseguire il training del modello è necessario un *set di dati con tag* che includa una colonna di etichette con un valore per tutte le righe.

È possibile eseguire il training di questo tipo di modello utilizzando [Train Model](././train-model.md). 

1.  In Azure Machine Learning aggiungere il modulo di **albero delle decisioni con boosting** alla pipeline.
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .
  
    + **Singolo parametro**: se si sa come si desidera configurare il modello, è possibile fornire un set di valori specifico come argomenti.
  
    + **Intervallo**di parametri: se non si è certi dei parametri migliori, è possibile trovare i parametri ottimali usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) . Viene fornito un intervallo di valori e il trainer scorre più combinazioni di impostazioni per determinare la combinazione di valori che produce il risultato migliore.
  
3.  Per il **numero massimo di foglie per albero**, indicare il numero massimo di nodi terminali (foglie) che è possibile creare in qualsiasi albero.
  
     Aumentando questo valore, potenzialmente si aumentano le dimensioni dell'albero e si ottiene una maggiore precisione. Possono tuttavia verificarsi casi di overfitting e tempi di training più lunghi.
  
4.  Per il **numero minimo di campioni per nodo foglia**, indicare il numero di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.  
  
     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.
  
5.  Per la **velocità di apprendimento**, digitare un numero compreso tra 0 e 1 che definisce le dimensioni del passaggio durante l'apprendimento.  
  
     La velocità di apprendimento determina la velocità o la lentezza della convergenza degli studenti sulla soluzione ottimale. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training impiega più tempo per convergere sulla soluzione migliore.
  
6.  Per il **numero di alberi costruiti**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma saranno necessari tempi di training maggiori.
  
     Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, impostare il valore su 1. Tuttavia, quando si esegue questa operazione, viene prodotto un solo albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.
  
7.  Per il valore di **inizializzazione numerico casuale**, digitare facoltativamente un numero intero non negativo da usare come valore di inizializzazione casuale. La specifica di un valore di inizializzazione garantisce la riproducibilità tra esecuzioni con gli stessi dati e parametri.  
  
     Per impostazione predefinita, il valore di inizializzazione casuale viene impostato su 0, il che significa che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema.  Le esecuzioni successive con un valore di inizializzazione casuale possono avere risultati diversi.
  

9. Eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.  
   
## <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per l'assegnazione dei punteggi, aggiungere il modulo **Score Model** a una pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 