---
title: Appiattire la trasformazione nel mapping del flusso di datiAppiattite transformation in mapping data flow
description: Denormalizzare i dati gerarchici utilizzando la trasformazione appiattitaDenormalize hierarchical data using the flatten transformation
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: b19aae8ab6730936a826f5bb069bfdb7d696cdfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246637"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Appiattire la trasformazione nel mapping del flusso di datiAppiattite transformation in mapping data flow

Usare la trasformazione appiattisci per inserire valori di matrice all'interno di strutture gerarchiche come JSON e srotolarli in singole righe. Questo processo è noto come denormalizzazione.

## <a name="configuration"></a>Configurazione

La trasformazione appiattisci contiene le seguenti impostazioni di configurazione

![Impostazioni di appiattire](media/data-flow/flatten1.png "Impostazioni di appiattire")

### <a name="unroll-by"></a>Srotola da

Selezionare una matrice da srotolare. I dati di output avranno una riga per elemento in ogni matrice. Se l'annullamento del roll per matrice nella riga di input è null o vuoto, sarà presente una riga di output con valori srotolati come null.

### <a name="unroll-root"></a>Annulla radice roll

Per impostazione predefinita, la trasformazione appiattisci annulla l'implementazione di una matrice all'inizio della gerarchia in cui è presente. Facoltativamente, è possibile selezionare una matrice come radice di annullamento del roll. La radice di annullamento del roll deve essere una matrice di oggetti complessi che è o contiene l'annullamento del roll per matrice. Se è selezionata una radice di annullamento del roll, i dati di output conterranno almeno una riga per elemento nella radice di annullamento del roll. Se la riga di input non ha elementi nella radice di annullamento del roll, verrà eliminata dai dati di output. La scelta di una radice di annullamento del roll restituisce sempre un numero di righe inferiore o uguale rispetto al comportamento predefinito.

### <a name="flatten-mapping"></a>Mappatura appiattita

Simile alla trasformazione di selezione, scegliere la proiezione della nuova struttura dai campi in entrata e dalla matrice denormalizzata. Se viene eseguito il mapping di una matrice denormalizzata, la colonna di output sarà dello stesso tipo di dati della matrice. Se l'annullamento del rollperazione per matrice è una matrice di oggetti complessi che contiene sottomatrici, il mapping di un elemento di tale subarry genererà una matrice.

Fare riferimento alla scheda di ispezione e all'anteprima dei dati per verificare l'output del mapping.

## <a name="examples"></a>Esempi

Fare riferimento all'oggetto JSON seguente per gli esempi seguenti della trasformazione appiattisci

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Nessuna radice di annullamento del rollo con matrice di stringheNo unroll root with string array

| Srotola da | Annulla radice roll | Proiezione |
| --------- | ----------- | ---------- |
| beni.clienti | nessuno | name <br> cliente - beni.cliente |

#### <a name="output"></a>Output

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Nessuna radice di annullamento del rollcone

| Srotola da | Annulla radice roll | Proiezione |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | nessuno | name <br> orderId - beni.ordini.orderId <br> itemName : goods.orders.shipped.orderItems.itemName <br> itemQty - goods.orders.shipped.orderItems.itemQty <br> posizione e posizione |

#### <a name="output"></a>Output

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Stessa radice della matrice di srotolato

| Srotola da | Annulla radice roll | Proiezione |
| --------- | ----------- | ---------- |
| beni.ordini | beni.ordini | name <br> goods.orders.shipped.orderItems.itemName <br> beni.clienti <br> posizione |

#### <a name="output"></a>Output

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Annulla radice di rollio con array complesso

| Srotola da | Annulla radice roll | Proiezione |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | beni.ordini |name <br> orderId - beni.ordini.orderId <br> itemName : goods.orders.shipped.orderItems.itemName <br> itemQty - goods.orders.shipped.orderItems.itemQty <br> posizione e posizione |

#### <a name="output"></a>Output

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Esempio

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Passaggi successivi

* Utilizzare la [trasformazione Pivot](data-flow-pivot.md) per eseguire il pivot delle righe in colonne.
* Utilizzare la [trasformazione Unpivot](data-flow-unpivot.md) per eseguire il pivot delle colonne in righe.
