---
title: Trasformazione pivot nel flusso di dati di mapping
description: Trasformare i dati tramite pivot da righe a colonne utilizzando la trasformazione pivot del flusso di dati del mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686387"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Trasformazione pivot nel flusso di dati di mapping


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione pivot per creare più colonne dai valori di riga univoci di una singola colonna. Pivot è una trasformazione aggregazione in cui è possibile selezionare Raggruppa per colonne e generare colonne pivot utilizzando [funzioni di aggregazione](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Configurazione

La trasformazione pivot richiede tre diversi input: colonne Group by, chiave pivot e come generare le colonne trasformate tramite pivot.

### <a name="group-by"></a>Group by

![Opzioni di raggruppamento](media/data-flow/pivot2.png "[Opzioni Group by")

Consente di selezionare le colonne su cui aggregare le colonne trasformate tramite pivot. I dati di output raggruppano tutte le righe con gli stessi valori Group by in un'unica riga. L'aggregazione eseguita nella colonna trasformata tramite pivot viene eseguita su ogni gruppo.

Questa sezione è facoltativa. Se non sono selezionate colonne Group by, l'intero flusso di dati verrà aggregato e verrà restituita una sola riga.

### <a name="pivot-key"></a>Chiave pivot

![Chiave pivot](media/data-flow/pivot3.png "Chiave pivot")

La chiave pivot è la colonna i cui valori di riga vengono trasformati tramite pivot in nuove colonne. Per impostazione predefinita, la trasformazione pivot creerà una nuova colonna per ogni valore di riga univoco.

Nella sezione **valore**con etichetta è possibile immettere valori di riga specifici da trasformare tramite pivot. Solo i valori di riga immessi in questa sezione verranno trasformati tramite pivot. L'abilitazione di un **valore null** creerà una colonna trasformata tramite pivot per i valori null nella colonna.

### <a name="pivoted-columns"></a>Colonne trasformate tramite pivot

![Colonne trasformate tramite pivot](media/data-flow/pivot4.png "Colonne trasformate tramite pivot")

Per ogni valore di chiave pivot univoco che diventa una colonna, generare un valore di riga aggregato per ogni gruppo. È possibile creare più colonne per ogni chiave pivot. Ogni colonna pivot deve contenere almeno una [funzione di aggregazione](data-flow-expression-functions.md#aggregate-functions).

**Modello nome colonna:** Consente di selezionare la modalità di formattazione del nome della colonna di ogni colonna pivot. Il nome della colonna in output sarà una combinazione del valore della chiave pivot, del prefisso di colonna e del prefisso facoltativo, bastanti, caratteri intermedi. 

**Disposizione colonne:** Se si generano più colonne pivot per ogni chiave pivot, scegliere il modo in cui si desidera ordinare le colonne. 

**Prefisso colonna:** Se si genera più di una colonna pivot per ogni chiave pivot, immettere un prefisso di colonna per ogni colonna. Questa impostazione è facoltativa se si dispone di una sola colonna trasformata tramite pivot.

## <a name="help-graphic"></a>Immagine della Guida

Il grafico della guida seguente mostra il modo in cui i diversi componenti pivot interagiscono tra loro

![Grafica della Guida pivot](media/data-flow/pivot5.png "Rappresentazione grafica della Guida pivot")

## <a name="pivot-metadata"></a>Metadati pivot

Se non viene specificato alcun valore nella configurazione della chiave pivot, le colonne trasformate tramite pivot verranno generate dinamicamente in fase di esecuzione. Il numero di colonne trasformate tramite pivot corrisponde al numero di valori di chiave pivot univoci moltiplicato per il numero di colonne pivot. Poiché può trattarsi di un numero modificabile, l'esperienza utente non visualizzerà i metadati della colonna nella scheda **Controlla** e non sarà presente alcuna propagazione delle colonne. Per eseguire la trasformazione di queste colonne, utilizzare le funzionalità del [modello di colonna](concepts-data-flow-column-pattern.md) del flusso di dati di mapping. 

Se sono impostati valori di chiave pivot specifici, le colonne trasformate tramite pivot verranno visualizzate nei metadati. e i nomi delle colonne saranno disponibili nel mapping controlla e sink.

### <a name="generate-metadata-from-drifted-columns"></a>Genera metadati da colonne con drifting

Pivot genera i nuovi nomi di colonna in modo dinamico in base ai valori di riga. È possibile aggiungere queste nuove colonne ai metadati a cui è possibile fare riferimento in un secondo momento nel flusso di dati. A tale scopo, usare l'azione rapida per la [mappa](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) nell'anteprima dei dati. 

![Colonne pivot](media/data-flow/newpivot1.png "Eseguire il mapping delle colonne pivot")

### <a name="sinking-pivoted-columns"></a>Sink di colonne trasformate tramite pivot

Sebbene le colonne trasformate tramite pivot siano dinamiche, possono comunque essere scritte nell'archivio dati di destinazione. Abilitare **Consenti Drift schema** nelle impostazioni del sink. Ciò consentirà di scrivere colonne non incluse nei metadati. i metadati della colonna, ma l'opzione di disattivazione dello schema consente di inserire i dati.

### <a name="rejoin-original-fields"></a>Riunisci campi originali

Tramite la trasformazione pivot vengono proiettate solo le colonne Group by e pivoted. Se si vuole che i dati di output includano altre colonne di input, usare un modello [self-join](data-flow-join.md#self-join) .

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

Le schermate visualizzate nella sezione configurazione hanno lo script del flusso di dati seguente:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Passaggi successivi

Provare la [trasformazione UnPivot](data-flow-unpivot.md) per trasformare i valori di colonna in valori di riga. 
