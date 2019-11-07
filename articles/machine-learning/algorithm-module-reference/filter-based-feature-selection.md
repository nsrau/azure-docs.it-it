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
ms.openlocfilehash: f8ae4982f7602cd7403a78517fec79fa95d33aa2
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717212"
---
# <a name="filter-based-feature-selection"></a>Selezione delle funzioni basata su filtro

Questo articolo descrive come usare il modulo Filter based feature selection in Azure Machine Learning Designer (Preview). Questo modulo consente di identificare le colonne nel set di dati di input con la massima potenza predittiva. 

In generale, la *selezione delle caratteristiche* si riferisce al processo di applicazione dei test statistici agli input, dato un output specificato. L'obiettivo è determinare quali colonne sono più predittive dell'output. Il modulo filtro basato sulle funzionalità di selezione offre più algoritmi di selezione delle funzioni tra cui scegliere. Il modulo include metodi di correlazione come la correlazione di Pearson e i valori chi quadrato. 

Quando si usa il modulo di selezione delle caratteristiche basato su filtro, si specifica un set di dati e si identifica la colonna contenente l'etichetta o la variabile dipendente. Si specifica quindi un singolo metodo da usare per misurare l'importanza della funzionalità.

Il modulo restituisce un set di dati che contiene le colonne di funzionalità migliori, classificate in base alla potenza predittiva. Restituisce anche i nomi delle funzionalità e i relativi punteggi dalla metrica selezionata.  

## <a name="what-filter-based-feature-selection-is"></a>Selezione delle caratteristiche basate su filtro  

Questo modulo per la selezione delle funzioni è denominato "basato su filtri" perché si usa la metrica selezionata per trovare attributi irrilevanti. È quindi possibile filtrare le colonne ridondanti dal modello. È possibile scegliere una singola misura statistica adatta ai dati e il modulo calcola un punteggio per ogni colonna della funzionalità. Le colonne vengono restituite in base ai punteggi delle funzionalità. 

Scegliendo le funzionalità appropriate, è possibile migliorare la precisione e l'efficienza della classificazione. 

In genere si utilizzano solo le colonne con i punteggi migliori per compilare il modello predittivo. Le colonne con punteggi di selezione delle funzionalità non sufficienti possono essere lasciate nel set di dati e ignorate quando si compila un modello.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Come scegliere una metrica di selezione delle funzionalità

Il modulo Filter-based feature selection fornisce un'ampia gamma di metriche per la valutazione del valore delle informazioni in ogni colonna. Questa sezione fornisce una descrizione generale di ogni metrica e come viene applicata. È possibile trovare ulteriori requisiti per l'utilizzo di ogni metrica nelle [Note tecniche](#technical-notes) e nelle [istruzioni](#how-to-configure-filter-based-feature-selection) per la configurazione di ogni modulo.

-   **Correlazione di Pearson**  

    Le statistiche di correlazione di Pearson, o il coefficiente di correlazione di Pearson, sono note anche come valore `r` nei modelli statistici. Per due variabili qualsiasi, restituisce un valore che indica il livello di attendibilità della correlazione.

    Il coefficiente di correlazione di Pearson viene calcolato prendendo la covarianza di due variabili e dividendo in base al prodotto della relativa deviazione standard. Le modifiche alla scala nelle due variabili non influiscono sul coefficiente.  

-   **Chi quadrato**  

    Il test bidirezionale di chi quadrato è un metodo statistico che misura il modo in cui i valori previsti sono vicini ai risultati effettivi. Il metodo presuppone che le variabili siano casuali e disegnate da un campione appropriato di variabili indipendenti. La statistica del chi quadrato risultante indica la distanza dei risultati dal risultato previsto (casuale).  


> [!TIP]
> Se è necessaria un'opzione diversa per il metodo di selezione delle funzioni personalizzate, usare il modulo [Execute R script](execute-r-script.md) . 

## <a name="how-to-configure-filter-based-feature-selection"></a>Come configurare la selezione delle funzioni basata su filtro

Si sceglie una metrica statistica standard. Il modulo calcola la correlazione tra una coppia di colonne, ovvero la colonna Label e una colonna feature.

1.  Aggiungere il modulo Filter-based Feature Selection alla pipeline. È possibile trovarlo nella categoria **Selezione funzionalità** nella finestra di progettazione.

2. Connettere un set di dati di input contenente almeno due colonne che rappresentano funzionalità potenziali.  

    Per assicurarsi che una colonna venga analizzata e che venga generato un punteggio di funzionalità, utilizzare il modulo [Modifica metadati](edit-metadata.md) per impostare l'attributo della **funzionalità** . 

    > [!IMPORTANT]
    > Verificare che le colonne fornite come input siano funzionalità potenziali. Una colonna che contiene un singolo valore, ad esempio, non ha un valore di informazioni.
    >
    > Se si è certi che alcune colonne creeranno funzionalità non valide, è possibile rimuoverle dalla selezione della colonna. È anche possibile usare il modulo [Modifica metadati](edit-metadata.md) per contrassegnarli come **categorici**. 
3.  Per il metodo di assegnazione dei punteggi delle **funzionalità**, scegliere uno dei seguenti metodi statistici stabiliti da usare per il calcolo dei punteggi.  

    | Metodo              | Requisiti                             |
    | ------------------- | ---------------------------------------- |
    | Correlazione di Pearson | Label può essere di testo o numerico. Le funzionalità devono essere numeriche. |
    Chi quadrato| Le etichette e le funzionalità possono essere di testo o numerici. Utilizzare questo metodo per calcolare l'importanza della funzionalità per due colonne categoriche.|

    > [!TIP]
    > Se si modifica la metrica selezionata, verranno reimpostate tutte le altre selezioni. Assicurarsi prima di tutto di impostare questa opzione.
4.  Selezionare l'opzione **utilizza solo colonne funzionalità** per generare un punteggio solo per le colonne contrassegnate in precedenza come funzionalità. 

    Se si deseleziona questa opzione, il modulo creerà un punteggio per ogni colonna che altrimenti soddisfa i criteri, fino al numero di colonne specificato in **numero di funzionalità desiderate**.  

5.  Per **colonna di destinazione**selezionare **Avvia selettore di colonna** per scegliere la colonna etichetta in base al nome o al relativo indice. Gli indici sono in base uno.  
    Per tutti i metodi che coinvolgono la correlazione statistica è necessaria una colonna di etichetta. Il modulo restituisce un errore in fase di progettazione se si sceglie nessuna colonna etichetta o più colonne etichetta. 

6.  Per il **numero di funzionalità desiderate**, immettere il numero di colonne di funzioni che si desidera vengano restituite come risultato:  

    - Il numero minimo di funzionalità che è possibile specificare è uno, ma si consiglia di aumentare questo valore.  

    - Se il numero specificato di funzioni desiderate è maggiore del numero di colonne nel set di dati, vengono restituite tutte le funzionalità. Vengono restituite anche le funzionalità con zero punteggi.  

    - Se si specifica un numero inferiore di colonne di risultati rispetto alle colonne di funzionalità, le funzionalità vengono classificate in base al Punteggio decrescente. Vengono restituite solo le funzionalità principali. 

7.  Eseguire la pipeline oppure selezionare il modulo filtro basato sulle funzionalità di selezione e quindi selezionare **Esegui**selezione.


## <a name="results"></a>Risultati

Al termine dell'elaborazione:

+ Per visualizzare un elenco completo delle colonne di funzionalità analizzate e dei relativi punteggi, fare clic con il pulsante destro del mouse sul modulo. Selezionare **funzionalità**, quindi selezionare **Visualizza**.  

+ Per visualizzare il set di dati generato in base ai criteri di selezione delle funzionalità, fare clic con il pulsante destro del mouse sul modulo. Selezionare **DataSet**, quindi selezionare **Visualizza**. 

Se il set di dati contiene un numero di colonne inferiore al previsto, controllare le impostazioni del modulo. Controllare inoltre i tipi di dati delle colonne fornite come input. Se, ad esempio, si imposta il **numero di funzionalità desiderate** su 1, il set di dati di output conterrà solo due colonne, ovvero la colonna Label e la colonna di funzionalità con classificazione più elevata.


##  <a name="technical-notes"></a>Note tecniche  

### <a name="implementation-details"></a>Dettagli dell'implementazione

Se si usa la correlazione di Pearson su una funzionalità numerica e un'etichetta categorica, il punteggio della funzionalità viene calcolato come segue:  

1.  Per ogni livello della colonna categorica, calcolare la media condizionale della colonna numerica.  

2.  Correlare la colonna di means condizionale con la colonna numerica.  

### <a name="requirements"></a>Requisiti  

-   Non è possibile generare un punteggio di selezione delle funzionalità per nessuna colonna designata come colonna **etichetta** o **Punteggio** .  

-   Se si tenta di usare un metodo di assegnazione dei punteggi con una colonna di un tipo di dati che il metodo non supporta, il modulo genererà un errore. In alternativa, alla colonna verrà assegnato un punteggio pari a zero.  

-   Se una colonna contiene valori logici (true/false), essi vengono elaborati come `True = 1` e `False = 0`.  

-   Una colonna non può essere una funzionalità se è stata designata come **etichetta** o **Punteggio**.  

### <a name="how-missing-values-are-handled"></a>Come vengono gestiti i valori mancanti  

-   Non è possibile specificare come colonna di destinazione (etichetta) qualsiasi colonna con tutti i valori mancanti.  

-   Se una colonna contiene valori mancanti, il modulo li ignora quando viene calcolato il punteggio per la colonna.  

-   Se una colonna designata come colonna di funzionalità presenta tutti i valori mancanti, il modulo assegna un punteggio pari a zero.   


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per il servizio Azure Machine Learning. 

