---
title: 'Rilevamento di anomalie basato su PCA: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di rilevamento delle anomalie basato su PCA per creare un modello di rilevamento delle anomalie basato sull'analisi dei componenti principali (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898467"
---
# <a name="pca-based-anomaly-detection-module"></a>Modulo di rilevamento delle anomalie basato su PCA

Questo articolo descrive come usare il modulo di rilevamento delle anomalie basato su PCA in Azure Machine Learning Designer, per creare un modello di rilevamento delle anomalie basato sull'analisi dei componenti principali (PCA).

Questo modulo consente di creare un modello in scenari in cui è facile ottenere i dati di training da una classe, ad esempio transazioni valide, ma è difficile ottenere campioni sufficienti delle anomalie di destinazione. 

Per rilevare le transazioni fraudolente, ad esempio, spesso non si dispone di un numero sufficiente di esempi di illecito di cui eseguire il training. Tuttavia, si potrebbero avere molti esempi di transazioni valide. Il modulo di rilevamento delle anomalie basato su PCA risolve il problema analizzando le funzionalità disponibili per determinare ciò che costituisce una classe "normale". Il modulo applica quindi le metriche di distanza per identificare i casi che rappresentano le anomalie. Questo approccio consente di eseguire il training di un modello utilizzando dati sbilanciati esistenti.

## <a name="more-about-principal-component-analysis"></a>Altre informazioni sull'analisi dei componenti principali

PCA è una tecnica consolidata in Machine Learning. Viene spesso usata nell'analisi esplorativa dei dati perché rivela la struttura interna dei dati e spiega la varianza nei dati.

Questa tecnica consiste nell'eseguire l'analisi di dati che contengono più variabili. Cerca le correlazioni tra le variabili e determina la combinazione di valori che meglio rappresenta le differenze nei risultati. Questi valori combinati delle funzionalità vengono usati per creare uno spazio di funzionalità più compatto denominato *componenti principali*.

Per il rilevamento delle anomalie, viene analizzato ogni nuovo input. L'algoritmo di rilevamento delle anomalie calcola la proiezione in autovettori, insieme a un errore di ricostruzione normalizzato. L'errore normalizzato viene usato come Punteggio anomalo. A un punteggio maggiore corrisponde una maggiore anomalia dell'istanza.

Per altre informazioni sul funzionamento della PCA e sull'implementazione per il rilevamento delle anomalie, vedere i documenti seguenti:

- [Algoritmo casuale per l'analisi dei componenti principali](https://arxiv.org/abs/0809.2274), da Rokhlin, Szlan e Tygert

- [Ricerca della struttura con casualità: algoritmi probabilistici per la costruzione di decomposizione di matrici approssimative](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (Download PDF), da Halko, Martinsson e tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>Come configurare il rilevamento delle anomalie basato su PCA

1. Aggiungere il modulo di **rilevamento delle anomalie basato su PCA** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Rilevamento anomalie** .

2. Nel pannello destro del modulo selezionare l'opzione modalità di **Training** . Indicare se si desidera eseguire il training del modello utilizzando un set di parametri specifico oppure utilizzare uno sweep di parametri per trovare i parametri migliori.

    Se si conosce il modo in cui si desidera configurare il modello, selezionare l'opzione del **singolo parametro** e specificare un set di valori specifico come argomenti.

3. Per il **numero di componenti da usare in PCA**, specificare il numero di funzionalità di output o i componenti desiderati.

    La decisione relativa al numero di componenti da includere è una parte importante della progettazione dell'esperimento che usa la PCA. Il materiale sussidiario generale è che non è necessario includere lo stesso numero di componenti PCA perché sono presenti variabili. È invece consigliabile iniziare con un numero minore di componenti e aumentarli fino a quando non viene raggiunto un criterio.

    I risultati migliori si ottengono quando il numero di componenti di output è *inferiore* al numero di colonne di funzionalità disponibili nel set di dati.

4. Consente di specificare la quantità di sovracampionamento da eseguire durante il training PCA casuale. Nei problemi di rilevamento anomalie, i dati sbilanciati rendono difficile l'applicazione delle tecniche standard PCA. Specificando una certa quantità di sovracampionamento, è possibile aumentare il numero di istanze di destinazione.

    Se si specifica **1**, non viene eseguito alcun sovracampionamento. Se si specifica un valore maggiore di **1**, verranno generati esempi aggiuntivi da utilizzare per il training del modello.

    Sono disponibili due opzioni, a seconda che si stia usando uno sweep di parametri o meno:

    - **Parametro di sovracampionamento per PCA casuale**: digitare un singolo numero intero che rappresenta il rapporto tra il sovracampionamento della classe di minoranza sulla classe normale. Questa opzione è disponibile quando si usa il metodo di training a **parametro singolo** .

    > [!NOTE]
    > Non è possibile visualizzare il set di dati sottocampionato. Per ulteriori informazioni sull'utilizzo del sovracampionamento con PCA, vedere [Note tecniche](#technical-notes).

5. Selezionare l'opzione **Abilita la normalizzazione media della funzionalità di input** per normalizzare tutte le funzionalità di input su una media pari a zero. La normalizzazione o la scalabilità a zero è generalmente consigliata per PCA, perché l'obiettivo di PCA è quello di ottimizzare la varianza tra le variabili.

    Questa opzione è selezionata per impostazione predefinita. Deselezionarlo se i valori sono già stati normalizzati tramite un metodo o una scala diversa.

6. Connettere un set di dati di training con tag e uno dei moduli di training.

   Se si imposta l'opzione **create Trainer Mode** su un **singolo parametro**, usare il modulo [Train Anomaly Detection Model](train-anomaly-detection-model.md) .

7. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile salvare il modello sottoposto a training. In alternativa, è possibile connetterlo al modulo [Score Model](score-model.md) per stimare i punteggi delle anomalie.

Per valutare i risultati di un modello di rilevamento delle anomalie:

1. Verificare che sia disponibile una colonna score in entrambi i set di impostazioni.

    Se si tenta di valutare un modello di rilevamento delle anomalie e viene restituito l'errore "non è presente alcuna colonna di punteggio nel set di dati con punteggio da confrontare", si sta usando un set di dati di valutazione tipico che contiene una colonna di etichetta ma senza punteggi di probabilità. Scegliere un set di dati che corrisponda all'output dello schema per i modelli di rilevamento delle anomalie, che include le **etichette con punteggio** e le **probabilità con punteggio** .

2. Verificare che le colonne delle etichette siano contrassegnate.

    In alcuni casi i metadati associati alla colonna Label vengono rimossi nel grafico della pipeline. In tal caso, quando si usa il modulo [Evaluate Model](evaluate-model.md) per confrontare i risultati di due modelli di rilevamento delle anomalie, è possibile che venga restituito l'errore "non è presente alcuna colonna Label nel set di dati con punteggio". In alternativa, è possibile che venga ricevuto l'errore "non è presente alcuna colonna etichetta nel set di dati con punteggio da confrontare".

    È possibile evitare questi errori aggiungendo il modulo di [modifica dei metadati](edit-metadata.md) prima del modulo [Evaluate Model](evaluate-model.md) . Usare il selettore di colonna per scegliere la colonna della classe e nell'elenco **campi** selezionare **etichetta**.

3. Usare il modulo [Execute Python script](execute-python-script.md) per modificare le categorie delle colonne delle etichette come **1 (positivo, normale)** e **0 (negativo, anomalo)**.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Note tecniche

Questo algoritmo usa PCA per approssimare il sottospazio che contiene la classe normale. Il sottospazio viene esteso da autovettori associato ai primi autovalori della matrice di covarianza dei dati. 

Per ogni nuovo input, il rilevatore di anomalie calcola prima di tutto la proiezione in autovettori, quindi calcola l'errore di ricostruzione normalizzato. Questo errore è il Punteggio di anomalie. Maggiore è l'errore, più è anomala l'istanza. Per informazioni dettagliate su come viene calcolato lo spazio normale, vedere Wikipedia: [analisi principale dei componenti](https://wikipedia.org/wiki/Principal_component_analysis). 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

Vedere [le eccezioni e i codici di errore per la finestra di progettazione](designer-error-codes.md) per un elenco di errori specifici dei moduli della finestra di progettazione.