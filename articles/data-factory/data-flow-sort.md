---
title: Trasformazione di ordinamento nel mapping del flusso di dati
description: Trasformazione Ordinamento dati mapping di Azure Data FactoryAzure Data Factory Mapping Data Sort
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606330"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Trasformazione di ordinamento nel mapping del flusso di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione di ordinamento consente di ordinare le righe in ingresso nel flusso di dati corrente. È possibile scegliere singole colonne e ordinarle in ordine crescente o decrescente.

> [!NOTE]
> Il mapping dei flussi di dati viene eseguito su cluster di spark che distribuiscono dati tra più nodi e partizioni. Se si sceglie di ripartizionare i dati in una trasformazione successiva, è possibile che l'ordinamento venga perso a causa del rimescolamento dei dati.

## <a name="configuration"></a>Configurazione

![Impostazioni di ordinamento](media/data-flow/sort.png "Ordina")

**Senza distinzione tra maiuscole e minuscole:** Se si desidera ignorare o meno la distinzione tra maiuscole e minuscole durante l'ordinamento di campi stringa o di testo

**Ordina solo all'interno delle partizioni:** Quando i flussi di dati vengono eseguiti su spark, ogni flusso di dati è suddiviso in partizioni. Questa impostazione ordina i dati solo all'interno delle partizioni in ingresso anziché ordinare l'intero flusso di dati. 

**Condizioni di ordinamento:** Scegliere le colonne in base alle quali eseguire l'ordinamento e in quale ordine viene eseguito l'ordinamento. L'ordine determina la priorità di ordinamento. Scegliere se i valori Null verranno visualizzati all'inizio o alla fine del flusso di dati.

### <a name="computed-columns"></a>Colonne calcolate

Per modificare o estrarre un valore di colonna prima di applicare l'ordinamento, passare il mouse sulla colonna e selezionare "colonna calcolata". Verrà aperto il generatore di espressioni per creare un'espressione per l'operazione di ordinamento anziché utilizzare un valore di colonna.

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

Lo script del flusso di dati per la configurazione di ordinamento precedente si trova nel frammento di codice seguente.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Passaggi successivi

Dopo l'ordinamento, è possibile utilizzare la [trasformazione](data-flow-aggregate.md)
