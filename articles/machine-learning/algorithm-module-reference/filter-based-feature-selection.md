---
title: 'Selezione feature basata su filtro: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Selezione funzionalità basata su filtro in Azure Machine Learning per identificare le funzionalità in un set di dati con la massima potenza predittiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477596"
---
# <a name="filter-based-feature-selection"></a>Filter Based Feature Selection

Questo articolo descrive come usare il modulo Selezione funzionalità basata su filtro nella finestra di progettazione di Azure Machine Learning (anteprima). Questo modulo consente di identificare le colonne nel set di dati di input che hanno la massima potenza predittiva. 

In generale, la selezione delle funzionalità si *riferisce* al processo di applicazione di test statistici agli input, dato un output specificato. L'obiettivo è determinare quali colonne sono più predittive dell'output. Il modulo Selezione funzionalità basata su filtro fornisce più algoritmi di selezione delle funzionalità tra cui scegliere. Il modulo include metodi di correlazione come la correlazione di Pearson e i valori del chi quadrato. 

Quando si utilizza il modulo Selezione funzionalità basata su filtro, si fornisce un set di dati e si identifica la colonna che contiene l'etichetta o la variabile dipendente. Specificare quindi un singolo metodo da utilizzare per misurare l'importanza della feature.

Il modulo restituisce un set di dati che contiene le migliori colonne di funzionalità, classificate in base alla potenza predittiva. Restituisce anche i nomi delle feature e i relativi punteggi dalla metrica selezionata.  

## <a name="what-filter-based-feature-selection-is"></a>Che cos'è la selezione delle funzionalità basate su filtri  

Questo modulo per la selezione delle funzionalità è denominato "basato su filtro" perché si utilizza la metrica selezionata per trovare gli attributi irrilevanti. È quindi possibile filtrare le colonne ridondanti dal modello. È possibile scegliere una singola misura statistica adatta ai dati e il modulo calcola un punteggio per ogni colonna di entità geografiche. Le colonne vengono restituite classificate in base ai punteggi delle funzionalità. 

La scelta delle funzioni appropriate può permettere di migliorare la precisione e l'efficienza della classificazione. 

In genere si usano solo le colonne con i punteggi migliori per creare il modello predittivo. Le colonne con punteggi di selezione delle funzionalità insufficienti possono essere lasciate nel set di dati e ignorate quando si compila un modello.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Come scegliere una metrica di selezione delle feature

Il modulo Selezione funzionalità basata su filtro fornisce una varietà di metriche per la valutazione del valore delle informazioni in ogni colonna. Questa sezione fornisce una descrizione generale di ogni metrica e come viene applicata. È possibile trovare requisiti aggiuntivi per l'utilizzo di ogni metrica nelle [note tecniche](#technical-notes) e nelle [istruzioni](#how-to-configure-filter-based-feature-selection) per la configurazione di ogni modulo.

-   **Correlazione di Pearson**  

    La statistica di correlazione di Pearson, o coefficiente di correlazione `r` di Pearson, è nota anche nei modelli statistici come valore. Per due variabili qualsiasi restituisce un valore che indica la forza della correlazione.

    Il coefficiente di correlazione di Pearson viene calcolato a partire dalla covarianza di due variabili e dalla divisione del prodotto delle rispettive deviazioni standard. Le modifiche di scala nelle due variabili non influiscono sul coefficiente.  

-   **Chi al quadrato**  

    Il test del chi quadrato a due vie è un metodo statistico che consente di misurare la distanza tra i valori previsti e i risultati effettivi. Il metodo presuppone che le variabili siano casuali e derivate da un adeguato campione di variabili indipendenti. La risultante statistica del chi quadrato indica la distanza dei risultati ottenuti da quelli previsti (casuali).  


> [!TIP]
> Se è necessaria un'opzione diversa per il metodo di selezione delle funzionalità personalizzate, utilizzare il modulo [Esegui script R.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Come configurare la selezione di funzionalità basate su filtri

Scegli una metrica statistica standard. Il modulo calcola la correlazione tra una coppia di colonne: la colonna label e una colonna di entità geografiche.

1.  Aggiungere il modulo Selezione funzionalità basata su filtro alla pipeline. È possibile trovarlo nella categoria **Selezione funzionalità** nella finestra di progettazione.

2. Connettere un set di dati di input che contiene almeno due colonne che sono potenziali funzionalità.  

    Per assicurarsi che una colonna venga analizzata e che venga generato un punteggio di feature, utilizzare il modulo [Modifica metadati](edit-metadata.md) per impostare l'attributo **IsFeature.** 

    > [!IMPORTANT]
    > Assicurarsi che le colonne che si stanno fornendo come input siano potenziali funzionalità. Ad esempio, una colonna che contiene un singolo valore non ha alcun valore di informazione.
    >
    > Se si sa che alcune colonne renderebbero caratteristiche non valido, è possibile rimuoverle dalla selezione delle colonne. È inoltre possibile utilizzare il modulo [Modifica metadati](edit-metadata.md) per contrassegnarli come **categorici**. 
3.  Per **Metodo punteggio caratteristica**, scegliere uno dei seguenti metodi statistici stabiliti da utilizzare nel calcolo dei punteggi.  

    | Metodo              | Requisiti                             |
    | ------------------- | ---------------------------------------- |
    | Correlazione di Pearson | L'etichetta può essere testo o numerica. Le entità geografiche devono essere numeriche. |
    Chi al quadrato| Le etichette e le caratteristiche possono essere di testo o numeriche. Utilizzare questo metodo per calcolare l'importanza delle funzionalità per due colonne di categoria.|

    > [!TIP]
    > Se si modifica la metrica selezionata, tutte le altre selezioni verranno reimpostate. Quindi assicuratevi di impostare questa opzione prima.
4.  Selezionare l'opzione **Attiva solo sulle colonne delle feature** per generare un punteggio solo per le colonne precedentemente contrassegnate come feature. 

    Se si deseleziona questa opzione, il modulo creerà una partitura per qualsiasi colonna che altrimenti soddisfa i criteri, fino al numero di colonne specificato in **Numero di feature desiderate.**  

5.  Per **Colonna di destinazione**, selezionare **Selettore di colonna di avvio** per scegliere la colonna dell'etichetta in base al nome o al relativo indice. Gli indici sono a base uno.  
    Una colonna etichetta è necessaria per tutti i metodi che implicano la correlazione statistica. Il modulo restituisce un errore in fase di progettazione se non si sceglie alcuna colonna etichetta o più colonne di etichette. 

6.  In **Numero di feature desiderate,** immettere il numero di colonne di entità geografiche che si desidera vengano restituite come risultato:  

    - Il numero minimo di feature che è possibile specificare è uno, ma è consigliabile aumentare questo valore.  

    - Se il numero specificato di funzioni desiderate è superiore al numero di colonne disponibili nel set di dati, verranno restituite tutte le funzioni. Vengono restituite anche le funzioni con punteggi pari a zero.  

    - Se si specificano meno colonne dei risultati rispetto alle colonne di entità geografiche, le feature vengono classificate in base al punteggio decrescente. Vengono restituite solo le funzioni principali. 

7.  Inviare la pipeline oppure selezionare il modulo Selezione funzionalità basata su filtro e quindi selezionare **Esegui selezionato**.


## <a name="results"></a>Risultati

Al termine dell'elaborazione:

+ Per visualizzare un elenco completo delle colonne delle feature analizzate e dei relativi punteggi, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza**.  

+ Per visualizzare il set di dati in base ai criteri di selezione delle feature, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza**. 

Se il set di dati contiene meno colonne del previsto, controllare le impostazioni del modulo. Controllare anche i tipi di dati delle colonne fornite come input. Ad esempio, se si imposta **Numero di feature desiderate** su 1, il set di dati di output contiene solo due colonne: la colonna label e la colonna della feature più classificata.


##  <a name="technical-notes"></a>Note tecniche  

### <a name="implementation-details"></a>Dettagli dell'implementazione

Se si utilizza la correlazione di Pearson su una feature numerica e un'etichetta di categoria, il punteggio della feature viene calcolato come segue:  

1.  Per ogni livello nella colonna di categoria, calcolare la media condizionale della colonna numerica.  

2.  Correlare la colonna di mezzi condizionali con la colonna numerica.  

### <a name="requirements"></a>Requisiti  

-   Non è possibile generare un punteggio di selezione delle entità geografiche per qualsiasi colonna designata come colonna **Etichetta** o **Punteggio.**  

-   Se si tenta di utilizzare un metodo di assegnazione del punteggio con una colonna di un tipo di dati non supportato dal metodo, il modulo genererà un errore. In alternativa, alla colonna verrà assegnato un punteggio pari a zero.  

-   Se una colonna contiene valori logici (true/false), vengono elaborati come `True = 1` e `False = 0`.  

-   Una colonna non può essere una funzione se è stata designata come **etichetta** o **punteggio**.  

### <a name="how-missing-values-are-handled"></a>Modalità di gestione dei valori mancanti  

-   Non è possibile specificare come colonna di destinazione (etichetta) qualsiasi colonna con tutti i valori mancanti.  

-   Se una colonna contiene valori mancanti, il modulo li ignora quando calcola il punteggio per la colonna.  

-   Se una colonna designata come colonna di entità geografiche contiene tutti i valori mancanti, il modulo assegna un punteggio pari a zero.   


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

