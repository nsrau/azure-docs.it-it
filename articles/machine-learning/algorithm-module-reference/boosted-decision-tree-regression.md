---
title: 'Regressione di albero delle decisioni con Boosting: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Boosted Decision Tree Regression nel servizio Azure Machine Learning per creare un insieme di alberi di regressione con boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b4ebf1740ec2b0288d8052cb075a61b720b031a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028336"
---
# <a name="boosted-decision-tree-regression-module"></a>Modulo di regressione di albero delle decisioni con Boosting

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un insieme di alberi di regressione con boosting. *Boosting* significa che ogni albero dipende alberi precedenti. L'algoritmo di apprendimento mediante l'adattamento delle sezioni residue degli alberi che lo precedesse. Di conseguenza, il boosting in un insieme di alberi delle decisioni tende a migliorare l'accuratezza con un rischio minimo di minore copertura.  
  
Questo metodo di regressione è un metodo di apprendimento supervisionato e pertanto richiede un *etichettato dataset*. La colonna di etichetta deve contenere valori numerici.  

> [!NOTE]
> Usare questo modulo solo con i set di dati che usano variabili numeriche.  

Dopo aver definito il modello, eseguire il training usando il [Train Model](./train-model.md).

> [!TIP]
> Vuoi saperne di più sugli alberi creati? Dopo il training del modello, fare doppio clic sull'output del [Train Model](./train-model.md) modulo e selezionare **Visualize** per visualizzare la struttura ad albero che è stata creata in ogni iterazione. È possibile approfondire le divisioni per ogni albero e visualizzare le regole per ogni nodo.  
  
## <a name="more-about-boosted-regression-trees"></a>Informazioni sugli alberi di regressione con Boosting  

Il Boosting è uno dei metodi classici per la creazione di modelli di insieme, insieme al bagging, foreste casuali e così via.  In Azure Machine Learning, gli alberi delle decisioni con Boosting usano un'implementazione efficiente dell'algoritmo di Boosting dei gradienti MART. Boosting a gradienti è una tecnica di machine learning per i problemi di regressione. Viene compilata ogni albero di regressione in modo graduale, usando una funzione di perdita predefinita per misurare l'errore in ogni passaggio e lo corregge per, entro i prossimi. In questo modo il modello di stima è effettivamente un insieme di modelli di previsione più deboli.  
  
I problemi di regressione boosting crea una serie di strutture ad albero in modo graduale e quindi seleziona l'albero ottimale usando una funzione di perdita arbitraria differenziabile.  
  
Per altre informazioni, vedere questi articoli:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Questo articolo di Wikipedia sul Boosting a gradienti fornisce alcune informazioni generali su alberi con Boosting. 
  
-  [http://research.microsoft.com/apps/pubs/default.aspx?id=132652](http://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Panoramica da RankNet a LambdaRank e: Panoramica. Da J.C. Burges.

La sfumatura di metodo di Boosting dei nonché per problemi di classificazione tramite la riduzione a regressione con una funzione di perdita idonea. Per altre informazioni sull'implementazione alberi con Boosting per attività di classificazione, vedere [Two-Class Boosted Decision Tree](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak nakonfigurovat Boosted Decision Tree Regression

1.  Aggiungere il **Boosted Decision Tree** modulo nell'esperimento. È possibile trovare questo modulo sotto **Machine Learning**, **inizializzare**, sotto il **regressione** categoria. 
  
2.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Selezionare questa opzione se si sa come si desidera configurare il modello e fornire un set specifico di valori come argomenti.  
   
  
3. **Numero massimo di foglie per ogni albero**: Indicare il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.  

    Se si aumenta questo valore, potenzialmente aumentare le dimensioni dell'albero e ottenere una maggiore precisione, con il rischio di overfitting e tempi di training è più.  

4. **Numero minimo di esempi per nodo foglia**: Indicare il numero minimo di casi necessari per creare un nodo terminale (foglia) in una struttura ad albero.

    Se si aumenta questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito pari a 1, anche un singolo caso può determinare una nuova regola da creare. Se si aumenta il valore su 5, i dati di training devono contenere almeno 5 casi che soddisfano le stesse condizioni.

5. **Velocità di apprendimento**: Digitare un numero compreso tra 0 e 1 che definisce la dimensione di incremento durante l'apprendimento. La velocità di apprendimento determina la modalità veloce o lento lo strumento di apprendimento converge su una soluzione ottimale. Se la dimensione di incremento è troppo grande, si può eccedere la soluzione ottimale. Se la dimensione di incremento è troppo piccola, corsi di formazione richiede più tempo per la convergenza sulla migliore soluzione.

6. **Numero di alberi costruito**: Indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training aumenta.

    Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello con training. Se si desidera visualizzare o stampare una struttura ad albero Single, è possibile impostare il valore su 1. Tuttavia, viene generato un solo albero (l'albero con il set iniziale di parametri) e non ci sono altre iterazioni vengono eseguite.

7. **Valore di inizializzazione di numeri casuali**: Digitare un numero intero non negativo facoltativo da utilizzare come valore di inizializzazione casuale. Specificare un valore di inizializzazione assicura la riproducibilità nelle esecuzioni che hanno gli stessi dati e parametri.

    Per impostazione predefinita, il valore di inizializzazione casuale è impostata su 0, ovvero che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema.
  
8. **Consenti livelli di categoria sconosciuti**: Selezionare questa opzione per creare un gruppo per i valori sconosciuti in set di training e convalida. Se si deseleziona questa opzione, il modello può accettare solo i valori contenuti nei dati di training. Il modello potrebbe essere meno preciso sui valori noti, ma può fornire stime migliori per i valori nuovi (sconosciuti).

9. Aggiungere un set di dati di training e uno dei moduli di formazione:

    - Se si imposta **modalità di creazione trainer** possibilità **singolo parametro**, usare il [Train Model](train-model.md) modulo.  
  
    

10. Eseguire l'esperimento.  
  
### <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare la struttura ad albero che è stata creata in ogni iterazione, fare doppio clic sull'output del [Train Model](train-model.md) modulo e selezionare **Visualize**.
  
     Fare clic su ogni albero per il drill down le divisioni e visualizzare le regole per ogni nodo.  

+ Per usare il modello per il punteggio, connetterlo al [Score Model](./score-model.md), per stimare i valori per i nuovi esempi di input.

+ Per salvare uno snapshot del modello con training, fare doppio clic il **Trained model** output del modulo di formazione e selezionare **Salva con nome**. La copia del modello con training che si salva, non viene aggiornata a ogni esecuzione dell'esperimento.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 