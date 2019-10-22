---
title: "Regressione dell'albero delle decisioni con boosting: riferimento al modulo"
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di regressione dell'albero delle decisioni con boosting nel servizio Azure Machine Learning per creare un insieme di alberi di regressione con boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 4b8ff1c9f5b0f0b04448b950d3ba904ba76927c9
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693843"
---
# <a name="boosted-decision-tree-regression-module"></a>Modulo di regressione dell'albero delle decisioni con boosting

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un insieme di alberi di regressione con boosting. L' *incremento* significa che ogni albero dipende da alberi precedenti. L'algoritmo apprende inserendo il residuo degli alberi che lo precedono. Il potenziamento in un insieme di alberi delle decisioni tende quindi a migliorare l'accuratezza con un piccolo rischio di minore copertura.  
  
Questo metodo di regressione è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con etichetta*. La colonna Label deve contenere valori numerici.  

> [!NOTE]
> Usare questo modulo solo con i set di impostazioni che usano variabili numeriche.  

Dopo aver definito il modello, eseguirne il training usando il [modello di training](./train-model.md).

> [!TIP]
> Per ulteriori informazioni sugli alberi creati, Dopo aver eseguito il training del modello, fare clic con il pulsante destro del mouse sull'output del modulo [Train Model](./train-model.md) e selezionare **Visualize (Visualizza** ) per visualizzare l'albero che è stato creato a ogni iterazione. È possibile eseguire il drill-down nelle divisioni per ogni albero e visualizzare le regole per ogni nodo.  
  
## <a name="more-about-boosted-regression-trees"></a>Altre informazioni sugli alberi di regressione con boosting  

Il potenziamento è uno dei diversi metodi classici per la creazione di modelli di Ensemble, insieme a un sacco, a foreste casuali e così via.  In Azure Machine Learning, gli alberi delle decisioni con boosting usano un'implementazione efficiente dell'algoritmo di boosting dei gradienti MART. L'incremento delle sfumature è una tecnica di apprendimento automatico per i problemi di regressione. Ogni albero di regressione viene compilato in modo graduale, usando una funzione di perdita predefinita per misurare l'errore in ogni passaggio e correggerlo nella prossima. In questo modo il modello di stima è in realtà un insieme di modelli di stima più vulnerabili.  
  
Nei problemi di regressione, l'aumento delle prestazioni crea una serie di alberi in modo graduale, quindi seleziona l'albero ottimale usando una funzione di perdita differenziale arbitraria.  
  
Per ulteriori informazioni, vedere i seguenti articoli:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Questo articolo di Wikipedia sull'incremento delle sfumature fornisce alcuni cenni preliminari sugli alberi con boosting. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: da RankNet a LambdaRank a LambdaMART: panoramica. Di J.C. Burges.

Il metodo di boosting a gradienti può essere usato anche per i problemi di classificazione riducendo la regressione con una funzione di perdita adatta. Per altre informazioni sull'implementazione degli alberi con boosting per le attività di classificazione, vedere [albero delle decisioni con boosting a due classi](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Come configurare la regressione dell'albero delle decisioni con boosting

1.  Aggiungere il modulo di **albero delle decisioni con boosting** alla pipeline. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **regressione** . 
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: selezionare questa opzione se si sa come si desidera configurare il modello e specificare un set di valori specifico come argomenti.  
   
  
3. **Numero massimo di foglie per albero**: indica il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.  

    Aumentando questo valore, è possibile aumentare le dimensioni dell'albero e ottenere una maggiore precisione, con il rischio di overfitting e tempi di training più lunghi.  

4. **Numero minimo di campioni per nodo foglia**: indica il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.

    Aumentando questo valore, si aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito 1, anche un singolo caso può causare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno 5 case che soddisfano le stesse condizioni.

5. **Velocità di apprendimento**: digitare un numero compreso tra 0 e 1 che definisce le dimensioni del passaggio durante l'apprendimento. La velocità di apprendimento determina la velocità o la lentezza della convergenza degli studenti sulla soluzione ottimale. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training impiega più tempo per convergere sulla soluzione migliore.

6. **Numero di alberi costruiti**: indica il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma aumenta il tempo di training.

    Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Tuttavia, viene prodotto un solo albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.

7. Valore di **inizializzazione numero casuale**: digitare un numero intero non negativo facoltativo da utilizzare come valore di inizializzazione casuale. La specifica di un valore di inizializzazione garantisce la riproducibilità tra esecuzioni con gli stessi dati e parametri.

    Per impostazione predefinita, il valore di inizializzazione casuale è impostato su 0, il che significa che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema.
  
8. **Consenti livelli categorici sconosciuti**: selezionare questa opzione per creare un gruppo per i valori sconosciuti nei set di training e di convalida. Se si deseleziona questa opzione, il modello può accettare solo i valori contenuti nei dati di training. Il modello può essere meno preciso per i valori noti, ma può fornire stime migliori per i valori nuovi (sconosciuti).

9. Aggiungere un set di dati di training e uno dei moduli di training:

    - Se si imposta l'opzione **create Trainer Mode** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .  
  
    

10. Eseguire la pipeline.  
  
## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare l'albero che è stato creato a ogni iterazione, fare clic con il pulsante destro del mouse sull'output del modulo [Train Model](train-model.md) e scegliere **Visualize**.
  
     Fare clic su ogni albero per eseguire il drill-down nelle divisioni e visualizzare le regole per ogni nodo.  

+ Per utilizzare il modello per il punteggio, connetterlo al [modello di Punteggio](./score-model.md)per stimare i valori per i nuovi esempi di input.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
