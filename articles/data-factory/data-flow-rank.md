---
title: Trasformazione classificazione nel flusso di dati del mapping
description: Come usare la trasformazione del rango del flusso di dati del mapping di Azure Data Factory generare una colonna di rango
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: 8584d1b64191cc5258c6eeeef9ae4125bf1a2c65
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044735"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Trasformazione classificazione nel flusso di dati del mapping 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione classificazione per generare una classificazione ordinata in base alle condizioni di ordinamento specificate dall'utente. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configurazione

![Impostazioni di rango](media/data-flow/rank-configuration.png "Impostazioni di rango")

Senza **distinzione tra maiuscole e minuscole:** Se una colonna di ordinamento è di tipo stringa, la distinzione tra maiuscole e minuscole verrà calcolata in rango. 

**Dense:** Se abilitata, la colonna Rank verrà classificata come dense. Ogni conteggio di rango sarà un numero consecutivo e i valori di rango non verranno ignorati dopo un vincolo.

**Colonna rango:** Nome della colonna di rango generata. Questa colonna sarà di tipo Long.

**Condizioni di ordinamento:** Scegliere le colonne in base a cui si sta eseguendo l'ordinamento e l'ordine in cui si verifica l'ordinamento. L'ordine determina la priorità di ordinamento.

La configurazione precedente accetta i dati di pallacanestro in ingresso e crea una colonna di rango denominata ' pointsRanking '. La riga con il valore più alto della colonna *pts* avrà un valore *pointsRanking* pari a 1.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Esempio

![Impostazioni di rango](media/data-flow/rank-configuration.png "Impostazioni di rango")

Lo script del flusso di dati per la configurazione di rango precedente si trova nel seguente frammento di codice.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Passaggi successivi

Filtrare le righe in base ai valori di pertinenza utilizzando la [trasformazione filtro](data-flow-filter.md).
