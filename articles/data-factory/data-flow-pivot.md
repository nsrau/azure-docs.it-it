---
title: Trasformazione Pivot per il flusso di dati di mapping di Azure Data Factory
description: Trasformare i dati tramite pivot da righe a colonne utilizzando la trasformazione pivot del flusso di dati del mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 0b68007f8c3383997f0d31888198af866d38b590
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178650"
---
# <a name="azure-data-factory-pivot-transformation"></a>Trasformazione pivot data factory di Azure
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Pivot nel flusso di dati di Azure Data Factory come un'aggregazione in cui i valori di riga distinti di una o più colonne di raggruppamento vengono trasformati in singole colonne. In pratica, è possibile trasformare tramite Pivot i valori di riga in nuove colonne (trasformare i dati in metadati).

![Opzioni di Pivot](media/data-flow/pivot1.png "Pivot 1")

## <a name="group-by"></a>Raggruppa in base a

![Opzioni di Pivot](media/data-flow/pivot2.png "Pivot 2")

Impostare prima di tutto le colonne in base alle quali si desidera eseguire il raggruppamento per l'aggregazione di Pivot. È possibile impostare più di 1 colonna con il segno più (+) accanto all'elenco di colonne.

## <a name="pivot-key"></a>Chiave pivot

![Opzioni di Pivot](media/data-flow/pivot3.png "Pivot 3")

La chiave Pivot è la colonna in base alla quale Azure Data Factory eseguirà la trasformazione tramite Pivot da riga a colonna. Per impostazione predefinita, ogni valore univoco nel set di dati per questo campo verrà trasformato tramite Pivot in una colonna. Tuttavia, è facoltativamente possibile immettere i valori dal set di dati che si desidera trasformare tramite Pivot in valori di colonna. Si tratta della colonna che determinerà le nuove colonne che verranno create.

## <a name="pivoted-columns"></a>Colonne trasformate tramite pivot

![Opzioni di Pivot](media/data-flow/pivot4.png "Pivot 4")

Infine, si sceglierà l'aggregazione che si desidera usare per i valori trasformati tramite Pivot e la modalità di visualizzazione per le colonne nella nuova proiezione di output dalla trasformazione.

(Facoltativo) È possibile impostare un criterio di denominazione con prefisso, valore intermedio e suffisso da aggiungere a ogni nuovo nome di colonna dai valori di riga.

Ad esempio, se si trasforma tramite Pivot "Vendite" in base ad "Area" verrebbero generati valori di colonna da ogni valore delle vendite, ad esempio "25", "50", "1000" e così via. Tuttavia, se si imposta un valore di prefisso "Sales-", ogni valore di colonna aggiungerebbe "Sales-" all'inizio del valore.

![Opzioni di Pivot](media/data-flow/pivot5.png "Pivot 5")

Impostare la disposizione delle colonne "Normal" (Normale) per raggruppare insieme tutte le colonne trasformate tramite Pivot con i relativi valori aggregati. La disposizione delle colonne "Lateral" (Laterale) consentirà di alternare colonna e valore.

### <a name="aggregation"></a>Aggregazione

Per impostare l'aggregazione da usare per i valori Pivot, fare clic sul campo nella parte inferiore del riquadro Pivoted Columns (Colonne trasformate tramite pivot). Verrà aperto il generatore di espressioni del flusso di dati di Azure Data Factory in cui è possibile compilare un'espressione di aggregazione e specificare un nome alias descrittivo per i nuovi valori aggregati.

Usare il linguaggio per le espressioni del flusso dei dati di Azure Data Factory per descrivere le trasformazioni di colonna tramite Pivot nel generatore di espressioni: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadati pivot

La trasformazione pivot produrrà nuovi nomi di colonna dinamici in base ai dati in ingresso. La chiave pivot produce i valori per ogni nuovo nome di colonna. Se non si specificano valori singoli e si desidera creare nomi di colonna dinamici per ogni valore univoco nella chiave pivot, l'interfaccia utente non visualizzerà i metadati in controlla e non verrà eseguita alcuna propagazione delle colonne alla trasformazione sink. Se si impostano i valori per la chiave pivot, ADF è in grado di determinare i nuovi nomi di colonna e i nomi delle colonne saranno disponibili nel mapping controlla e sink.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generare un nuovo modello da colonne dinamiche

Pivot genera i nuovi nomi di colonna in modo dinamico in base ai valori di riga. È possibile trasformare le nuove colonne in metadati a cui è possibile fare riferimento in un secondo momento nel flusso di dati. A tale scopo, fare clic sulla scheda Anteprima dati. Tutte le nuove colonne generate dalla trasformazione pivot vengono visualizzate con l'icona "Drifted" nell'intestazione della tabella. Fai clic sul pulsante "mappato" per trasformare le nuove colonne in metadati, facendole parte del modello del flusso di dati.

![Colonne pivot](media/data-flow/newpivot1.png "Eseguire il mapping delle colonne pivot")

### <a name="landing-new-columns-in-sink"></a>Destinazione di nuove colonne nel sink

Anche con i nomi di colonna dinamici in pivot, è ancora possibile affondare i nomi e i valori delle nuove colonne nell'archivio di destinazione. È sufficiente impostare "Consenti la deriva dello schema" su on nelle impostazioni del sink. I nuovi nomi dinamici non vengono visualizzati nei metadati della colonna, ma l'opzione di spostamento dello schema consente di inserire i dati.

### <a name="view-metadata-in-design-mode"></a>Visualizzare i metadati in modalità progettazione

Se si desidera visualizzare i nuovi nomi di colonna come metadati in controlla e si desidera che le colonne vengano propagate in modo esplicito alla trasformazione sink, impostare valori espliciti nella scheda chiave pivot.

### <a name="how-to-rejoin-original-fields"></a>Come unire di nuovo in join i campi originali
La trasformazione Pivot proietterà solo le colonne usate nelle azioni di aggregazione, raggruppamento e Pivot. Se si desidera includere le altre colonne del passaggio precedente nel flusso, utilizzare un nuovo ramo del passaggio precedente e utilizzare il modello di self-join per connettere il flusso ai metadati originali.

## <a name="next-steps"></a>Passaggi successivi

Provare la [trasformazione UnPivot](data-flow-unpivot.md) per trasformare i valori di colonna in valori di riga. 
