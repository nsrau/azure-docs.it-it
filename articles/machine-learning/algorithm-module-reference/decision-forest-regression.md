---
title: 'Regressione foresta decisionale: riferimento al moduloDecision Forest Regression: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Regressione foresta decisionale in Azure Machine Learning per creare un modello di regressione basato su un insieme di alberi delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456540"
---
# <a name="decision-forest-regression-module"></a>Modulo di regressione della foresta decisionaleDecision Forest Regression module

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per creare un modello di regressione basato su un insieme di alberi delle decisioni.

Dopo aver configurato il modello, è necessario eseguire il training del modello usando un set di dati con etichetta e il modulo [Train Model.](./train-model.md) Sarà quindi possibile usare il modello con training per ottenere stime. 

## <a name="how-it-works"></a>Funzionamento

Gli alberi delle decisioni sono modelli non parametrici che eseguono una sequenza di semplici test per ogni istanza, attraversando una struttura dei dati ad albero binario fino a raggiungere un nodo foglia (decisione).

Gli alberi delle decisioni offrono i vantaggi seguenti:

- Sono efficienti sia nel calcolo che nell'uso della memoria durante il training e la stima.

- Possono rappresentare limiti di decisione non lineari.

- Eseguono la selezione e la classificazione integrata delle funzioni e sono resilienti in caso di funzioni con rumore.

Questo modello di regressione è costituito da un insieme di alberi delle decisioni. Ogni albero in una foresta di decisione di regressione restituisce una distribuzione gaussiana come stima. Viene eseguita un'aggregazione sull'insieme di alberi per trovare una distribuzione gaussiana più vicina alla distribuzione combinata per tutti gli alberi nel modello.

Per altre informazioni sul framework teorico per questo algoritmo e la relativa implementazione, vedere questo articolo: [Foreste decisionali: un framework unificato per classificazione, regressione, stima della densità, apprendimento del ripiegamento e apprendimento semi-supervisionatoFor](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) more information about the theoretical framework for this algorithm and its implementation, see this article: Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Trafold Learning and Semi-Supervised Learning

## <a name="how-to-configure-decision-forest-regression-model"></a>Come configurare il modello di regressione della foresta decisionaleHow to configure Decision Forest Regression Model

1. Aggiungere il modulo **Regressione foresta decisionale** alla pipeline. Il modulo è presente nella finestra di progettazione in **Machine Learning**, **Initialize Model**e Regression **.**

2. Aprire le proprietà del modulo e per **Metodo di ricampionamento**scegliere il metodo utilizzato per creare i singoli alberi.  È possibile scegliere tra **Bagging** o **Replicate**.

    - **Bagging**: Bagging è anche chiamato *bootstrap aggregating*. Ogni albero in una foresta di decisione di regressione restituisce una distribuzione gaussiana tramite previsione. L'aggregazione è quella di trovare un gaussiano i cui primi due momenti corrispondono ai momenti della miscela di distribuzioni gaussiane date combinando tutte le distribuzioni restituite da singoli alberi.

         Per ulteriori informazioni, vedere la voce di Wikipedia per [l'aggregazione del bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replica**: nella replica, ogni albero viene addestrato sugli stessi dati di input. La determinazione del predicato diviso viene utilizzata per ogni nodo della struttura ad albero rimane casuale e gli alberi saranno diversi.

         Per ulteriori informazioni sul processo di formazione con l'opzione **Replica,** vedere [Foreste decisionali per la visione artificiale e l'analisi delle immagini mediche. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/). .

3. Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**

    - **Parametro singolo**

      Se si conosce il modo in cui si desidera configurare il modello, è possibile definire come argomenti un set specifico di valori. Questi valori possono essere stati appresi dalle sperimentazioni o ricevuti come istruzioni.

    - **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali. 



4. Per **Numero di alberi delle decisioni**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma saranno necessari tempi di training maggiori.

    > [!TIP]
    > Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Tuttavia, ciò significa che verrà prodotto un solo albero (l'albero con il set iniziale di parametri) e non verranno eseguite ulteriori iterazioni.

5. In **Profondità massima degli alberi delle decisioni**digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio però di overfitting e di aumento dei tempi di training.

6. Per **Numero di divisioni casuali per nodo**, digitare il numero di divisioni da utilizzare durante la compilazione di ogni nodo della struttura ad albero. Una *divisione* significa che le funzionalità in ogni livello dell'albero (nodo) vengono suddivise in modo casuale.

7. Per **Numero minimo di campioni per nodo foglia**, indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.

     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.


9. Connettere un set di dati con etichetta, selezionare una singola colonna etichetta contenente non più di due risultati e connettersi al modello di [training](./train-model.md).

    - Se si imposta **l'opzione Crea modalità trainer** su **Parametro singolo**, eseguire il training del modello utilizzando il modulo Train [Model.](./train-model.md)

   

10. Inviare la pipeline.

### <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per salvare un'istantanea del modello sottoposto a training, selezionare il modulo di allenamento, quindi passare alla scheda **Uscite** nel pannello di destra. Fare clic sull'icona **Registra modello**.  È possibile trovare il modello salvato come modulo nell'albero del modulo. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 