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
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502984"
---
# <a name="pca-based-anomaly-detection"></a>Rilevamento anomalie basato su PCA

Questo articolo descrive come usare il modulo di **rilevamento delle anomalie basato su PCA** in Azure Machine Learning Designer (anteprima) per creare un modello di rilevamento delle anomalie basato sull'analisi dei componenti principali (PCA).

Questo modulo consente di creare un modello in scenari in cui è facile ottenere i dati di training da una classe, ad esempio transazioni valide, ma è difficile ottenere campioni sufficienti delle anomalie di destinazione. 

Ad esempio, per rilevare le transazioni fraudolente, molto spesso non si dispone di un numero sufficiente di esempi di illecito per eseguire il training, ma sono disponibili molti esempi di transazioni valide. Il modulo di **rilevamento delle anomalie basato su PCA** risolve il problema analizzando le funzionalità disponibili per determinare ciò che costituisce una classe "normale" e applicando metriche di distanza per identificare i casi che rappresentano le anomalie. In questo modo è possibile eseguire il training di un modello utilizzando dati sbilanciati esistenti.

## <a name="more-about-principal-component-analysis"></a>Ulteriori informazioni su PCA

L' *analisi principale dei componenti*, che è spesso abbreviata in PCA, è una tecnica consolidata in Machine Learning. La tecnica PCA viene spesso usata nell'analisi esplorativa dei dati perché rivela la struttura interna e illustra la varianza all'interno dei dati stessi.

Questa tecnica consiste nell'eseguire l'analisi di dati che contengono più variabili. Cerca le correlazioni tra le variabili e determina la combinazione di valori che meglio rappresenta le differenze nei risultati. Questi valori combinati delle funzionalità vengono usati per creare uno spazio di funzionalità più compatto denominato *componenti principali*.

Per il rilevamento delle anomalie, ogni nuovo input viene analizzato e l'algoritmo di rilevamento delle anomalie calcola la proiezione in autovettori, insieme a un errore di ricostruzione normalizzato. L'errore normalizzato viene usato come Punteggio anomalo. A un punteggio maggiore corrisponde una maggiore anomalia dell'istanza.

Per altre informazioni sul funzionamento della PCA e sull'implementazione per il rilevamento delle anomalie, vedere i documenti seguenti:

- [Algoritmo casuale per l'analisi principale dei componenti](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan e Tygert

- [Ricerca della struttura con sequenza casuale: algoritmi probabilistici per la costruzione di decomposizione di matrici approssimative](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (Download PDF). Halko, Martinsson e tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Come configurare il rilevamento di anomalie PCA

1. Aggiungere il modulo di **rilevamento delle anomalie basato su PCA** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Rilevamento anomalie** .

2. Nel pannello destro del modulo di **Rilevamento anomalie basato su PCA** , fare clic sull'opzione **modalità di training** e indicare se si desidera eseguire il training del modello utilizzando un set di parametri specifico oppure utilizzare uno sweep di parametri per trovare i parametri migliori.

    - **Singolo parametro**: selezionare questa opzione se si sa come si desidera configurare il modello e specificare un set di valori specifico come argomenti.

3. **Numero di componenti da usare in PCA**: specificare il numero di funzionalità di output, o componenti, di cui si vuole eseguire l'output.

    La decisione relativa al numero di componenti da includere è una parte importante della progettazione dell'esperimento con PCA. Il materiale sussidiario generale è che non è necessario includere lo stesso numero di componenti PCA perché sono presenti variabili. È invece consigliabile iniziare con un numero minore di componenti e aumentarli fino a quando non vengono soddisfatti alcuni criteri.

    I risultati migliori si ottengono quando il numero di componenti di output è **inferiore** al numero di colonne di funzionalità disponibili nel set di dati.

4. Consente di specificare la quantità di sovracampionamento da eseguire durante il training PCA casuale. Nei problemi di rilevamento anomalie, i dati sbilanciati rendono difficile l'applicazione delle tecniche standard PCA. Specificando una certa quantità di sovracampionamento, è possibile aumentare il numero di istanze di destinazione.

    Se si specifica 1, non viene eseguito alcun sovracampionamento. Se si specifica un valore maggiore di 1, verranno generati esempi aggiuntivi da utilizzare per il training del modello.

    Sono disponibili due opzioni, a seconda che si usi o meno uno sweep di parametri:

    - **Parametro di sovracampionamento per PCA casuale**: digitare un singolo numero intero che rappresenta il rapporto tra il sovracampionamento della classe di minoranza sulla classe normale. (Disponibile quando si usa il metodo di training per **parametri singoli** ).

    > [!NOTE]
    > Non è possibile visualizzare il set di dati sottocampionato. Per ulteriori informazioni sull'utilizzo del sovracampionamento con PCA, vedere [Note tecniche](#technical-notes).

5. **Abilita la normalizzazione media della funzionalità di input**: selezionare questa opzione per normalizzare tutte le funzionalità di input su una media pari a zero. La normalizzazione o la scalabilità a zero è generalmente consigliata per PCA, perché l'obiettivo di PCA è quello di ottimizzare la varianza tra le variabili.

     Questa opzione è selezionata per impostazione predefinita. Deselezionare questa opzione se i valori sono già stati normalizzati con un metodo o una scala diversa.

6. Connettere un set di dati di training con tag e uno dei moduli di training:

    - Se si imposta l'opzione **create Trainer Mode** su un **singolo parametro**, usare il modulo [Train Anomaly Detection Model](train-anomaly-detection-model.md) .

7. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile salvare il modello sottoposto a training o connetterlo al modulo [Score Model](score-model.md) per stimare i punteggi delle anomalie.

Per valutare i risultati di un modello di rilevamento delle anomalie sono necessari alcuni passaggi aggiuntivi:

1. Verificare che una colonna Score sia disponibile in entrambi i set di impostazioni

    Se si tenta di valutare un modello di rilevamento delle anomalie e viene restituito l'errore "non è presente alcuna colonna score nel set di dati con punteggio da confrontare", significa che si sta usando un set di dati di valutazione tipico che contiene una colonna di etichetta ma senza punteggi di probabilità. È necessario scegliere un set di dati che corrisponda all'output dello schema per i modelli di rilevamento delle anomalie, che include una colonna con **punteggio delle etichette** e delle **probabilità con punteggio** .

2. Verificare che le colonne delle etichette siano contrassegnate

    In alcuni casi i metadati associati alla colonna Label vengono rimossi nel grafico della pipeline. In tal caso, quando si usa il modulo [Evaluate Model](evaluate-model.md) per confrontare i risultati di due modelli di rilevamento delle anomalie, è possibile che venga restituito l'errore "non è presente alcuna colonna Label nel set di dati con punteggio" oppure "non è presente alcuna colonna Label nel set di dati con punteggio da confrontare".

    È possibile evitare questo errore aggiungendo il modulo [Edit Metadata (Modifica metadati](edit-metadata.md) ) prima del modulo [Evaluate Model](evaluate-model.md) . Usare il selettore di colonna per scegliere la colonna classe e nell'elenco a discesa **campi** selezionare **etichetta**.

3. Usare [Execute Python script](execute-python-script.md) per modificare le categorie di colonne dell'etichetta come 1 (positivo, normale) e 0 (negativo, anomalo).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Note tecniche

Questo algoritmo usa PCA per approssimare il sottospazio che contiene la classe normale. Il sottospazio viene esteso da autovettori associato ai primi autovalori della matrice di covarianza dei dati. Per ogni nuovo input, il rilevatore di anomalie calcola prima di tutto la proiezione in autovettori, quindi calcola l'errore di ricostruzione normalizzato. Questo errore è il Punteggio di anomalie. Maggiore è l'errore, più è anomala l'istanza. Per informazioni dettagliate su come viene calcolato lo spazio normale, vedere Wikipedia: [analisi dei componenti principali](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

Vedere [le eccezioni e i codici di errore per la finestra di progettazione (anteprima)](designer-error-codes.md) per un elenco di errori specifici dei moduli della finestra di progettazione .''