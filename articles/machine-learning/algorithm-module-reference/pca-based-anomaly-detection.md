---
title: 'Rilevamento anomalie basato su PCA: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come utilizzare il modulo Rilevamento anomalie basato su PCA per creare un modello di rilevamento delle anomalie basato su PCA (Principal Component Analysis).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502984"
---
# <a name="pca-based-anomaly-detection"></a>Rilevamento delle anomalie basato su PCA

Questo articolo descrive come usare il modulo **Rilevamento anomalie basato su PCA** nella finestra di progettazione di Azure Machine Learning (anteprima) per creare un modello di rilevamento anomalie basato sull'analisi dei componenti principali (PCA).

Questo modulo consente di creare un modello in scenari in cui è facile ottenere dati di training da una classe, ad esempio transazioni valide, ma è difficile ottenere campioni sufficienti delle anomalie mirate. 

Ad esempio, per rilevare le transazioni fraudolente, molto spesso non si dispone di abbastanza esempi di frode per la formazione, ma si hanno molti esempi di buone transazioni. Il modulo **Rilevamento anomalie basato su PCA** risolve il problema analizzando le funzionalità disponibili per determinare cosa costituisce una classe "normale" e applicando metriche di distanza per identificare i casi che rappresentano anomalie. Ciò consente di eseguire il training di un modello utilizzando dati sbilanciati esistenti.

## <a name="more-about-principal-component-analysis"></a>Ulteriori informazioni su PCA

*Principal Component Analysis*, spesso abbreviato in PCA, è una tecnica consolidata nell'apprendimento automatico. La tecnica PCA viene spesso usata nell'analisi esplorativa dei dati perché rivela la struttura interna e illustra la varianza all'interno dei dati stessi.

Questa tecnica consiste nell'eseguire l'analisi di dati che contengono più variabili. Cerca le correlazioni tra le variabili e determina la combinazione di valori che meglio rappresenta le differenze nei risultati. Questi valori combinati di funzionalità vengono utilizzati per creare uno spazio di funzionalità più compatto denominato *componenti principali*.

Per il rilevamento delle anomalie, ogni nuovo input viene analizzato e l'algoritmo di rilevamento delle anomalie calcola la sua proiezione sugli eigenvectors, insieme a un errore di ricostruzione normalizzato. L'errore normalizzato viene utilizzato come punteggio di anomalia. A un punteggio maggiore corrisponde una maggiore anomalia dell'istanza.

Per ulteriori informazioni sul funzionamento di PCA e sull'implementazione per il rilevamento delle anomalie, vedere i seguenti documenti:

- [Algoritmo randomizzato per l'analisi](https://arxiv.org/abs/0809.2274)dei componenti principali. Rokhlin, Szlan e Tygert

- [Ricerca di struttura con casualità: algoritmi probabilistici per la costruzione di scomposizioni](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) a matrice approssimativa (download PDF). Halko, Martinsson e.

## <a name="how-to-configure-pca-anomaly-detection"></a>Come configurare il rilevamento anomalie PCA

1. Aggiungere il modulo **Rilevamento anomalie basato su PCA** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Rilevamento anomalie.**

2. Nel pannello destro del modulo **Rilevamento anomalie basato su PCA,** fare clic sull'opzione **Modalità allenamento** e indicare se si desidera eseguire il training del modello utilizzando un set specifico di parametri o utilizzare una sweep di parametri per trovare i parametri migliori.

    - **Parametro singolo**: Selezionare questa opzione se si conosce la configurazione del modello e si fornisce un set specifico di valori come argomenti.

3. **Numero di componenti da utilizzare in PCA**: Specificare il numero di funzioni di output, o componenti, che si desidera emettere.

    La decisione del numero di componenti da includere è una parte importante della progettazione dell'esperimento tramite PCA. Indicazioni generali è che non è necessario includere lo stesso numero di componenti PCA come ci sono variabili. Invece, si dovrebbe iniziare con un numero inferiore di componenti e aumentarli fino a quando non vengono soddisfatti alcuni criteri.

    I risultati migliori si ottengono quando il numero di componenti di output è **inferiore** al numero di colonne di entità geografiche disponibili nel set di dati.

4. Specificare la quantità di sovracampionamento da eseguire durante l'allenamento PCA randomizzato. Nei problemi di rilevamento delle anomalie, i dati sbilanciati rendono difficile l'applicazione di tecniche PCA standard. Specificando una certa quantità di sovracampionamento, è possibile aumentare il numero di istanze di destinazione.

    Se si specifica 1, non viene eseguito alcun sovracampionamento. Se si specifica un valore maggiore di 1, vengono generati campioni aggiuntivi da usare per il training del modello.

    Sono disponibili due opzioni, a seconda che si stia utilizzando o meno una sweep di parametro:

    - **Parametro di sovracampionamento per PCA randomizzato:** digitare un singolo numero intero che rappresenta il rapporto di sovracampionamento della classe di minoranza rispetto alla classe normale. (Disponibile quando si utilizza il metodo di training **a parametro singolo.)**

    > [!NOTE]
    > Non è possibile visualizzare il set di dati sovracampionato. Per ulteriori informazioni sull'utilizzo del sovracampionamento con PCA, consultate [Note tecniche.](#technical-notes)

5. Abilita funzione di **input significa normalizzazione**: Selezionare questa opzione per normalizzare tutte le funzioni di input con una media pari a zero. La normalizzazione o il ridimensionamento a zero è generalmente consigliato per PCA, perché l'obiettivo di PCA è quello di massimizzare la varianza tra le variabili.

     Questa opzione è selezionata per impostazione predefinita. Deselezionate questa opzione se i valori sono già stati normalizzati utilizzando un metodo o una scala diversa.

6. Connettere un set di dati di training con tag e uno dei moduli di training:Connect a tagged training dataset, and one of the training modules:

    - Se si imposta l'opzione **Crea modalità trainer** su **Parametro singolo**, utilizzare il modulo Modello di [rilevamento anomalie treno.](train-anomaly-detection-model.md)

7. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile salvare il modello sottoposto a training o collegarlo al modulo Modello di [punteggio](score-model.md) per prevedere i punteggi di anomalia.

La valutazione dei risultati di un modello di rilevamento delle anomalie richiede alcuni passaggi aggiuntivi:Evaluating the results of an a anomalie detection model requires some additional steps:

1. Verificare che una colonna del punteggio sia disponibile in entrambi i set di datiEnsure that a score column is available in both datasets

    Se si tenta di valutare un modello di rilevamento anomalie e si ottiene l'errore "Non esiste alcuna colonna di punteggio nel set di dati con punteggio da confrontare", significa che si sta utilizzando un tipico set di dati di valutazione che contiene una colonna label ma nessun punteggio di probabilità. È necessario scegliere un set di dati che corrisponda all'output dello schema per i modelli di rilevamento delle anomalie, che include una colonna **Etichette** con punteggio e **Probabilità con punteggio.**

2. Assicurarsi che le colonne delle etichette siano contrassegnate

    Talvolta i metadati associati alla colonna label vengono rimossi nel grafico della pipeline. In questo caso, quando si utilizza il modulo [Valuta modello](evaluate-model.md) per confrontare i risultati di due modelli di rilevamento anomalie, è possibile che venga visualizzato l'errore "Non esiste alcuna colonna di etichette nel set di dati con parti ture" o "Non è presente alcuna colonna di etichetta nel set di dati con partitura da confrontare".

    È possibile evitare questo errore aggiungendo il modulo [Modifica metadati](edit-metadata.md) prima del modulo [Valuta modello.](evaluate-model.md) Utilizzare il selettore di colonna per scegliere la colonna della classe e nell'elenco a discesa **Campi** selezionare **Etichetta**.

3. Utilizzare [Esegui script Python](execute-python-script.md) per regolare le categorie di colonne etichetta come 1(positivo, normale) e 0 (negativo, anormale).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Note tecniche

Questo algoritmo utilizza PCA per approssimare il sottospazio contenente la classe normale. Il sottospazio è attraversato da eigenvectors associati ai primi eigenvalues della matrice di covarianza dei dati. Per ogni nuovo input, il rilevatore di anomalie calcola prima la proiezione sugli eigenvectors, quindi calcola l'errore di ricostruzione normalizzato. Questo errore è il punteggio di anomalia. Più alto è l'errore, più anomala è l'istanza. Per informazioni dettagliate su come viene calcolato lo spazio normale, vedere Wikipedia: [Principal Component Analysis](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

Per un elenco di errori specifici per i moduli della finestra di progettazione, vedere Eccezioni e codici di errore per la finestra di [progettazione.'](designer-error-codes.md)