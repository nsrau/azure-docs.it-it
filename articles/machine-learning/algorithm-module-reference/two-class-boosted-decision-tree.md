---
title: 'Albero delle decisioni con boosting a due classi: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo a albero delle decisioni con boosting a due classi nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo degli alberi delle decisioni con boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 812fb8d312b165bd43f600da520f390f9c6399fe
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128410"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modulo di albero delle decisioni con boosting a due classi

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo degli alberi delle decisioni con boosting. 

Un albero delle decisioni con boosting è un metodo di apprendimento dell'insieme in cui il secondo albero corregge gli errori del primo albero, il terzo albero corregge gli errori del primo e del secondo albero e così via.  Le stime si basano sull'intero insieme di alberi che esegue la stima.
  
In genere, quando sono configurati correttamente, gli alberi delle decisioni con boosting sono i metodi più semplici per ottenere prestazioni ottimali in un'ampia gamma di attività di machine learning. Tuttavia, sono anche uno degli strumenti di apprendimento più intensivo di memoria e l'implementazione corrente contiene tutti gli elementi in memoria. Pertanto, un modello di albero delle decisioni con boosting potrebbe non essere in grado di elaborare i set di impostazioni di grandi dimensioni che alcuni elementi di apprendimento lineari possono gestire.

## <a name="how-to-configure"></a>Come configurare

Questo modulo crea un modello di classificazione non sottoposto a training. Poiché la classificazione è un metodo di apprendimento supervisionato, per eseguire il training del modello è necessario un *set di dati con tag* che includa una colonna di etichette con un valore per tutte le righe.

È possibile eseguire il training di questo tipo di modello utilizzando [Train Model](././train-model.md). 

1.  In Azure Machine Learning aggiungere il modulo di **albero delle decisioni** con boosting all'esperimento.
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .
  
    + **Singolo parametro**: Se si conosce il modo in cui si desidera configurare il modello, è possibile specificare come argomenti un set specifico di valori.
  
  
3.  Per il **numero massimo di foglie per albero**, indicare il numero massimo di nodi terminali (foglie) che è possibile creare in qualsiasi albero.
  
     Aumentando questo valore, è possibile aumentare le dimensioni dell'albero e ottenere una maggiore precisione, con il rischio di overfitting e tempi di training più lunghi.
  
4.  Per il **numero minimo di campioni per nodo foglia**, indicare il numero di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.  
  
     Aumentando questo valore, si aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito 1, anche un singolo caso può causare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque case che soddisfano le stesse condizioni.
  
5.  Per la **velocità di apprendimento**, digitare un numero compreso tra 0 e 1 che definisce le dimensioni del passaggio durante l'apprendimento.  
  
     La velocità di apprendimento determina la velocità o la lentezza della convergenza degli studenti sulla soluzione ottimale. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training impiega più tempo per convergere sulla soluzione migliore.
  
6.  Per il **numero di alberi costruiti**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training aumenterà.
  
     Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, impostare il valore su 1. Tuttavia, quando si esegue questa operazione, viene prodotto un solo albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.
  
7.  Per il valore di **inizializzazione numerico casuale**, digitare facoltativamente un numero intero non negativo da usare come valore di inizializzazione casuale. La specifica di un valore di inizializzazione garantisce la riproducibilità tra esecuzioni con gli stessi dati e parametri.  
  
     Per impostazione predefinita, il valore di inizializzazione casuale viene impostato su 0, il che significa che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema.  Le esecuzioni successive con un valore di inizializzazione casuale possono avere risultati diversi.
  

9. Eseguire il training del modello.
  
    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](./train-model.md) .  
  
   
## <a name="results"></a>Risultati

Al termine del training del modello, fare clic con il pulsante destro del mouse sull'output di [Train Model](./train-model.md) per visualizzare i risultati:

+ Per visualizzare l'albero creato in ogni iterazione, selezionare **Visualizza**. 
+ Per eseguire il drill-down nelle divisioni e vedere le regole per ogni nodo, fare clic su ogni albero.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 