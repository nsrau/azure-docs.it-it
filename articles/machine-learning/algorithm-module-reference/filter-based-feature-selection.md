---
title: 'Selezione delle funzioni basata su filtro: informazioni di riferimento sui moduli'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Filter based feature selection nel servizio Azure Machine Learning per identificare le funzionalità in un set di dati con la massima potenza predittiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517969"
---
# <a name="filter-based-feature-selection"></a>Selezione delle funzioni basata su filtro

Questo articolo descrive come usare il modulo [Filter based feature selection](filter-based-feature-selection.md) in Azure Machine Learning Designer (Preview) per identificare le colonne nel set di dati di input con la massima potenza predittiva. 

In generale, la *selezione delle caratteristiche* si riferisce al processo di applicazione dei test statistici agli input, dato un output specificato, per determinare quali colonne sono più predittive dell'output. Il modulo [filtro basato sulle funzionalità](filter-based-feature-selection.md) di selezione offre più algoritmi di selezione delle funzioni, inclusi i metodi di correlazione, ad esempio la correlazione di Pearson e i valori chi quadrato. 

Quando si usa il modulo di [selezione delle caratteristiche basato su filtro](filter-based-feature-selection.md) , si fornisce un set di dati, si identifica la colonna contenente l'etichetta o la variabile dipendente, quindi si specifica un singolo metodo da usare per misurare l'importanza della funzionalità.

Il modulo restituisce un set di dati che contiene le colonne di funzionalità migliori, classificate in base alla potenza predittiva. Restituisce anche i nomi delle funzionalità e i relativi punteggi dalla metrica selezionata.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Che cos'è la selezione delle funzioni basata su filtro e perché utilizzarla?  

Questo modulo per la selezione delle funzioni è denominato "basato su filtri" perché si usa la metrica selezionata per identificare gli attributi irrilevanti e filtrare le colonne ridondanti dal modello.  È possibile scegliere una singola misura statistica adatta ai dati e il modulo calcola un punteggio per ogni colonna della funzionalità. Le colonne vengono restituite in base ai punteggi delle funzionalità. 

Scegliendo le funzionalità appropriate, è possibile migliorare la precisione e l'efficienza della classificazione. 

In genere si utilizzano solo le colonne con i punteggi migliori per compilare il modello predittivo. Le colonne con punteggi di selezione delle funzionalità non sufficienti possono essere lasciate nel set di dati e ignorate quando si compila un modello.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Come scegliere una metrica di selezione delle funzionalità

La **selezione delle funzioni basata su filtro** offre una serie di metriche per la valutazione del valore delle informazioni in ogni colonna.  Questa sezione fornisce una descrizione generale di ogni metrica e come viene applicata. I requisiti aggiuntivi per l'uso di ogni metrica sono indicati nella sezione [Note tecniche](#technical-notes) e nelle [istruzioni](#how-to-configure-filter-based-feature-selection) per la configurazione di ogni modulo.

-   **Correlazione di Pearson**  

     Le statistiche di correlazione di Pearson, o il coefficiente di correlazione di Pearson, sono note anche come valore `r` nei modelli statistici. Per due variabili qualsiasi, restituisce un valore che indica il livello di attendibilità della correlazione

     Il coefficiente di correlazione di Pearson viene calcolato prendendo la covarianza di due variabili e dividendo in base al prodotto della relativa deviazione standard. Il coefficiente non è interessato dalle modifiche di scala nelle due variabili.  

-   **Chi quadrato**  

     Il test bidirezionale di chi quadrato è un metodo statistico che misura il modo in cui i valori previsti sono vicini ai risultati effettivi. Il metodo presuppone che le variabili siano casuali e disegnate da un campione appropriato di variabili indipendenti. La statistica del chi quadrato risultante indica la distanza dei risultati dal risultato previsto (casuale).  


> [!TIP]
> Se è necessaria un'opzione diversa per il metodo di selezione delle funzioni personalizzate, usare il modulo [Execute R script](execute-r-script.md) . 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Come configurare la selezione delle funzioni basata su filtro

Si sceglie una metrica statistica standard e il modulo calcola la correlazione tra una coppia di colonne, ovvero la colonna Label e una colonna Feature

1.  Aggiungere il modulo **Filter-based feature selection** alla pipeline. È possibile trovarlo nella categoria **Selezione funzionalità** nella finestra di progettazione.

2. Connettere un set di dati di input contenente almeno due colonne che rappresentano funzionalità potenziali.  

    Per assicurarsi che una colonna venga analizzata e che sia stato generato un punteggio di funzionalità, utilizzare il modulo [Modifica metadati](edit-metadata.md) per impostare l'attributo di **funzione** . 

    > [!IMPORTANT]
    > Verificare che le colonne fornite come input siano funzionalità potenziali. Una colonna che contiene un singolo valore, ad esempio, non ha un valore di informazioni.
    >
    > Se sono presenti colonne che potrebbero comportare funzionalità non valide, è possibile rimuoverle dalla selezione della colonna. È anche possibile usare il modulo [Modifica metadati](edit-metadata.md) per contrassegnarli come **categorici**. 
3.  Per il metodo di assegnazione dei punteggi delle **funzionalità**, scegliere uno dei seguenti metodi statistici stabiliti da usare per il calcolo dei punteggi.  

    | Metodo              | Requisiti                             |
    | ------------------- | ---------------------------------------- |
    | Correlazione di Pearson | Label può essere di testo o numerico. Le funzionalità devono essere numeriche. |
    Chi quadrato| Le etichette e le funzionalità possono essere di testo o numerici. Utilizzare questo metodo per calcolare l'importanza della funzionalità per due colonne categoriche.|

    > [!TIP]
    > Se si modifica la metrica selezionata, tutte le altre selezioni verranno reimpostate, quindi assicurarsi di impostare prima questa opzione.
4.  Selezionare l'opzione **utilizza solo colonne funzionalità** per generare un punteggio solo per le colonne contrassegnate in precedenza come funzionalità. 

    Se si deseleziona questa opzione, il modulo creerà un punteggio per ogni colonna che altrimenti soddisfa i criteri, fino al numero di colonne specificato in **numero di funzionalità desiderate**.  

5.  Per **colonna di destinazione**fare clic su **Avvia selettore di colonna** per scegliere la colonna etichetta in base al nome o al relativo indice (gli indici sono basati su uno).  

     Per tutti i metodi che coinvolgono la correlazione statistica è necessaria una colonna di etichetta. Il modulo restituisce un errore in fase di progettazione se si sceglie nessuna colonna etichetta o più colonne etichetta. 

6.  Per il **numero di funzionalità desiderate**, digitare il numero di colonne di funzioni che si desidera vengano restituite come risultato.  

     - Il numero minimo di funzionalità che è possibile specificare è 1, ma si consiglia di aumentare questo valore.  

     - Se il numero specificato di funzioni desiderate è maggiore del numero di colonne nel set di dati, vengono restituite tutte le funzionalità, anche quelle con zero punteggi.  

    - Se si specifica un numero inferiore di colonne di risultati rispetto alle colonne di funzionalità, le funzionalità vengono classificate in base al Punteggio decrescente e vengono restituite solo le funzionalità principali. 

7.  Eseguire la pipeline oppure selezionare il modulo [filtro basato sulle funzionalità di selezione](filter-based-feature-selection.md) e quindi fare clic su **Esegui**selezione.


## <a name="results"></a>Risultati

Al termine dell'elaborazione:

+ Per visualizzare un elenco completo delle colonne di funzionalità analizzate e dei relativi punteggi, fare clic con il pulsante destro del mouse sul modulo, scegliere **funzionalità**e fare clic su **Visualizza**.  

+ Per visualizzare il set di dati generato in base ai criteri di selezione delle funzionalità, fare clic con il pulsante destro del mouse sul modulo, scegliere **DataSet**e quindi fare clic su **Visualizza**. 

Se il set di dati contiene un numero di colonne inferiore al previsto, verificare le impostazioni del modulo e i tipi di dati delle colonne fornite come input. Se, ad esempio, si imposta il **numero di funzionalità desiderate** su 1, il set di dati di output conterrà solo due colonne, ovvero la colonna Label e la colonna di funzionalità con classificazione più elevata.


##  <a name="technical-notes"></a>Note tecniche  

### <a name="implementation-details"></a>Dettagli dell'implementazione

Se si usa la correlazione di Pearson su una funzionalità numerica e un'etichetta categorica, il punteggio della funzionalità viene calcolato come segue:  

1.  Per ogni livello della colonna categorica, calcolare la media condizionale della colonna numerica.  

2.  Correlare la colonna di means condizionale con la colonna numerica.  

### <a name="requirements"></a>Requisiti  

-   Non è possibile generare un punteggio di selezione delle caratteristiche per nessuna colonna designata come **etichetta** o come colonna del **Punteggio** .  

-   Se si tenta di usare un metodo di assegnazione dei punteggi con una colonna di un tipo di dati non supportato dal metodo, il modulo genererà un errore oppure verrà assegnato un punteggio pari a zero alla colonna.  

-   Se una colonna contiene valori logici (true/false), questi vengono elaborati come true = 1 e false = 0.  

-   Una colonna non può essere una funzionalità se è stata designata come **etichetta** o **Punteggio**.  

### <a name="how-missing-values-are-handled"></a>Come vengono gestiti i valori mancanti  

-   Non è possibile specificare come colonna di destinazione (etichetta) qualsiasi colonna con tutti i valori mancanti.  

-   Se una colonna contiene valori mancanti, verranno ignorati durante il calcolo del punteggio per la colonna.  

-   Se una colonna designata come colonna di funzionalità presenta tutti i valori mancanti, viene assegnato un punteggio pari a zero.   


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 

