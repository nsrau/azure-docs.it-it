---
title: Concetti JSON del flusso di dati del mapping Azure Data Factory
description: Data Factory flusso di dati di mapping include funzionalità predefinite per la gestione di documenti JSON con gerarchie
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 69dce46052c18eec7c3f1fa2082907ed62b367d6
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703335"
---
# <a name="mapping-data-flow-json-handling"></a>Mapping della gestione JSON del flusso di dati

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>Creazione di strutture JSON nell'editor espressioni
### <a name="derived-column-transformation"></a>Trasformazione colonna derivata
L'aggiunta di una colonna complessa al flusso di dati è più semplice tramite l'editor espressioni colonna derivata. Dopo aver aggiunto una nuova colonna e aperto l'editor, sono disponibili due opzioni: immettere manualmente la struttura JSON o usare l'interfaccia utente per aggiungere le sottocolonne in modo interattivo.

#### <a name="interactive-ui-json-design"></a>Progettazione JSON interattiva dell'interfaccia utente
Dal riquadro lato schema di output è possibile aggiungere nuove sottocolonne usando il menu `+`: ![Aggiungi sottocolonna](media/data-flow/addsubcolumn.png "Aggiungi") sottocolonna

Da questa posizione è possibile aggiungere nuove colonne e sottocolonne nello stesso modo. Per ogni campo non complesso, è possibile aggiungere un'espressione nell'editor di espressioni a destra.

![](media/data-flow/complexcolumn.png "Colonna") complessa colonna complessa

#### <a name="manual-json-design"></a>Progettazione JSON manuale
Per aggiungere manualmente una struttura JSON, aggiungere una nuova colonna e immettere l'espressione nell'editor. L'espressione segue il formato generale seguente:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Se questa espressione è stata immessa per una colonna denominata "complexColumn", viene scritta nel sink come il seguente codice JSON:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Script manuale di esempio per la definizione gerarchica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Opzioni del formato di origine
### <a name="default"></a>Predefinito
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Documento singolo
* Opzione uno
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Opzione due
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Nomi di colonna non racchiusi tra virgolette
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Con commenti
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Virgoletta singola
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Barra rovesciata con escape
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funzioni di ordine superiore
## <a name="filter"></a>filtro
Filtra gli elementi dalla matrice che non soddisfano il predicato specificato. Il filtro prevede un riferimento a un elemento nella funzione predicato come #item.

### <a name="examples"></a>Esempi
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Esegue il mapping di ogni elemento della matrice a un nuovo elemento usando l'espressione fornita. Map prevede un riferimento a un elemento nella funzione Expression come #item.

### <a name="examples"></a>Esempi
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>Ridurre
Accumula gli elementi in una matrice. Per reduce si prevede un riferimento a un accumulatore e un elemento nella prima funzione di espressione come #acc e #item e si prevede che il valore risultante sia #result da utilizzare nella seconda funzione di espressione.

### <a name="examples"></a>Esempi
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
Ordina la matrice utilizzando la funzione di predicato fornita. Sort prevede un riferimento a due elementi consecutivi nella funzione Expression come #item1 e #item2.

### <a name="examples"></a>Esempi
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contiene
Restituisce true se qualsiasi elemento nella matrice fornita restituisce true nel predicato fornito. Contains prevede un riferimento a un elemento nella funzione predicato come #item.

### <a name="examples"></a>Esempi
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare la trasformazione colonna derivata per compilare le strutture gerarchiche](data-flow-derived-column.md)
