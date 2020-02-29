---
title: Trasformazione Flat flusso di dati mapping
description: Trasformazione appiattimento del flusso di dati Azure Data Factory mapping
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164730"
---
# <a name="azure-data-factory-flatten-transformation"></a>Trasformazione Flat Azure Data Factory

La trasformazione flat può essere utilizzata per trasformare i valori di matrice all'interno di una struttura gerarchica in nuove righe, denormalizzando essenzialmente i dati.

![Casella degli strumenti trasformazione](media/data-flow/flatten5.png "Casella degli strumenti trasformazione")

![Trasformazione Flat 1](media/data-flow/flatten7.png "Trasformazione Flat 1")

## <a name="unroll-by"></a>Esegui unroll by

Prima di tutto, scegliere la colonna di matrice di cui si desidera eseguire l'unrolling e pivot.

![Impostazioni di trasformazione Flat](media/data-flow/flatten1.png "Impostazioni di trasformazione Flat")

## <a name="unroll-root"></a>Unroll radice

Per impostazione predefinita, ADF rende flat la struttura in corrispondenza dell'array di cui si è scelto di eseguire il rollback. In alternativa, è possibile scegliere una parte diversa della gerarchia da cui eseguire l'unroll. "Unroll root" è un'impostazione facoltativa.

## <a name="input-columns"></a>Colonne di input

Infine, scegliere la proiezione della nuova struttura in base ai campi in ingresso e alla colonna normalizzata di cui è stato eseguito il rollback.

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
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Passaggi successivi

* Utilizzare la [trasformazione pivot](data-flow-pivot.md) per trasformare le righe in colonne.
* Utilizzare la [trasformazione UnPivot](data-flow-unpivot.md) per trasformare colonne tramite pivot in righe.
