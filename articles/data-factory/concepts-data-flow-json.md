---
title: Uso di JSON in un flusso di dati di mapping in Azure Data Factory
description: Azure Data Factory flusso di dati di mapping include funzionalità predefinite per la gestione di documenti JSON con gerarchie
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: fe412e9e682fb55f1664c546e6b6c5a347527adb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387352"
---
# <a name="mapping-data-flow-json-handling"></a>Mapping della gestione JSON del flusso di dati

## <a name="creating-json-structures-in-derived-column"></a>Creazione di strutture JSON in una colonna derivata

È possibile aggiungere una colonna complessa al flusso di dati tramite il generatore di espressioni della colonna derivata. Nella trasformazione colonna derivata aggiungere una nuova colonna e aprire il generatore di espressioni facendo clic sulla casella blu. Per rendere complessa una colonna, è possibile immettere la struttura JSON manualmente o usare l'esperienza utente per aggiungere le sottocolonne in modo interattivo.

### <a name="using-the-expression-builder-ux"></a>Uso del generatore di espressioni UX

Nel riquadro lato schema di output posizionare il puntatore del mouse su una colonna e fare clic sull'icona con il segno più. Selezionare **Aggiungi sottocolonna** per rendere la colonna un tipo complesso.

![Aggiungi sottocolonna](media/data-flow/addsubcolumn.png "Aggiungi sottocolonna")

È possibile aggiungere colonne e sottocolonne aggiuntive nello stesso modo. Per ogni campo non complesso, è possibile aggiungere un'espressione nell'editor di espressioni a destra.

![Colonna complessa](media/data-flow/complexcolumn.png "Colonna complessa")

### <a name="entering-the-json-structure-manually"></a>Immissione manuale della struttura JSON

Per aggiungere manualmente una struttura JSON, aggiungere una nuova colonna e immettere l'espressione nell'editor. L'espressione segue il formato generale seguente:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se questa espressione è stata immessa per una colonna denominata "complexColumn", viene scritta nel sink come il JSON seguente:

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

L'uso di un set di dati JSON come origine nel flusso di dati consente di impostare cinque impostazioni aggiuntive. Queste impostazioni sono disponibili nella scheda **Opzioni di origine** del file **JSON Settings** Accordion.  

![Impostazioni JSON](media/data-flow/json-settings.png "Impostazioni JSON")

### <a name="default"></a>Predefinito

Per impostazione predefinita, i dati JSON vengono letti nel formato seguente.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Documento singolo

Se è selezionato un **singolo documento** , il mapping dei flussi di dati legge un documento JSON da ogni file. 

``` json
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

Se è selezionato **nomi di colonna non racchiusi tra virgolette** , il mapping dei flussi di dati legge le colonne JSON che non sono racchiuse tra virgolette 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Con commenti

Select **ha commenti** se i dati JSON sono di tipo C++ C o con commenti di stile.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Virgoletta singola

Selezionare **singolo racchiuso tra** virgolette se i campi e i valori JSON usano virgolette singole anziché virgolette doppie.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Barra rovesciata con escape

Selezionare **singolo racchiuso tra virgolette** se le barre rovesciate vengono usate per usare caratteri di escape nei dati JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funzioni di ordine superiore

Una funzione di ordine superiore è una funzione che accetta una o più funzioni come argomento. Di seguito è riportato un elenco di funzioni di ordine superiore supportate nel mapping di flussi di dati che consentono operazioni di matrici.

### <a name="filter"></a>filter
Filtra gli elementi dalla matrice che non soddisfano il predicato specificato. Il filtro prevede un riferimento a un elemento nella funzione predicato come #item.

#### <a name="examples"></a>esempi
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Esegue il mapping di ogni elemento della matrice a un nuovo elemento usando l'espressione fornita. Map prevede un riferimento a un elemento nella funzione Expression come #item.

#### <a name="examples"></a>esempi
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>ridurre
Accumula gli elementi in una matrice. Per reduce si prevede un riferimento a un accumulatore e un elemento nella prima funzione di espressione come #acc e #item e si prevede che il valore risultante sia #result da utilizzare nella seconda funzione di espressione.

#### <a name="examples"></a>esempi
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sort
Ordina la matrice utilizzando la funzione di predicato fornita. Sort prevede un riferimento a due elementi consecutivi nella funzione Expression come #item1 e #item2.

#### <a name="examples"></a>esempi
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
Restituisce true se qualsiasi elemento nella matrice fornita restituisce true nel predicato fornito. Contains prevede un riferimento a un elemento nella funzione predicato come #item.

#### <a name="examples"></a>esempi
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare la trasformazione colonna derivata per compilare le strutture gerarchiche](data-flow-derived-column.md)
