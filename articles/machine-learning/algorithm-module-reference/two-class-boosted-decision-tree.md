---
title: 'Albero delle decisioni con Boosting a due classi: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class Boosted Decision Tree nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo di alberi delle decisioni con Boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027946"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modulo Two-Class Boosted Decision Tree

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo di alberi delle decisioni con Boosting. 

Un albero delle decisioni con Boosting è un insieme in cui la struttura ad albero secondo corregge gli errori dell'albero, prima del metodo di apprendimento dell'albero terzo lo corregge per gli errori del alberi primi e la secondo e così via.  Le stime sono basate sull'intero insieme di alberi insieme che esegue la stima.
  
In genere, quando configurati correttamente, gli alberi delle decisioni con Boosting rappresentano i metodi più semplici con cui si desidera ottenere prestazioni ottimali su una vasta gamma di attività di machine learning. Tuttavia, sono anche tra gli strumenti di apprendimento di più intensivo della memoria e l'implementazione corrente impegna completamente la memoria. Pertanto, un modello di albero delle decisioni con Boosting potrebbe non essere in grado di elaborare grandi set di dati che può gestire alcuni strumenti di apprendimento lineare.

## <a name="how-to-configure"></a>Come configurare

Questo modulo consente di creare un modello di classificazione senza training. Poiché la classificazione è un metodo di apprendimento supervisionato, per il training del modello, è necessario un *set di dati con tag* che include una colonna di etichetta con un valore per tutte le righe.

È possibile eseguire il training di questo tipo di modello utilizzando [Train Model](././train-model.md). 

1.  In Azure Machine Learning, aggiungere il **Boosted Decision Tree** modulo nell'esperimento.
  
2.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.
  
    + **Singolo parametro**: Se si conosce la modalità con cui si desidera configurare il modello, è possibile definire un set specifico di valori come argomenti.
  
  
3.  Per la **numero massimo di foglie per ogni albero**, indicare il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.
  
     Se si aumenta questo valore, potenzialmente aumentare le dimensioni dell'albero e ottenere una maggiore precisione, con il rischio di overfitting e tempi di training è più.
  
4.  Per la **numero minimo di campioni per ogni nodo foglia**, indicare il numero di casi necessari per creare un nodo terminale (foglia) in una struttura ad albero.  
  
     Se si aumenta questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito pari a 1, anche un singolo caso può determinare una nuova regola da creare. Se si aumenta il valore su 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.
  
5.  Per la **velocità di apprendimento**, digitare un numero compreso tra 0 e 1 che definisce la dimensione di incremento durante l'apprendimento.  
  
     La velocità di apprendimento determina la modalità veloce o lento lo strumento di apprendimento converge su una soluzione ottimale. Se la dimensione di incremento è troppo grande, si può eccedere la soluzione ottimale. Se la dimensione di incremento è troppo piccola, corsi di formazione richiede più tempo per la convergenza sulla migliore soluzione.
  
6.  Per la **numero di alberi costruito**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma sarà necessario più tempo di training.
  
     Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello con training. Se si desidera visualizzare o stampare un singolo albero, impostare il valore su 1. Tuttavia, quando si esegue questa operazione, viene generato un solo albero (l'albero con il set iniziale di parametri) e non ci sono altre iterazioni vengono eseguite.
  
7.  Per **seed numeri Random**, facoltativamente, digitare un numero intero non negativo da utilizzare come valore di inizializzazione casuale. Specificare un valore di inizializzazione assicura la riproducibilità nelle esecuzioni che hanno gli stessi dati e parametri.  
  
     Il valore di inizializzazione casuale è impostato per impostazione predefinita su 0, ovvero che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema.  Le esecuzioni successive utilizzando un valore di inizializzazione casuale possono produrre risultati diversi.
  

9. Il training del modello.
  
    + Se si imposta **modalità di creazione trainer** al **singolo parametro**, connettere un set di dati con tag e il [Train Model](./train-model.md) modulo.  
  
   
## <a name="results"></a>Risultati

Al termine training del modello, fare doppio clic sull'output del [Train Model](./train-model.md) per visualizzare i risultati:

+ Per visualizzare la struttura ad albero che è stata creata in ogni iterazione, selezionare **Visualize**. 
+ Il drill down le divisioni e visualizzare le regole per ogni nodo, fare clic su ogni albero.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 