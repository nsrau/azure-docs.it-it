---
title: "Regressione dell'albero delle decisioni potenziato: riferimento al modulo"
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Boosted Decision Tree Regression in Azure Machine Learning per creare un insieme di alberi di regressione usando Boosting.Learn how to use the Boosted Decision Tree Regression module in Azure Machine Learning to create an ensemble of regression trees using boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456727"
---
# <a name="boosted-decision-tree-regression-module"></a>Modulo di regressione dell'albero delle decisioni potenziato

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per creare un insieme di alberi di regressione usando boosting. *Aumentare* significa che ogni albero dipende da alberi precedenti. L'algoritmo apprende adattando il residuo degli alberi che lo hanno preceduto. Il boosting in un insieme di alberi delle decisioni tende quindi a migliorare la precisione, anche se con un rischio minimo di minore copertura.  
  
Questo metodo di regressione è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con etichetta.* La colonna label deve contenere valori numerici.  

> [!NOTE]
> Usare questo modulo solo con set di dati che usano variabili numeriche.  

Dopo aver definito il modello, esetrarlo utilizzando il [modello di training](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Ulteriori informazioni sugli alberi di regressione potenziati  

Il boosting è uno dei metodi classici per la creazione di modelli d'insieme, insieme al bagging, alle foreste casuali e così via.  In Azure Machine Learning, gli alberi decisionali potenziati usano un'implementazione efficiente dell'algoritmo di incremento del gradiente MART. L'incremento dei gradienti è una tecnica di apprendimento automatico per i problemi di regressione. Permette di creare ogni albero di regressione eseguendo alcuni passaggi e usando una funzione di perdita predefinita per misurare l'errore in ogni passaggio, in modo da applicare una correzione nel passaggio successivo. Il modello di previsione è quindi effettivamente un insieme di modelli di previsione più deboli.  
  
Nei problemi di regressione, l'amplificazione crea una serie di alberi in modo graduale e quindi seleziona l'albero ottimale utilizzando una funzione di perdita differenziabile arbitraria.  
  
Per altre informazioni, vedere i seguenti articoli:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Questo articolo di Wikipedia sull'amplificazione del gradiente fornisce un po ' di background sugli alberi potenziati. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: da RankNet a LambdaRank a LambdaMART: una panoramica. Di J.C. Burges.

Il metodo di boosting dei gradienti può essere usato anche per problemi di classificazione, tramite la riduzione a regressione con una funzione di perdita idonea. Per ulteriori informazioni sull'implementazione degli alberi potenziati per le attività di classificazione, vedere [Albero decisionale potenziato a due](./two-class-boosted-decision-tree.md)classi .  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Come configurare la regressione dell'albero delle decisioni potenziatoHow to configure Boosted Decision Tree Regression

1.  Aggiungere il modulo **Albero decisionale potenziato** alla pipeline. Questo modulo è disponibili in **Machine Learning**, **Initialize**, nella categoria **Regressione.** 
  
2.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: Selezionare questa opzione se si conosce la configurazione del modello e si fornisce un set specifico di valori come argomenti. 
     
    -   **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.    
   
  
3. **Numero massimo di foglie per albero**: Indica il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.  

    Aumentando questo valore, potenzialmente si aumentano le dimensioni dell'albero e si ottiene una maggiore precisione. Possono tuttavia verificarsi casi di overfitting e tempi di training più lunghi.  

4. **Numero minimo di campioni per nodo foglia**: Indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.

    Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno 5 casi che soddisfano le stesse condizioni.

5. **Tasso di**apprendimento : Digitare un numero compreso tra 0 e 1 che definisce la dimensione del passo durante l'apprendimento. La velocità di apprendimento determina la velocità o la lentezza con cui lo studente converge sulla soluzione ottimale. Se la dimensione del passo è troppo grande, si potrebbe superare la soluzione ottimale. Se la dimensione del passo è troppo piccola, la formazione richiede più tempo per convergere sulla soluzione migliore.

6. **Numero di alberi costruiti**: Indicare il numero totale di alberi decisionali da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una copertura migliore, ma il tempo di allenamento aumenta.

    Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; tuttavia, viene prodotto un solo albero (l'albero con il set iniziale di parametri) e non vengono eseguite ulteriori iterazioni.

7. **Valore di serie numerico casuale**: digitare un numero intero non negativo facoltativo da utilizzare come valore di serie casuale. La specifica di un valore di serie garantisce la riproducibilità tra le esecuzioni con gli stessi dati e parametri.

    Per impostazione predefinita, il valore di inizializzazione casuale è impostato su 0, il che significa che il valore di inizializzazione iniziale viene ottenuto dall'orologio di sistema.
  

9. Aggiungere un set di dati di training e uno dei moduli di training:Add a training dataset, and one of the training modules:

    - Se si imposta **l'opzione Crea modalità trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](train-model.md)  
  
    

10. Inviare la pipeline.  
  
## <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per usare il modello per il punteggio, collegarlo al modello di [punteggio](./score-model.md), per stimare i valori per i nuovi esempi di input.

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Output** nel riquadro destro di **Modello addestrato** e fare clic sull'icona **Registra set di dati.** La copia del modello sottoposto a training verrà salvata come modulo nell'albero del modulo e non verrà aggiornata nelle esecuzioni successive della pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
