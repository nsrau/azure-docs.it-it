---
title: Trasformazione pivot nel mapping del flusso di dati
description: Trasformazione pivot del flusso di dati di mapping di Dati pivot da righe a colonne tramite la trasformazione pivot del flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686387"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Trasformazione pivot nel mapping del flusso di dati


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione pivot per creare più colonne dai valori di riga univoci di una singola colonna. Pivot è una trasformazione di aggregazione in cui si seleziona raggruppa per colonne e si generano colonne pivot utilizzando funzioni di [aggregazione.](data-flow-expression-functions.md#aggregate-functions)

## <a name="configuration"></a>Configurazione

La trasformazione pivot richiede tre diversi input: raggruppa per colonne, la chiave pivot e come generare le colonne pivot

### <a name="group-by"></a>Group by

![Opzioni di raggruppamento](media/data-flow/pivot2.png "[Raggruppa per opzioni")

Selezionare le colonne in cui aggregare le colonne con pivot. I dati di output verranno raggruppati in tutte le righe con lo stesso gruppo in base ai valori in un'unica riga. L'aggregazione eseguita nella colonna pivot verrà eseguita su ogni gruppo.

Questa sezione è facoltativa. Se non è selezionato alcun gruppo per colonne, verrà aggregato l'intero flusso di dati e verrà emessa una sola riga.

### <a name="pivot-key"></a>Tasto Pivot

![Tasto Pivot](media/data-flow/pivot3.png "Tasto Pivot")

La chiave pivot è la colonna i cui valori di riga vengono sottoposti a pivot in nuove colonne. Per impostazione predefinita, la trasformazione pivot creerà una nuova colonna per ogni valore di riga univoco.

Nella sezione **Valore**è possibile immettere valori di riga specifici da eseguire il pivot. Verranno sottoposti a pivot solo i valori di riga immessi in questa sezione. L'abilitazione del **valore Null** creerà una colonna pivot per i valori Null nella colonna.

### <a name="pivoted-columns"></a>Colonne pivot

![Colonne pivot](media/data-flow/pivot4.png "Colonne pivot")

Per ogni valore di chiave pivot univoco che diventa una colonna, generare un valore di riga aggregato per ogni gruppo. È possibile creare più colonne per chiave pivot. Ogni colonna pivot deve contenere almeno una [funzione di aggregazione.](data-flow-expression-functions.md#aggregate-functions)

**Modello nome colonna:** Selezionare la modalità di formattazione del nome della colonna pivot. Il nome della colonna emessa sarà una combinazione del valore della chiave pivot, del prefisso di colonna e del prefisso facoltativo, sufficiente, dei caratteri intermedi. 

**Disposizione delle colonne:** Se si generano più colonne pivot per chiave pivot, scegliere la modalità di ordinamento delle colonne. 

**Prefisso colonna:** Se si generano più di una colonna pivot per chiave pivot, immettere un prefisso di colonna per ogni colonna. Questa impostazione è facoltativa se si dispone di una sola colonna pivot.

## <a name="help-graphic"></a>Grafica della Guida

L'immagine della Guida seguente mostra come i diversi componenti di rotazione interagiscono tra loro

![Grafica della Guida pivot](media/data-flow/pivot5.png "Grafica della Guida pivot")

## <a name="pivot-metadata"></a>Metadati pivot

Se nella configurazione della chiave pivot non viene specificato alcun valore, le colonne pivot verranno generate dinamicamente in fase di esecuzione. Il numero di colonne pivot sarà uguale al numero di valori di chiave pivot univoci moltiplicato per il numero di colonne pivot. Poiché può trattarsi di un numero che cambia, l'esperienza utente non visualizzerà i metadati della colonna nella scheda **Controlla** e non sarà presente alcuna propagazione delle colonne. Per trasformare queste colonne, usare le funzionalità del modello di [colonna](concepts-data-flow-column-pattern.md) per il mapping del flusso di dati. 

Se vengono impostati valori di chiave pivot specifici, le colonne pivot verranno visualizzate nei metadati. i nomi delle colonne saranno disponibili nel mapping Controlla e sink.

### <a name="generate-metadata-from-drifted-columns"></a>Generare metadati da colonne con deriva

Pivot genera nuovi nomi di colonna in modo dinamico in base ai valori di riga. È possibile aggiungere queste nuove colonne ai metadati a cui è possibile fare riferimento in un secondo momento nel flusso di dati. A tale scopo, utilizzare l'azione rapida [mappa alla deriva](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) nell'anteprima dei dati. 

![Colonne pivot](media/data-flow/newpivot1.png "Mappa colonne Pivot alla deriva")

### <a name="sinking-pivoted-columns"></a>Colonne pivot affondate

Sebbene le colonne con pivot siano dinamiche, possono comunque essere scritte nell'archivio dati di destinazione. Abilitare **Consenti deriva schema** nelle impostazioni del sink. In questo modo sarà possibile scrivere colonne non incluse nei metadati. i metadati della colonna, ma l'opzione di deriva dello schema consente di ottenere i dati.

### <a name="rejoin-original-fields"></a>Ricongiungi campi originali

La trasformazione pivot proibirà solo il gruppo e le colonne sottoposte a pivot. Se si desidera che i dati di output includano altre colonne di input, usare un modello [di self join.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Esempio

Le schermate visualizzate nella sezione di configurazione hanno lo script del flusso di dati seguente:The screens shown in the configuration section, have the following data flow script:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Passaggi successivi

Provare la [trasformazione unpivot](data-flow-unpivot.md) per trasformare i valori di colonna in valori di riga. 
