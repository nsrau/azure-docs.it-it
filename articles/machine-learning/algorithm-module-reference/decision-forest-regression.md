---
title: 'Regressione tramite foresta delle decisioni: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Decision Forest Regression nel servizio Azure Machine Learning per creare un modello di regressione basato su un insieme di alberi delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442371"
---
# <a name="decision-forest-regression-module"></a>Modulo di regressione di foreste delle decisioni

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di regressione basato su un insieme di alberi delle decisioni.

Dopo aver configurato il modello, è necessario eseguirne il training del modello usando un set di dati con etichette e la [Train Model](./train-model.md) modulo.  Il modello con training può quindi utilizzabile per eseguire stime. 

## <a name="how-it-works"></a>Funzionamento

Gli alberi delle decisioni sono modelli non parametrici che eseguono una sequenza di semplici test per ogni istanza, attraversando una struttura di dati ad albero binario fino al raggiungimento di un nodo foglia (decisione).

Gli alberi delle decisioni offrono i vantaggi seguenti:

- Sono efficienti in calcolo e dell'utilizzo della memoria durante il training e stima.

- Possono rappresentare limiti di decisione non lineari.

- Che eseguono funzionalità integrate di selezione e classificazione e sono resilienti in caso di funzionalità rumorose.

Questo modello di regressione è costituito da un insieme di alberi delle decisioni. Ogni albero in una foresta delle decisioni regressione genera una distribuzione di Gauss come una stima. Un'aggregazione viene eseguita tramite l'insieme di alberi per trovare più vicino alla distribuzione combinata per tutti gli alberi nel modello di distribuzione gaussiana.

Per altre informazioni sul framework di teorico per questo algoritmo e la relativa implementazione, vedere questo articolo: [Le foreste delle decisioni: Un Framework unificato per la classificazione, regressione, la stima, densità del collettore formazione e apprendimento semi-supervisionati](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak nakonfigurovat modello di regressione di foreste delle decisioni

1. Aggiungere il **Decision Forest Regression** modulo all'esperimento. È possibile trovare il modulo nell'interfaccia sotto **Machine Learning**, **modello di inizializzazione**, e **regressione**.

2. Aprire le proprietà del modulo e per **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra **Bagging** oppure **replicare**.

    - **Bagging**: Viene anche chiamato bagging *l'aggregazione di bootstrap*. Ogni albero in una foresta delle decisioni regressione genera una distribuzione di Gauss mezzo di stima. L'aggregazione consiste nel trovare un Gauss cui primi due istanti corrispondono i momenti della miscela di Gaussians dato dalla combinazione di tutti i Gaussians restituito da singoli alberi.

         Per altre informazioni, vedere la voce di Wikipedia relativa [l'aggregazione di Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicare**: Nella replica di ogni albero viene eseguito il training su esattamente gli stessi dati di input. La determinazione di quali split predicato è utilizzato per ciascun nodo dell'albero rimane casuali e alberi saranno diversi.

         Per altre informazioni sul processo di training con il **replicare** opzione, vedere [foreste delle decisioni per visione artificiale e analisi delle immagini medici. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.

    - **Parametro singolo**

      Se si conosce la modalità con cui si desidera configurare il modello, è possibile definire un set specifico di valori come argomenti. È che questi valori possono essere stati appresi dalle sperimentazioni o ricevuti come istruzioni.



4. Per la **numero di alberi delle decisioni**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma sarà necessario più tempo di training.

    > [!TIP]
    > Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello con training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1. Tuttavia, ciò significa che un solo albero sarà generati (l'albero con il set iniziale di parametri) e non verranno eseguite alcuna ulteriore iterazioni.

5. Per la **profondità massima degli alberi delle decisioni**, digitare un numero per limitare la profondità massima degli alberi delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio di overfitting e una maggiore tempi di training.

6. Per la **numero di divisioni casuali per nodo**, digitare il numero di suddivisioni da usare durante la compilazione di ogni nodo dell'albero. Oggetto *dividere* significa che le funzionalità disponibili in ogni livello dell'albero (nodo) venga divise in modo casuale.

7. Per la **numero minimo di campioni per ogni nodo foglia**, indicare il numero minimo di casi necessari per creare un nodo terminale (foglia) in una struttura ad albero.

     Se si aumenta questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito pari a 1, anche un singolo caso può determinare una nuova regola da creare. Se si aumenta il valore su 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.


9. Connettere un set di dati con etichetta, selezionare una colonna di etichetta singola contenente non più di due risultati e connettersi al [Train Model](./train-model.md).

    - Se si imposta **modalità di creazione trainer** possibilità **singolo parametro**, il training del modello utilizzando il [Train Model](./train-model.md) modulo.

   

10. Eseguire l'esperimento.

### <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare la struttura ad albero che è stata creata in ogni iterazione, l'output del modulo di formazione e scegliere **Visualize**.

+ Per visualizzare le regole per ogni nodo, fare clic su ciascuna struttura e il drill down le divisioni.

+ Per salvare uno snapshot del modello con training, l'output del modulo di formazione e scegliere **Salva come modello sottoposto a training**. Questa copia del modello non viene aggiornata a ogni esecuzione dell'esperimento. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 