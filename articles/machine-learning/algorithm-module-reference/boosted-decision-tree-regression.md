---
title: "Regressione dell'albero delle decisioni con boosting: riferimento al modulo"
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione dell'albero delle decisioni con boosting in Azure Machine Learning per creare un insieme di alberi di regressione con boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 6b72d80f0be65af22bf334752052757346457a67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84448632"
---
# <a name="boosted-decision-tree-regression-module"></a>Modulo di regressione dell'albero delle decisioni con boosting

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un insieme di alberi di regressione con boosting. L' *incremento* significa che ogni albero dipende da alberi precedenti. L'algoritmo apprende inserendo il residuo degli alberi che lo precedono. Il boosting in un insieme di alberi delle decisioni tende quindi a migliorare la precisione, anche se con un rischio minimo di minore copertura.  
  
Questo metodo di regressione è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con etichetta*. La colonna Label deve contenere valori numerici.  

> [!NOTE]
> Usare questo modulo solo con set di dati che usano variabili numeriche.  

Dopo aver definito il modello, eseguirne il training usando il [modello di training](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Altre informazioni sugli alberi di regressione con boosting  

Il boosting è uno dei metodi classici per la creazione di modelli d'insieme, insieme al bagging, alle foreste casuali e così via.  In Azure Machine Learning, gli alberi delle decisioni con boosting usano un'implementazione efficiente dell'algoritmo di boosting dei gradienti MART. L'incremento dei gradienti è una tecnica di apprendimento automatico per i problemi di regressione. Permette di creare ogni albero di regressione eseguendo alcuni passaggi e usando una funzione di perdita predefinita per misurare l'errore in ogni passaggio, in modo da applicare una correzione nel passaggio successivo. Il modello di previsione è quindi effettivamente un insieme di modelli di previsione più deboli.  
  
Nei problemi di regressione, l'aumento delle prestazioni crea una serie di alberi in modo graduale, quindi seleziona l'albero ottimale usando una funzione di perdita differenziale arbitraria.  
  
Per altre informazioni, vedere i seguenti articoli:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Questo articolo di Wikipedia sull'incremento delle sfumature fornisce alcuni cenni preliminari sugli alberi con boosting. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: da RankNet a LambdaRank a LambdaMART: panoramica. Di J.C. Burges.

Il metodo di boosting dei gradienti può essere usato anche per problemi di classificazione, tramite la riduzione a regressione con una funzione di perdita idonea. Per altre informazioni sull'implementazione degli alberi con boosting per le attività di classificazione, vedere [albero delle decisioni con boosting a due classi](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Come configurare la regressione dell'albero delle decisioni con boosting

1.  Aggiungere il modulo di **albero delle decisioni con boosting** alla pipeline. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **regressione** . 
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: selezionare questa opzione se si sa come si desidera configurare il modello e specificare un set di valori specifico come argomenti. 
     
    -   **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.    
   
  
3. **Numero massimo di foglie per albero**: indica il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.  

    Aumentando questo valore, potenzialmente si aumentano le dimensioni dell'albero e si ottiene una maggiore precisione. Possono tuttavia verificarsi casi di overfitting e tempi di training più lunghi.  

4. **Numero minimo di campioni per nodo foglia**: indica il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.

    Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno 5 casi che soddisfano le stesse condizioni.

5. **Velocità di apprendimento**: digitare un numero compreso tra 0 e 1 che definisce le dimensioni del passaggio durante l'apprendimento. La velocità di apprendimento determina la velocità o la lentezza della convergenza degli studenti sulla soluzione ottimale. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training impiega più tempo per convergere sulla soluzione migliore.

6. **Numero di alberi costruiti**: indica il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma aumenta il tempo di training.

    Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Tuttavia, viene prodotto un solo albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.

7. Valore di **inizializzazione numero casuale**: digitare un numero intero non negativo facoltativo da utilizzare come valore di inizializzazione casuale. La specifica di un valore di inizializzazione garantisce la riproducibilità tra esecuzioni con gli stessi dati e parametri.

    Per impostazione predefinita, il valore di inizializzazione casuale è impostato su 0, il che significa che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema.
  

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
    

10. Inviare la pipeline.  
  
## <a name="results"></a>Risultati

Al termine del training:

+ Per usare il modello per il punteggio, connettere [Train Model](train-model.md) a [Score Model](./score-model.md)per stimare i valori per i nuovi esempi di input.

+ Per salvare uno snapshot del modello sottoposto a training, selezionare scheda **output** nel riquadro destro del **modello** sottoposto a training e fare clic sull'icona **registra set di dati** . La copia del modello sottoposto a training verrà salvata come modulo nell'albero del modulo e non verrà aggiornata sulle esecuzioni successive della pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
