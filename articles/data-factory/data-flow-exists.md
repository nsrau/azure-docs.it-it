---
title: Trasformazione EXISTS nel flusso di dati del mapping Azure Data Factory | Microsoft Docs
description: Verificare la presenza di righe esistenti utilizzando la trasformazione EXISTS nel flusso di dati di mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: dfd304b0c15b325208daba104bb79863fcd3f53f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527438"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Trasformazione EXISTS nel flusso di dati di mapping

La trasformazione EXISTS è una trasformazione filtro di riga che controlla se i dati esistono in un'altra origine o in un altro flusso. Il flusso di output include tutte le righe nel flusso a sinistra che esistono o non esistono nel flusso di destra. La trasformazione EXISTS è simile a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Configurazione

Scegliere il flusso di dati che si sta controllando di esistere nell'elenco a discesa **flusso destro** .

Specificare se si desidera che i dati esistano o non esistano nell'impostazione del **tipo exist** .

Consente di scegliere le colonne chiave da confrontare con le condizioni esistenti. Per impostazione predefinita, il flusso di dati cerca l'uguaglianza tra una colonna in ogni flusso. Per eseguire il confronto tramite un valore di calcolo, passare il puntatore del mouse sull'elenco a discesa della colonna e selezionare **colonna calcolata**.

![Impostazioni exists](media/data-flow/exists.png "esistente 1")

### <a name="multiple-exists-conditions"></a>Più condizioni exists

Per confrontare più colonne da ogni flusso, aggiungere una nuova condizione exists facendo clic sull'icona a forma di segno più accanto a una riga esistente. Ogni condizione aggiuntiva viene unita in join da un'istruzione "and". Il confronto di due colonne equivale all'espressione seguente:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Espressione personalizzata

Per creare un'espressione in formato libero che contenga operatori diversi da "e" e "uguale a", selezionare il campo **espressione personalizzata** . Immettere un'espressione personalizzata tramite il generatore di espressioni del flusso di dati facendo clic sulla casella blu.

![Impostazioni personalizzate exists](media/data-flow/exists1.png "esistente personalizzato")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente è una trasformazione exists denominata `checkForChanges` che accetta il flusso sinistro `NameNorm2` e il flusso destro `TypeConversions`.  La condizione EXISTS è l'espressione `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` che restituisce true se le colonne `EMPID` e `Region` in ogni flusso corrispondono. Mentre viene verificata l'esistenza, `negate` è false. Non è abilitata alcuna trasmissione nella scheda Optimize, in modo `broadcast` `'none'` valore.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio exists](media/data-flow/exists-script.png "Esempio exists")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni simili sono [ricerca](data-flow-lookup.md) e [join](data-flow-join.md).
