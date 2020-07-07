---
title: 'Foresta delle decisioni multiclasse: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo multiCLASS Decision Forest in Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo della *foresta delle decisioni* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9e8798ea1c03ebf4c0d08adfbf5a0ee0755164a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82137808"
---
# <a name="multiclass-decision-forest-module"></a>Modulo della foresta delle decisioni multiclasse

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo della *foresta delle decisioni* . Una foresta delle decisioni è un modello di ensemble che consente di compilare rapidamente una serie di alberi delle decisioni, imparando dai dati con tag.

## <a name="more-about-decision-forests"></a>Altre informazioni sulle foreste delle decisioni

L'algoritmo di foresta delle decisioni è un metodo di apprendimento dell'insieme per la classificazione. L'algoritmo funziona compilando più alberi delle decisioni e quindi *votando* la classe di output più diffusa. Il voto è una forma di aggregazione, in cui ogni albero in una foresta delle decisioni di classificazione restituisce un istogramma non normalizzato di etichette. Il processo di aggregazione Somma questi istogrammi e normalizza il risultato per ottenere le "probabilità" per ciascuna etichetta. Gli alberi con attendibilità di stima elevata hanno un peso maggiore nella decisione finale dell'insieme.

Gli alberi delle decisioni in generale sono modelli non parametrici, ovvero supportano i dati con diverse distribuzioni. In ogni albero viene eseguita una sequenza di semplici test per ogni classe, aumentando i livelli di una struttura ad albero, finché viene raggiunto un nodo foglia (decisione).

Gli alberi delle decisioni offrono molti vantaggi:

+ Possono rappresentare limiti di decisione non lineari.
+ Sono efficienti sia nel calcolo che nell'uso della memoria durante il training e la stima.
+ Eseguono la selezione e la classificazione integrata di funzionalità
+ e sono resilienti in caso di funzionalità rumorose.

Il classificatore della foresta delle decisioni in Azure Machine Learning è costituito da un insieme di alberi delle decisioni. In genere, i modelli di insieme offrono una copertura e un'accuratezza migliori rispetto a singoli alberi delle decisioni. Per altre informazioni, vedere [Decision Trees](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Come configurare una foresta delle decisioni multiclasse

1. Aggiungere il modulo **multiCLASS Decision Forest** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning**, **inizializzare il modello**e la **classificazione**.

2. Fare doppio clic sul modulo per aprire il riquadro **Proprietà** .

3. Per il **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere il bagging o la replica.

    + **Insaccamento**: l'insaccamento viene chiamato anche *aggregazione bootstrap*. In questo metodo ogni albero viene ampliato in un nuovo esempio, creato eseguendo il campionamento casuale del set di dati originale con sostituzione fino a quando non si dispone di un set di dati di dimensioni originali. Gli output dei modelli vengono combinati *votando*, ovvero una forma di aggregazione. Per ulteriori informazioni, vedere la voce Wikipedia per l'aggregazione bootstrap.

    + **Replicate**: nella replica ogni albero viene sottoposto a training esattamente sugli stessi dati di input. La determinazione del predicato Split utilizzato per ogni nodo della struttura ad albero resta casuale e crea alberi diversi.

   

4. Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .

    + **Singolo parametro**: selezionare questa opzione se si conosce la modalità di configurazione del modello e si specifica un set di valori come argomenti.

    + **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.   

5. **Numero di alberi delle decisioni**: digitare il numero massimo di alberi delle decisioni che è possibile creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training potrebbe aumentare.

    Questo valore controlla anche il numero di alberi visualizzati nei risultati, quando si Visualizza il modello sottoposto a training. Per visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Ciò significa tuttavia che è possibile produrre solo un albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.

6. **Profondità massima degli alberi delle decisioni**: digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio però di overfitting e di aumento dei tempi di training.

7. **Numero di divisioni casuali per nodo**: digitare il numero di divisioni da usare durante la compilazione di ogni nodo dell'albero. Una *suddivisione* indica che le funzionalità di ogni livello dell'albero (nodo) sono divise in modo casuale.

8. **Numero minimo di campioni per nodo foglia**: indica il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero. Aumentando questo valore, aumenta la soglia per la creazione di nuove regole.

    Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.



10. Connettere un set di dati con etichetta ed eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.

11. Inviare la pipeline.



## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 