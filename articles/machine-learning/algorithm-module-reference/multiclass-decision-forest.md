---
title: 'Foresta decisionale multiclasse: riferimento al moduloMulticlass Decision Forest: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Foresta decisionale multiclasse in Azure Machine Learning per creare un modello di Machine Learning basato sull'algoritmo della *foresta* decisionale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477545"
---
# <a name="multiclass-decision-forest-module"></a>Modulo Foresta decisionale multiclasse

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di apprendimento automatico basato sull'algoritmo della *foresta decisionale.* Una foresta decisionale è un modello di insieme che crea rapidamente una serie di alberi delle decisioni, mentre impara dai dati con tag.

## <a name="more-about-decision-forests"></a>Maggiori informazioni sulle foreste decisionali

L'algoritmo della foresta decisionale è un metodo di apprendimento dell'insieme per la classificazione. L'algoritmo funziona costruendo più alberi delle decisioni e quindi *votando* sulla classe di output più popolare. Il voto è una forma di aggregazione, in cui ogni albero in una foresta decisionale di classificazione restituisce un istogramma di frequenza non normalizzato di etichette. Il processo di aggregazione somma questi istogrammi e normalizza il risultato per ottenere le "probabilità" per ogni etichetta. Gli alberi che hanno un'elevata fiducia nella previsione hanno un peso maggiore nella decisione finale dell'ensemble.

Gli alberi delle decisioni in generale sono modelli non parametrici, il che significa che supportano dati con distribuzioni diverse. In ogni albero viene eseguita una sequenza di semplici test per ogni classe, aumentando i livelli di una struttura ad albero, finché viene raggiunto un nodo foglia (decisione).

Gli alberi delle decisioni offrono molti vantaggi:

+ Possono rappresentare limiti di decisione non lineari.
+ Sono efficienti sia nel calcolo che nell'uso della memoria durante il training e la stima.
+ Eseguono la selezione e la classificazione integrata di funzionalità
+ e sono resilienti in caso di funzionalità rumorose.

Il classificatore della foresta decisionale in Azure Machine Learning è costituito da un insieme di alberi delle decisioni. In genere, i modelli di insieme offrono una copertura e un'accuratezza migliori rispetto a singoli alberi delle decisioni. Per ulteriori informazioni, vedere [Alberi delle decisioni](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Come configurare la foresta decisionale multiclasseHow to configure Multiclass Decision Forest

1. Aggiungere il modulo **Foresta decisionale multiclasse** alla pipeline nella finestra di progettazione. Questo modulo è reperito in **Machine Learning**, **Inizializza modello**e **Classificazione**.

2. Fare doppio clic sul modulo per aprire il riquadro **Proprietà.**

3. In **Metodo di ricampionamento**, scegliere il metodo utilizzato per creare i singoli alberi.  È possibile scegliere il bagging o la replica.

    + **Bagging**: Bagging è anche chiamato *bootstrap aggregating*. In questo metodo, ogni albero viene coltivato in un nuovo campione, creato campionando casualmente il set di dati originale con la sostituzione fino a quando non si dispone di un set di dati delle dimensioni dell'originale. Gli output dei modelli vengono combinati *votando*, che è una forma di aggregazione. Per ulteriori informazioni, vedere la voce di Wikipedia per l'aggregazione del bootstrap.

    + **Replica**: nella replica, ogni albero viene addestrato sugli stessi dati di input. La determinazione del predicato diviso viene utilizzata per ogni nodo della struttura ad albero rimane casuale, creando alberi diversi.

   

4. Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**

    + **Parametro singolo**: Selezionare questa opzione se si conosce la configurazione del modello e si fornisce un set di valori come argomenti.

    + **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.   

5. **Numero di alberi delle decisioni**: Digitare il numero massimo di alberi delle decisioni che possono essere creati nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una copertura migliore, ma il tempo di allenamento potrebbe aumentare.

    Questo valore controlla anche il numero di alberi visualizzati nei risultati, durante la visualizzazione del modello sottoposto a training. Per visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Tuttavia, ciò significa che è possibile produrre un solo albero (l'albero con il set iniziale di parametri) e non vengono eseguite ulteriori iterazioni.

6. **Profondità massima degli alberi delle decisioni**: Digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio però di overfitting e di aumento dei tempi di training.

7. **Numero di divisioni casuali per nodo**: Digitare il numero di divisioni da utilizzare durante la compilazione di ogni nodo dell'albero. Una *divisione* significa che le funzionalità in ogni livello dell'albero (nodo) vengono suddivise in modo casuale.

8. **Numero minimo di campioni per nodo foglia**: Indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero. Aumentando questo valore, aumenta la soglia per la creazione di nuove regole.

    Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.



10. Collegare un set di dati con etichetta e uno dei moduli di formazione:

    + Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](./train-model.md)

11. Inviare la pipeline.



## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 