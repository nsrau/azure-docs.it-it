---
title: Trasformazione Colonna derivata nel flusso di dati per mapping
description: Informazioni su come trasformare i dati su larga scala in Azure Data Factory con la trasformazione Colonna derivata nel flusso di dati per mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532029"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Trasformazione Colonna derivata nel flusso di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione Colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

## <a name="create-and-update-columns"></a>Creazione e aggiornamento di colonne

Quando si crea una colonna derivata, è possibile generare una nuova colonna o aggiornarne una esistente. Nella casella di testo **Column** immettere nella colonna che si sta creando. Per eseguire l'override di una colonna esistente nello schema, è possibile utilizzare l'elenco a discesa della colonna. Per compilare l'espressione della colonna derivata, fare clic sulla casella di testo **immettere l'espressione** . È possibile iniziare a digitare l'espressione o aprire il generatore di espressioni per costruire la logica.

![Impostazioni della colonna derivata](media/data-flow/create-derive-column.png "Impostazioni della colonna derivata")

Per aggiungere altre colonne derivate, fare clic su **Aggiungi** sopra l'elenco di colonne o sull'icona del segno più accanto a una colonna derivata esistente. Scegliere **Aggiungi colonna** o **Aggiungi criteri di ricerca colonna**.

![Selezione di una nuova colonna derivata](media/data-flow/add-derived-column.png "Selezione di una nuova colonna derivata")

### <a name="column-patterns"></a>Criteri delle colonne

Nei casi in cui lo schema non è definito in modo esplicito o se si desidera aggiornare un set di colonne in blocco, è necessario creare i profilatori della colonna. I criteri di colonna consentono di associare colonne utilizzando regole basate sui metadati della colonna e di creare colonne derivate per ogni colonna corrispondente. Per ulteriori informazioni, vedere la pagina relativa [alla creazione di modelli di colonna](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) nella trasformazione colonna derivata.

![Criteri delle colonne](media/data-flow/column-pattern-derive.png "Criteri delle colonne")

## <a name="building-schemas-using-the-expression-builder"></a>Compilazione di schemi mediante il generatore di espressioni

Quando si utilizza il generatore di [espressioni](concepts-data-flow-expression-builder.md)del flusso di dati di mapping, è possibile creare, modificare e gestire le colonne derivate nella sezione **colonne derivate** . Vengono elencate tutte le colonne create o modificate nella trasformazione. Scegliere in modo interattivo la colonna o il modello che si sta modificando facendo clic sul nome della colonna. Per aggiungere una colonna aggiuntiva, selezionare **Crea nuovo** e scegliere se si desidera aggiungere una singola colonna o un modello.

![Crea nuova colonna](media/data-flow/derive-add-column.png "Crea nuova colonna")

Quando si utilizzano colonne complesse, è possibile creare sottocolonne. A tale scopo, fare clic sull'icona con il segno più accanto a una colonna e selezionare **Aggiungi sottocolonna**. Per altre informazioni sulla gestione di tipi complessi nel flusso di dati, vedere [Gestione del formato JSON nel flusso di dati per mapping](format-json.md#mapping-data-flow-properties).

![Aggiungi sottocolonna](media/data-flow/derive-add-subcolumn.png "Aggiungi sottocolonna")

Per altre informazioni sulla gestione di tipi complessi nel flusso di dati, vedere [Gestione del formato JSON nel flusso di dati per mapping](format-json.md#mapping-data-flow-properties).

![Aggiungi colonna complessa](media/data-flow/derive-complex-column.png "Aggiungi colonne")

### <a name="locals"></a>Variabili locali

Se si condivide la logica tra più colonne o si desidera compartimenti la logica, è possibile creare un oggetto locale all'interno di una trasformazione colonna derivata. Un oggetto locale è un set di logica che non viene propagato a valle alla trasformazione seguente. È possibile creare variabili locali all'interno del generatore di espressioni passando a **elementi espressione** e selezionando **variabili locali**. Crearne uno nuovo selezionando **Crea nuovo**.

![Crea locale](media/data-flow/create-local.png "Crea locale")

Le variabili locali possono fare riferimento a qualsiasi elemento Expression una colonna derivata, incluse funzioni, schema di input, parametri e altre variabili locali. Quando si fa riferimento ad altre variabili locali, l'ordine è importante perché il locale a cui si fa riferimento deve essere "sopra" quello corrente.

![Creazione locale 2](media/data-flow/create-local-2.png "Creazione locale 2")

Per fare riferimento a un oggetto locale in una colonna derivata, fare clic sull'elemento local dalla visualizzazione **elementi espressione** oppure fare riferimento a esso con i due punti davanti al nome. Ad esempio, è possibile fare riferimento a un local1 locale chiamato da `:local1` . Per modificare una definizione locale, passare il puntatore del mouse su di essa nella visualizzazione elementi espressione e fare clic sull'icona della matita.

![Uso di variabili locali](media/data-flow/using-locals.png "Uso di variabili locali")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente rappresenta una colonna derivata denominata `CleanData` che accetta un flusso in ingresso `MoviesYear` e crea due colonne derivate. La prima colonna derivata sostituisce la colonna `Rating` con un valore di valutazione come tipo Integer. La seconda colonna derivata è un modello a cui corrisponde ogni colonna il cui nome inizia con "movies". Per ogni colonna corrispondente, viene creata una colonna `movie` uguale al valore della colonna corrispondente con il prefisso ' movie_'. 

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di derivazione](media/data-flow/derive-script.png "Esempio di derivazione")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Linguaggio delle espressioni del flusso di dati per mapping](data-flow-expression-functions.md).
