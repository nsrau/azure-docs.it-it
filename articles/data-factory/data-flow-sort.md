---
title: Trasformazione ordinamento nel flusso di dati del mapping
description: Trasformazione ordinamento dati mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606330"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Trasformazione ordinamento nel flusso di dati del mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione ordinamento consente di ordinare le righe in ingresso nel flusso di dati corrente. È possibile scegliere singole colonne e ordinarle in ordine crescente o decrescente.

> [!NOTE]
> I flussi di dati di mapping vengono eseguiti nei cluster Spark che distribuiscono i dati tra più nodi e partizioni. Se si sceglie di ripartizionare i dati in una trasformazione successiva, è possibile che l'ordinamento venga perso a causa della ridistribuzione dei dati.

## <a name="configuration"></a>Configurazione

![Impostazioni di ordinamento](media/data-flow/sort.png "Ordina")

Senza **distinzione tra maiuscole e minuscole:** Indica se si desidera ignorare la distinzione tra maiuscole e minuscole durante l'ordinamento di campi di tipo stringa o testo

**Ordina solo all'interno delle partizioni:** Poiché i flussi di dati vengono eseguiti in Spark, ogni flusso di dati viene suddiviso in partizioni. Questa impostazione ordina i dati solo all'interno delle partizioni in ingresso anziché ordinare l'intero flusso di dati. 

**Condizioni di ordinamento:** Scegliere le colonne in base alle quali ordinare e in quale ordine si verifica l'ordinamento. L'ordine determina la priorità di ordinamento. Scegliere se visualizzare o meno i valori null all'inizio o alla fine del flusso di dati.

### <a name="computed-columns"></a>Colonne calcolate

Per modificare o estrarre un valore di colonna prima di applicare l'ordinamento, passare il puntatore del mouse sulla colonna e selezionare "colonna calcolata". Verrà aperto il generatore di espressioni per creare un'espressione per l'operazione di ordinamento anziché utilizzare un valore di colonna.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Esempio

![Impostazioni di ordinamento](media/data-flow/sort.png "Ordina")

Lo script del flusso di dati per la configurazione di ordinamento precedente si trova nel frammento di codice riportato di seguito.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Passaggi successivi

Dopo l'ordinamento, potrebbe essere necessario utilizzare la [trasformazione aggregazione](data-flow-aggregate.md)
