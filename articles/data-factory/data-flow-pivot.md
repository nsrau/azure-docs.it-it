---
title: Mapping della trasformazione Pivot del flusso di dati
description: Trasformazione pivot del flusso di dati di mapping di Dati pivot da righe a colonne tramite la trasformazione pivot del flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 980d7c3e1b1f69e76c091e2a4a74c8e5a4d0bb64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606367"
---
# <a name="azure-data-factory-pivot-transformation"></a>Trasformazione del pivot di Azure Data FactoryAzure data factory pivot transformation

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione Pivot nel flusso di dati di Azure Data Factory come un'aggregazione in cui i valori di riga distinti di una o più colonne di raggruppamento vengono trasformati in singole colonne. In pratica, è possibile trasformare tramite Pivot i valori di riga in nuove colonne (trasformare i dati in metadati).

![Opzioni pivot](media/data-flow/pivot1.png "perno 1")

## <a name="group-by"></a>Group by

![Opzioni pivot](media/data-flow/pivot2.png "perno 2")

Impostare prima di tutto le colonne in base alle quali si desidera eseguire il raggruppamento per l'aggregazione di Pivot. È possibile impostare più di 1 colonna con il segno più (+) accanto all'elenco di colonne.

## <a name="pivot-key"></a>Tasto Pivot

![Opzioni pivot](media/data-flow/pivot3.png "perno 3")

La chiave Pivot è la colonna in base alla quale Azure Data Factory eseguirà la trasformazione tramite Pivot da riga a colonna. Per impostazione predefinita, ogni valore univoco nel set di dati per questo campo verrà trasformato tramite Pivot in una colonna. Tuttavia, è facoltativamente possibile immettere i valori dal set di dati che si desidera trasformare tramite Pivot in valori di colonna. Questa è la colonna che determinerà le nuove colonne che verranno create.

## <a name="pivoted-columns"></a>Colonne pivot

![Opzioni pivot](media/data-flow/pivot4.png "perno 4")

Infine, si sceglierà l'aggregazione che si desidera usare per i valori trasformati tramite Pivot e la modalità di visualizzazione per le colonne nella nuova proiezione di output dalla trasformazione.

(Facoltativo) È possibile impostare un criterio di denominazione con prefisso, valore intermedio e suffisso da aggiungere a ogni nuovo nome di colonna dai valori di riga.

Ad esempio, il pivot "Vendite" per "Regione" comporterebbe nuovi valori di colonna da ogni valore di vendita, ad esempio "25", "50", "1000" e così via. Tuttavia, se si imposta un valore di prefisso "Sales-", ogni valore di colonna aggiungerà "Sales-" all'inizio del valore.

![Opzioni pivot](media/data-flow/pivot5.png "perno 5")

Impostare la disposizione delle colonne "Normal" (Normale) per raggruppare insieme tutte le colonne trasformate tramite Pivot con i relativi valori aggregati. La disposizione delle colonne "Lateral" (Laterale) consentirà di alternare colonna e valore.

### <a name="aggregation"></a>Aggregazione

Per impostare l'aggregazione da usare per i valori Pivot, fare clic sul campo nella parte inferiore del riquadro Pivoted Columns (Colonne trasformate tramite pivot). Verrà aperto il generatore di espressioni del flusso di dati di Azure Data Factory in cui è possibile compilare un'espressione di aggregazione e specificare un nome alias descrittivo per i nuovi valori aggregati.

Usare il linguaggio per le espressioni del flusso dei dati di Azure Data Factory per descrivere le trasformazioni di colonna tramite Pivot nel generatore di espressioni: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadati pivot

La trasformazione Pivot produrrà nuovi nomi di colonna dinamici in base ai dati in ingresso. La chiave pivot produce i valori per ogni nuovo nome di colonna. Se non si specificano singoli valori e si desidera creare nomi di colonna dinamici per ogni valore univoco nella chiave pivot, l'interfaccia utente non visualizzerà i metadati in Inspect e non sarà presente alcuna propagazione delle colonne per la trasformazione Sink. Se si impostano i valori per la chiave pivot, ADF può determinare i nuovi nomi di colonna e tali nomi di colonna saranno disponibili nel mapping Controlla e sink.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generare un nuovo modello da colonne dinamiche

Pivot genera nuovi nomi di colonna in modo dinamico in base ai valori di riga. È possibile trasformare queste nuove colonne in metadati a cui è possibile fare riferimento in un secondo momento nel flusso di dati. A tale scopo, fare clic sulla scheda Anteprima dati. Tutte le nuove colonne generate dalla trasformazione Pivot vengono visualizzate con un'icona "drifted" nell'intestazione della tabella. Fare clic sul pulsante "Mappa alla deriva" per trasformare queste nuove colonne in metadati, rendendole parte del modello del flusso di dati.

![Colonne pivot](media/data-flow/newpivot1.png "Mappa colonne Pivot alla deriva")

### <a name="landing-new-columns-in-sink"></a>Atterraggio di nuove colonne nel sink

Anche con i nomi di colonna dinamici in Pivot, è comunque possibile affondere i nuovi nomi e valori di colonna nell'archivio di destinazione. È sufficiente impostare "Consenti deriva dello schema" su on nelle impostazioni del sink. Non verranno visualizzati i nuovi nomi dinamici nei metadati della colonna, ma l'opzione di deriva dello schema consentirà di ottenere i dati.

### <a name="view-metadata-in-design-mode"></a>Visualizzare i metadati in modalità progettazione

Se si desidera visualizzare i nuovi nomi di colonna come metadati in Inspect e si desidera visualizzare le colonne propagate in modo esplicito per la trasformazione Sink, quindi impostare valori espliciti nella scheda Chiave pivot .

### <a name="how-to-rejoin-original-fields"></a>Come unire di nuovo in join i campi originali
La trasformazione Pivot proietterà solo le colonne usate nelle azioni di aggregazione, raggruppamento e Pivot. Se si desidera includere nel flusso le altre colonne del passaggio precedente, usare un nuovo ramo del passaggio precedente e il modello di self-join per connettere il flusso ai metadati originali.

## <a name="next-steps"></a>Passaggi successivi

Provare la [trasformazione unpivot](data-flow-unpivot.md) per trasformare i valori di colonna in valori di riga. 
