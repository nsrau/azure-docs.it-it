---
title: 'Regressione della foresta delle decisioni: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione della foresta delle decisioni in Azure Machine Learning per creare un modello di regressione basato su un insieme di alberi delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: bb7ebee67d65ab37dc037437b7c35d8c19c53096
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137026"
---
# <a name="decision-forest-regression-module"></a>Modulo di regressione della foresta delle decisioni

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di regressione basato su un insieme di alberi delle decisioni.

Dopo aver configurato il modello, è necessario eseguire il training del modello usando un set di dati con etichetta e il modulo [Train Model](./train-model.md) . Sarà quindi possibile usare il modello con training per ottenere stime. 

## <a name="how-it-works"></a>Come funziona

Gli alberi delle decisioni sono modelli non parametrici che eseguono una sequenza di semplici test per ogni istanza, attraversando una struttura dei dati ad albero binario fino a raggiungere un nodo foglia (decisione).

Gli alberi delle decisioni offrono i vantaggi seguenti:

- Sono efficienti sia nel calcolo che nell'uso della memoria durante il training e la stima.

- Possono rappresentare limiti di decisione non lineari.

- Eseguono la selezione e la classificazione integrata delle funzioni e sono resilienti in caso di funzioni con rumore.

Questo modello di regressione è costituito da un insieme di alberi delle decisioni. Ogni albero in una foresta delle decisioni di regressione restituisce una distribuzione gaussiana come stima. Viene eseguita un'aggregazione sull'insieme di alberi per trovare una distribuzione gaussiana più vicina alla distribuzione combinata per tutti gli alberi nel modello.

Per altre informazioni sul framework teorico per questo algoritmo e sulla relativa implementazione, vedere questo articolo: [foreste delle decisioni: un framework unificato per la classificazione, la regressione, la stima della densità, l'apprendimento di manifold e l'apprendimento semi supervisionato](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Come configurare il modello di regressione della foresta delle decisioni

1. Aggiungere il modulo di **regressione della foresta delle decisioni** alla pipeline. È possibile trovare il modulo nella finestra di progettazione in **Machine Learning**, **inizializzare il modello**e **regressione**.

2. Aprire le proprietà del modulo e, per il **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra l' **insaccamento** o la **replica**.

    - **Insaccamento**: l'insaccamento viene chiamato anche *aggregazione bootstrap*. Ogni albero in una foresta delle decisioni di regressione restituisce una distribuzione gaussiana mediante la stima. L'aggregazione consiste nell'individuare un controllo gaussiana i cui primi due istanti corrispondono ai momenti della combinazione di distribuzioni di Gauss fornite combinando tutte le distribuzioni restituite da singoli alberi.

         Per ulteriori informazioni, vedere la voce Wikipedia per l' [aggregazione bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicate**: nella replica ogni albero viene sottoposto a training esattamente sugli stessi dati di input. La determinazione del predicato Split utilizzato per ogni nodo della struttura ad albero rimane casuale e gli alberi saranno diversi.

         Per altre informazioni sul processo di training con l'opzione **replicate** , vedere [foreste delle decisioni per visione artificiale e analisi delle immagini mediche. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/)..

3. Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .

    - **Parametro singolo**

      Se si conosce il modo in cui si desidera configurare il modello, è possibile definire come argomenti un set specifico di valori. Questi valori possono essere stati appresi dalle sperimentazioni o ricevuti come istruzioni.

    - **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali. 



4. Per **numero di alberi delle decisioni**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma saranno necessari tempi di training maggiori.

    > [!TIP]
    > Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Ciò significa tuttavia che verrà prodotto solo un albero (l'albero con il set di parametri iniziale) e non verranno eseguite altre iterazioni.

5. Per la **profondità massima degli alberi delle decisioni**, digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio però di overfitting e di aumento dei tempi di training.

6. Per **numero di divisioni casuali per nodo**, digitare il numero di divisioni da usare durante la compilazione di ogni nodo dell'albero. Una *suddivisione* indica che le funzionalità di ogni livello dell'albero (nodo) sono divise in modo casuale.

7. Per il **numero minimo di campioni per nodo foglia**, indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.

     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.


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

### <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare il modulo training, quindi passare alla scheda **output** nel riquadro di destra. Fare clic sull'icona **Registra modello**.  È possibile trovare il modello salvato come modulo nell'albero del modulo. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 