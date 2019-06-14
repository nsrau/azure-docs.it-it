---
title: 'Multiclass Decision Forest: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Multiclass Decision Forest nel servizio Azure Machine Learning per creare un modello di machine learning in base il *Decision Trees foresta* algoritmo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 10364d014431a500e7c38a02d47f432cd464feb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411461"
---
# <a name="multiclass-decision-forest-module"></a>Modulo multiclass Decision Forest

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning in base il *Decision Trees foresta* algoritmo. Una foresta delle decisioni è un modello di insieme che consente di creare rapidamente una serie di alberi delle decisioni, durante l'apprendimento dai dati con tag.

## <a name="more-about-decision-forests"></a>Informazioni sulle foreste delle decisioni

L'algoritmo di foresta delle decisioni è un metodo per la classificazione di apprendimento dell'insieme. L'algoritmo funziona creando più alberi delle decisioni e quindi *voto* della classe di output più comune. È possibile votare una maschera di aggregazione, in cui ciascuna struttura di una decisione relativa alla classificazione dell'insieme di strutture genera un istogramma non normalizzato frequenza delle etichette. Il processo di aggregazione Somma questi istogrammi e normalizza il risultato per ottenere la "probabilità" per ogni etichetta. Gli alberi dotati di attendibilità elevata hanno un peso maggiore decisione finale dell'insieme.

In generale, gli alberi delle decisioni sono modelli non parametrici, vale a dire che supportano dati con diverse distribuzioni. In ogni albero, viene eseguita una sequenza di semplici test per ogni classe, aumentando i livelli di una struttura ad albero fino al raggiungimento di un nodo foglia (decisione).

Gli alberi delle decisioni offrono molti vantaggi:

+ Possono rappresentare limiti di decisione non lineari.
+ Sono efficienti in calcolo e di utilizzo della memoria durante il training e stima.
+ Eseguono selezione caratteristica integrata e la classificazione.
+ Sono resilienti in caso di funzionalità rumorose.

Il classificatore delle foreste delle decisioni in Azure Machine Learning è costituito da un insieme di alberi delle decisioni. In generale, i modelli di insieme offrono una copertura e accuratezza rispetto a singoli alberi delle decisioni migliori. Per altre informazioni, vedere [algoritmo Microsoft Decision Trees](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak nakonfigurovat Multiclass Decision Forest



1. Aggiungere il **Multiclass Decision Forest** modulo all'esperimento nell'interfaccia. È possibile trovare questo modulo sotto **Machine Learning**, **modello di inizializzazione**, e **classificazione**.

2. Fare doppio clic sul modulo per aprire la **proprietà** riquadro.

3. Per la **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra bagging o la replica.

    + **Bagging**: Viene anche chiamato bagging *l'aggregazione di bootstrap*. In questo metodo, ogni albero è cresciuto in un nuovo esempio, creato tramite il campionamento in modo casuale il set di dati originale con sostituzione fino a quando non si dispone di un set di dati le dimensioni dell'originale. Gli output dei modelli vengono combinati da *voto*, ovvero una maschera di aggregazione. Per altre informazioni, vedere la pagina di Wikipedia per l'aggregazione di Bootstrap.

    + **Replicare**: Nella replica di ogni albero viene eseguito il training su esattamente gli stessi dati di input. La determinazione di quali split predicato è utilizzato per ciascun nodo dell'albero rimane casuale, creazione di alberi diversi.

   

4. Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.

    + **Singolo parametro**: Selezionare questa opzione se si sa come si desidera configurare il modello e fornire un set di valori come argomenti.


5. **Numero di alberi delle decisioni**: Digitare il numero massimo di alberi delle decisioni che possono essere creati nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma potrebbero aumentare i tempi di training.

    Questo valore controlla anche il numero di alberi visualizzati nei risultati, fare riferimento alla terminologia modello con training. Per visualizzare o stampare un singolo albero, è possibile impostare il valore su 1. Tuttavia, ciò significa che un solo albero può essere generata (l'albero con il set iniziale di parametri) e non ci sono altre iterazioni vengono eseguite.

6. **Profondità massima degli alberi delle decisioni**: Digitare un numero per limitare la profondità massima degli alberi delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio di overfitting e una maggiore tempi di training.

7. **Numero di divisioni casuali per nodo**: Digitare il numero di suddivisioni da usare durante la compilazione di ogni nodo dell'albero. Oggetto *dividere* significa che le funzionalità disponibili in ogni livello dell'albero (nodo) venga divise in modo casuale.

8. **Numero minimo di esempi per nodo foglia**: Indicare il numero minimo di casi necessari per creare un nodo terminale (foglia) in una struttura ad albero. Se si aumenta questo valore, aumenta la soglia per la creazione di nuove regole.

    Ad esempio, con il valore predefinito pari a 1, anche un singolo caso può determinare una nuova regola da creare. Se si aumenta il valore su 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.



10. Connettere un set di dati con etichette e uno dei moduli di formazione:

    + Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](./train-model.md) modulo.

11. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare la struttura ad albero che è stata creata in ogni iterazione, fare doppio clic sull'output del [Train Model](./train-model.md) modulo e selezionare **Visualize**.
+ Per visualizzare le regole per ogni nodo, fare clic su ciascuna struttura di drill-down le divisioni per.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 