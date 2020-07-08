---
title: Trasformazione tramite Pivot nel flusso di dati per mapping
description: Trasformare i dati tramite Pivot da righe a colonne usando la trasformazione Pivot del flusso di dati per mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a0084c3e8185f615e7ac2a2b8c212f1ebf022c08
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683292"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Trasformazione tramite Pivot nel flusso di dati per mapping


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione tramite Pivot per creare più colonne dai valori di riga univoci di una singola colonna. La trasformazione tramite Pivot si basa sull'aggregazione e consente di selezionare il raggruppamento per colonne per generare colonne pivot usando le [funzioni di aggregazione](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Configurazione

La trasformazione tramite Pivot richiede tre diversi input: le colonne per il raggruppamento, la chiave pivot e la modalità di generazione per le colonne trasformate tramite Pivot.

### <a name="group-by"></a>Group by

![Opzioni di raggruppamento](media/data-flow/pivot2.png "Opzioni di raggruppamento")

Selezionare le colonne da aggregare per la trasformazione tramite Pivot. I dati di output raggruppano tutte le righe con gli stessi valori Group by in un'unica riga. L'aggregazione eseguita nella colonna trasformata tramite Pivot viene eseguita in ogni gruppo.

Questa sezione è facoltativa. Se non sono selezionate colonne Group by, viene aggregato l'intero flusso dei dati e viene restituita una sola riga.

### <a name="pivot-key"></a>Chiave pivot

![Chiave pivot](media/data-flow/pivot3.png "Chiave pivot")

La chiave pivot corrisponde alla colonna i cui valori di riga vengono trasformati tramite Pivot in nuove colonne. Per impostazione predefinita, la trasformazione tramite Pivot crea una nuova colonna per ogni valore di riga univoco.

Nella sezione **Valore** è possibile immettere valori di riga specifici da trasformare tramite Pivot. Vengono trasformati tramite Pivot solo i valori di riga immessi in questa sezione. L'abilitazione del **valore Null** crea una colonna trasformata tramite Pivot per i valori Null della colonna.

### <a name="pivoted-columns"></a>Colonne trasformate tramite Pivot

![Colonne trasformate tramite Pivot](media/data-flow/pivot4.png "Colonne trasformate tramite Pivot")

Per ogni valore di chiave pivot univoco che viene trasformato in una colonna, viene generato un valore di riga aggregato per ogni gruppo. È possibile creare più colonne per ogni chiave pivot. Ogni colonna pivot deve contenere almeno una [funzione di aggregazione](data-flow-expression-functions.md#aggregate-functions).

**Column name pattern** (Modello nome colonna): Selezionare la modalità di formattazione del nome colonna per ogni colonna pivot. Il nome della colonna in output corrisponde a una combinazione con valore della chiave pivot, prefisso di colonna, prefisso facoltativo, suffisso e caratteri intermedi. 

**Column arrangement** (Disposizione colonne): Se si generano più colonne pivot per ogni chiave pivot, scegliere il modo in cui ordinare le colonne. 

**Prefisso di colonna**: Se si generano più colonne pivot per ogni chiave pivot, immettere un prefisso di colonna per ogni colonna. Questa impostazione è facoltativa se si dispone di una sola colonna trasformata tramite Pivot.

## <a name="help-graphic"></a>Immagine guida

L'immagine guida che segue mostra l'interazione tra i diversi componenti pivot

![Immagine guida per la trasformazione tramite Pivot](media/data-flow/pivot5.png "Immagine guida per la trasformazione tramite Pivot")

## <a name="pivot-metadata"></a>Metadati pivot

Se non viene specificato alcun valore nella configurazione della chiave pivot, le colonne trasformate tramite Pivot vengono generate in modo dinamico in fase di esecuzione. Il numero di colonne trasformate tramite Pivot corrisponde al numero di valori di chiave pivot univoci moltiplicato per il numero di colonne pivot. Poiché può trattarsi di un numero variabile, l'esperienza utente non visualizza i metadati della colonna nella scheda **Ispeziona** e non viene eseguita nessuna propagazione delle colonne. Per eseguire la trasformazione di queste colonne, usare le funzionalità del flusso di dati per mapping del [modello di colonna](concepts-data-flow-column-pattern.md). 

Se sono stati impostati valori di chiave pivot specifici, le colonne trasformate tramite Pivot vengono visualizzate nei metadati. I nomi delle colonne sono disponibili nella scheda Ispeziona e nel mapping di sink e ispezione.

### <a name="generate-metadata-from-drifted-columns"></a>Generare i metadati dalle colonne deviate

La trasformazione tramite Pivot genera i nuovi nomi di colonna in modo dinamico in base ai valori di riga. È possibile aggiungere le nuove colonne ai metadati a cui fare riferimento in un secondo momento nel flusso di dati. A tale scopo, usare l'azione rapida per il [mapping delle colonne deviate](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) nell'anteprima dei dati. 

![Colonne pivot](media/data-flow/newpivot1.png "Mapping delle colonne pivot deviate")

### <a name="sinking-pivoted-columns"></a>Colonne trasformate tramite Pivot con sink

Sebbene le colonne trasformate tramite Pivot siano dinamiche, possono comunque essere scritte nell'archivio dati di destinazione. Abilitare **Allow schema drift** (Consenti deviazione schema) nelle impostazioni di sink. Ciò consente di eseguire la scrittura delle colonne non incluse nei metadati. I nuovi nomi dinamici non vengono visualizzati nei metadati della colonna, ma l'opzione di deviazione dello schema consente di inserire i dati.

### <a name="rejoin-original-fields"></a>Unire di nuovo in join i campi originali

La trasformazione tramite Pivot proietta solo le colonne Group by e trasformate tramite Pivot. Per includere altre colonne di input nei dati di output, usare un modello [self join](data-flow-join.md#self-join).

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

Le schermate visualizzate nella sezione di configurazione includono lo script del flusso di dati seguente:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Passaggi successivi

Provare la [trasformazione tramite UnPivot](data-flow-unpivot.md) per trasformare i valori di colonna in valori di riga. 
