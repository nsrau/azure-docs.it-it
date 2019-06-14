---
title: 'Two-Class Decision Forest: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class Decision Forest nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo delle foreste delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 73b7822c56e2b07eeefdedce1bce6d410d110ebc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411495"
---
# <a name="two-class-decision-forest-module"></a>Modulo Two-Class Decision Forest

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo delle foreste delle decisioni.  

Le foreste delle decisioni sono modelli di insieme rapidi con supervisione. Questo modulo è un'ottima scelta se si desidera stimare una destinazione con un massimo di due risultati. 

## <a name="understanding-decision-forests"></a>Informazioni sulle foreste delle decisioni

Questo algoritmo di foresta delle decisioni è un metodo può essere utilizzata per attività di classificazione di apprendimento dell'insieme. Metodi di insieme sono basati sul principio generale che piuttosto che basarsi su un singolo modello, è possibile ottenere risultati migliori e un modello più generalizzato mediante la creazione di più modelli correlati e combinarle in qualche modo. In generale, i modelli di insieme offrono una copertura e accuratezza rispetto a singoli alberi delle decisioni migliori. 

Esistono diversi modi per creare singoli modelli e combinarli in un insieme. Questo particolare implementazione di una foresta delle decisioni funziona creando più alberi delle decisioni e quindi **voto** della classe di output più comune. Voto è uno dei metodi better-known per la generazione di risultati in un modello di insieme. 

+ Vengono creati molti alberi di classificazione singoli, utilizzando l'intero set di dati, ma diverso (in genere casuale) punti di partenza. Questo comportamento è diverso dall'approccio foresta casuale, in cui gli alberi delle decisioni singoli usino solo una parte casuale dei dati o delle funzionalità.
+ Ogni albero nell'albero di foresta delle decisioni genera un istogramma non normalizzato frequenza delle etichette. 
+ Il processo di aggregazione Somma questi istogrammi e normalizza il risultato per ottenere la "probabilità" per ogni etichetta. 
+ Gli alberi dotati di attendibilità delle previsioni elevato avranno un peso maggiore decisione finale dell'insieme.

In generale, gli alberi delle decisioni offrono molti vantaggi per le attività di classificazione:
  
- È possibile acquisire i limiti di decisione non lineari.
- È possibile eseguire il training e stima su un numero elevato di dati, così come sono efficiente nel caso di utilizzo di calcolo e memoria.
- Selezione delle caratteristiche è integrata nei processi di training e di classificazione.  
- Strutture ad albero può contenere dati non significativi e molte delle funzionalità.  
- Sono modelli non parametrici, vale a dire che possono gestire i dati con diverse distribuzioni. 

Tuttavia, gli alberi delle decisioni semplici possibile overfitting sui dati e sono meno generalizzabile rispetto a insiemi di struttura ad albero.

Per altre informazioni, vedere [foreste delle decisioni](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Come configurare
  
1.  Aggiungere il **Two-Class Decision Forest** modulo nell'esperimento in Azure Machine Learning e aprire il **proprietà** riquadro del modulo. 

    È possibile trovare il modulo sotto **Machine Learning**. Espandere **inizializzare**e quindi **classificazione**.  
  
2.  Per la **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra **Bagging** oppure **replicare**.  
  
    -   **Bagging**: Viene anche chiamato bagging *l'aggregazione di bootstrap*. In questo metodo, ogni albero è cresciuto in un nuovo esempio, creato tramite il campionamento in modo casuale il set di dati originale con sostituzione fino a quando non si dispone di un set di dati le dimensioni dell'originale.  
  
         Gli output dei modelli vengono combinati da *voto*, ovvero una maschera di aggregazione. Ogni albero in una foresta delle decisioni di classificazione genera un istogramma non normalizzato frequenza delle etichette. L'aggregazione è la somma di questi istogrammi e normalizzare per ottenere la "probabilità" per ogni etichetta. In questo modo, gli alberi dotati di attendibilità delle previsioni elevato avranno un peso maggiore decisione finale dell'insieme.  
  
         Per altre informazioni, vedere la pagina di Wikipedia per l'aggregazione di Bootstrap.  
  
    -   **Replicare**: Nella replica di ogni albero viene eseguito il training su esattamente gli stessi dati di input. La determinazione di quali split predicato è utilizzato per ciascun nodo dell'albero rimane casuali e alberi saranno diversi.   
  
3.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Se si conosce la modalità con cui si desidera configurare il modello, è possibile definire un set specifico di valori come argomenti.
  
4.  Per la **numero di alberi delle decisioni**, digitare il numero massimo di alberi delle decisioni che possono essere creati nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training aumenta.  
  
    > [!NOTE]
    >  Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello con training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1. Tuttavia, può essere solo un albero prodotta (l'albero con il set iniziale di parametri) e non ci sono altre iterazioni vengono eseguite.
  
5.  Per la **profondità massima degli alberi delle decisioni**, digitare un numero per limitare la profondità massima degli alberi delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio di overfitting e una maggiore tempi di training.
  
6.  Per la **numero di divisioni casuali per nodo**, digitare il numero di suddivisioni da usare durante la compilazione di ogni nodo dell'albero. Oggetto *dividere* significa che le funzionalità disponibili in ogni livello dell'albero (nodo) venga divise in modo casuale.
  
7.  Per la **numero minimo di campioni per ogni nodo foglia**, indicare il numero minimo di casi necessari per creare un nodo terminale (foglia) in una struttura ad albero.
  
     Se si aumenta questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito pari a 1, anche un singolo caso può determinare una nuova regola da creare. Se si aumenta il valore su 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.  
  
8.  Selezionare il **consentire i valori sconosciuti per funzionalità categoriche** opzione per creare un gruppo per i valori sconosciuti nei set di training o la convalida. Il modello potrebbe essere meno preciso sui valori noti, ma può fornire stime migliori per i valori nuovi (sconosciuti). 

     Se si deseleziona questa opzione, il modello può accettare solo i valori contenuti nei dati di training.
  
9. Collegare un set di dati con etichette e una delle [moduli di training](module-reference.md):  
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](./train-model.md) modulo.  
  
    
## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare la struttura ad albero che è stata creata in ogni iterazione, fare doppio clic sull'output del [Train Model](./train-model.md) modulo e selezionare **Visualize**.
  
    Fare clic su ogni albero per il drill down le divisioni e visualizzare le regole per ogni nodo.

+ Per salvare uno snapshot del modello, fare doppio clic il **modello con training** di output e selezionare **salvataggio modello**. Il modello salvato non viene aggiornato a ogni esecuzione dell'esperimento.

+ Per usare il modello per il punteggio, aggiungere il **Score Model** modulo in un esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 