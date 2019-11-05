---
title: 'Regressione della foresta delle decisioni: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione della foresta delle decisioni in Azure Machine Learning per creare un modello di regressione basato su un insieme di alberi delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d930a6d856c6608e7792ce8ef3204b39aba0028a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497967"
---
# <a name="decision-forest-regression-module"></a>Modulo di regressione della foresta delle decisioni

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di regressione basato su un insieme di alberi delle decisioni.

Dopo aver configurato il modello, è necessario eseguire il training del modello usando un set di dati con etichetta e il modulo [Train Model](./train-model.md) . Il modello con Training può quindi essere utilizzato per eseguire stime. 

## <a name="how-it-works"></a>Funzionamento

Gli alberi delle decisioni sono modelli non parametrici che eseguono una sequenza di semplici test per ogni istanza, attraversando una struttura di dati dell'albero binario finché non viene raggiunto un nodo foglia (decisione).

Gli alberi delle decisioni offrono i vantaggi seguenti:

- Sono efficienti sia nel calcolo che nell'utilizzo della memoria durante il training e la stima.

- Possono rappresentare limiti di decisione non lineari.

- Eseguono la selezione e la classificazione delle funzionalità integrate e sono resilienti in presenza di funzionalità rumorose.

Questo modello di regressione è costituito da un insieme di alberi delle decisioni. Ogni albero in una foresta delle decisioni di regressione restituisce una distribuzione gaussiana come stima. Viene eseguita un'aggregazione sull'insieme di alberi per trovare una distribuzione gaussiana più vicina alla distribuzione combinata per tutti gli alberi nel modello.

Per altre informazioni sul framework teorico per questo algoritmo e sulla relativa implementazione, vedere questo articolo: [foreste delle decisioni: un framework unificato per la classificazione, la regressione, la stima della densità, l'apprendimento di manifold e l'apprendimento semi supervisionato ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Come configurare il modello di regressione della foresta delle decisioni

1. Aggiungere il modulo di **regressione della foresta delle decisioni** alla pipeline. È possibile trovare il modulo nella finestra di progettazione in **Machine Learning**, **inizializzare il modello**e **regressione**.

2. Aprire le proprietà del modulo e, per il **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra l' **insaccamento** o la **replica**.

    - **Insaccamento**: l'insaccamento viene chiamato anche *aggregazione bootstrap*. Ogni albero in una foresta delle decisioni di regressione restituisce una distribuzione gaussiana mediante la stima. L'aggregazione consiste nell'individuare un controllo gaussiana i cui primi due istanti corrispondono ai momenti della combinazione di distribuzioni di Gauss fornite combinando tutte le distribuzioni restituite da singoli alberi.

         Per ulteriori informazioni, vedere la voce Wikipedia per l' [aggregazione bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicate**: nella replica ogni albero viene sottoposto a training esattamente sugli stessi dati di input. La determinazione del predicato Split utilizzato per ogni nodo della struttura ad albero rimane casuale e gli alberi saranno diversi.

         Per altre informazioni sul processo di training con l'opzione **replicate** , vedere [foreste delle decisioni per visione artificiale e analisi delle immagini mediche. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/)..

3. Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .

    - **Singolo parametro**

      Se si conosce il modo in cui si desidera configurare il modello, è possibile specificare come argomenti un set specifico di valori. Questi valori potrebbero essere stati appresi per sperimentazione o ricevuti come linee guida.



4. Per **numero di alberi delle decisioni**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training aumenterà.

    > [!TIP]
    > Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Ciò significa tuttavia che verrà prodotto solo un albero (l'albero con il set di parametri iniziale) e non verranno eseguite altre iterazioni.

5. Per la **profondità massima degli alberi delle decisioni**, digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero può aumentare la precisione, a rischio di overfitting e di aumento dei tempi di training.

6. Per **numero di divisioni casuali per nodo**, digitare il numero di divisioni da usare durante la compilazione di ogni nodo dell'albero. Una *suddivisione* indica che le funzionalità di ogni livello dell'albero (nodo) sono divise in modo casuale.

7. Per il **numero minimo di campioni per nodo foglia**, indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.

     Aumentando questo valore, si aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito 1, anche un singolo caso può causare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque case che soddisfano le stesse condizioni.


9. Connettere un set di dati con etichetta, selezionare una singola colonna di etichetta che non contenga più di due risultati e connettersi al [modello di training](./train-model.md).

    - Se si imposta l'opzione **create Trainer Mode** su un **singolo parametro**, eseguire il training del modello usando il modulo [Train Model](./train-model.md) .

   

10. Eseguire la pipeline.

### <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare l'albero che è stato creato a ogni iterazione, fare clic con il pulsante destro del mouse sull'output del modulo training e scegliere **Visualizza**.

+ Per visualizzare le regole per ogni nodo, fare clic su ogni albero ed eseguire il drill-down nelle divisioni.

+ Per salvare uno snapshot del modello sottoposto a training, fare clic con il pulsante destro del mouse sull'output del modulo training e selezionare **Salva come modello con**training. Questa copia del modello non viene aggiornata sulle esecuzioni successive della pipeline. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 