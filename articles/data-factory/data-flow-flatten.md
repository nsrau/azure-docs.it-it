---
title: Trasformazione flat nel flusso di dati del mapping
description: Denormalizzare i dati gerarchici utilizzando la trasformazione Flat
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9b09771f51c8b7e6762dac23cc7390d75f47a387
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969092"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Trasformazione flat nel flusso di dati del mapping

Utilizzare la trasformazione flat per prendere i valori di matrice all'interno di strutture gerarchiche, ad esempio JSON, e per eseguirne il rollback in singole righe. Questo processo è noto come denormalizzazione.

## <a name="configuration"></a>Configurazione

La trasformazione Flat contiene le impostazioni di configurazione seguenti

![Impostazioni Flat](media/data-flow/flatten1.png "Impostazioni Flat")

### <a name="unroll-by"></a>Esegui unroll by

Selezionare una matrice da annullare. I dati di output avranno una riga per ogni elemento in ogni matrice. Se l'operazione di annullamento della matrice nella riga di input è null o vuota, sarà presente una riga di output con valori non registrati come null.

### <a name="unroll-root"></a>Unroll radice

Per impostazione predefinita, la trasformazione flat consente di eseguire il rollback di una matrice nella parte superiore della gerarchia in cui è presente. Facoltativamente, è possibile selezionare una matrice come radice di unroll. La radice di unroll deve essere una matrice di oggetti complessi che è o contiene l'oggetto unroll by Array. Se si seleziona una radice di unroll, i dati di output conterranno almeno una riga per ogni elemento nella radice di unroll. Se la riga di input non contiene elementi nella radice di unroll, verrà rilasciata dai dati di output. Se si sceglie una radice di unroll, viene sempre restituito un numero minore o uguale a quello di righe rispetto al comportamento predefinito.

### <a name="flatten-mapping"></a>Mapping Flat

Analogamente alla trasformazione Select, scegliere la proiezione della nuova struttura dai campi in ingresso e dalla matrice denormalizzata. Se viene eseguito il mapping di una matrice denormalizzata, la colonna di output sarà lo stesso tipo di dati della matrice. Se la matrice unroll by è una matrice di oggetti complessi che contiene sottomatrici, il mapping di un elemento di subarry restituirà una matrice.

Vedere la scheda controlla e l'anteprima dati per verificare l'output del mapping.

## <a name="examples"></a>Esempi

Vedere l'oggetto JSON seguente per gli esempi seguenti della trasformazione Flat

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

### <a name="no-unroll-root-with-string-array"></a>Nessuna radice di unroll con matrice di stringhe

| Esegui unroll by | Unroll radice | Proiezione |
| --------- | ----------- | ---------- |
| Goods. Customers | nessuno | name <br> Customer = Goods. Customer |

#### <a name="output"></a>Output

```
{ 'MSFT', 'government'},
{ 'MSFT', 'distributer'},
{ 'MSFT', 'retail'},
{ 'Company1', 'store'},
{ 'Company1', 'store2'},
{ 'Company2', 'Bank'},
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Nessuna radice di unroll con matrice complessa

| Esegui unroll by | Unroll radice | Proiezione |
| --------- | ----------- | ---------- |
| Goods. Orders. Spedit. orderItems | nessuno | name <br> orderId = Goods. Orders. orderId <br> ItemName = Goods. Orders. Spedit. orderItems. ItemName <br> itemQty = Goods. Orders. Spedit. orderItems. itemQty <br> Località = Località |

#### <a name="output"></a>Output

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'},
{ 'MSFT', 1, 'Charger', 2, 'Redmond'},
{ 'MSFT', 2, 'Mice', 2, 'Redmond'},
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'},
{ 'Company1', 4, 'Laptop', 20, 'Seattle'},
{ 'Company1', 4, 'Charger', 3, 'Seattle'},
{ 'Company1', 5, 'Chair', 4, 'Seattle'},
{ 'Company1', 5, 'Lamp', 2, 'Seattle'},
{ 'Company2', 4, null, null, 'Bellevue'},
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Stessa radice di unroll Array

| Esegui unroll by | Unroll radice | Proiezione |
| --------- | ----------- | ---------- |
| Goods. Orders | Goods. Orders | name <br> Goods. Orders. Spedit. orderItems. ItemName <br> Goods. Customers <br> posizione |

#### <a name="output"></a>Output

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'},
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'},
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'},
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'},
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Radice di unroll con matrice complessa

| Esegui unroll by | Unroll radice | Proiezione |
| --------- | ----------- | ---------- |
| Goods. Orders. Spedit. orderItem | Goods. Orders |name <br> orderId = Goods. Orders. orderId <br> ItemName = Goods. Orders. Spedit. orderItems. ItemName <br> itemQty = Goods. Orders. Spedit. orderItems. itemQty <br> Località = Località |

#### <a name="output"></a>Output

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'},
{ 'MSFT', 1, 'Charger', 2, 'Redmond'},
{ 'MSFT', 2, 'Mice', 2, 'Redmond'},
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'},
{ 'Company1', 4, 'Laptop', 20, 'Seattle'},
{ 'Company1', 4, 'Charger', 3, 'Seattle'},
{ 'Company1', 5, 'Chair', 4, 'Seattle'},
{ 'Company1', 5, 'Lamp', 2, 'Seattle'},
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

* Utilizzare la [trasformazione pivot](data-flow-pivot.md) per trasformare le righe in colonne.
* Utilizzare la [trasformazione UnPivot](data-flow-unpivot.md) per trasformare colonne tramite pivot in righe.
