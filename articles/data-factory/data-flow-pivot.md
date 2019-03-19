---
title: Trasformazione Pivot per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Pivot per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569893"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Trasformazione Pivot per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Pivot nel flusso di dati di Azure Data Factory come un'aggregazione in cui i valori di riga distinti di una o più colonne di raggruppamento vengono trasformati in singole colonne. In pratica, è possibile trasformare tramite Pivot i valori di riga in nuove colonne (trasformare i dati in metadati).

![Opzioni di Pivot](media/data-flow/pivot1.png "Pivot 1")

## <a name="group-by"></a>Raggruppa per

![Opzioni di Pivot](media/data-flow/pivot2.png "Pivot 2")

Impostare prima di tutto le colonne in base alle quali si desidera eseguire il raggruppamento per l'aggregazione di Pivot. È possibile impostare più di 1 colonna con il segno più (+) accanto all'elenco di colonne.

## <a name="pivot-key"></a>Chiave Pivot

![Opzioni di Pivot](media/data-flow/pivot3.png "Pivot 3")

La chiave Pivot è la colonna in base alla quale Azure Data Factory eseguirà la trasformazione tramite Pivot da riga a colonna. Per impostazione predefinita, ogni valore univoco nel set di dati per questo campo verrà trasformato tramite Pivot in una colonna. Tuttavia, è facoltativamente possibile immettere i valori dal set di dati che si desidera trasformare tramite Pivot in valori di colonna.

## <a name="pivoted-columns"></a>Colonne trasformate tramite Pivot

![Opzioni di Pivot](media/data-flow/pivot4.png "Pivot 4")

Infine, si sceglierà l'aggregazione che si desidera usare per i valori trasformati tramite Pivot e la modalità di visualizzazione per le colonne nella nuova proiezione di output dalla trasformazione.

(Facoltativo) È possibile impostare un criterio di denominazione con prefisso, valore intermedio e suffisso da aggiungere a ogni nuovo nome di colonna dai valori di riga.

Ad esempio, se si trasforma tramite Pivot "Vendite" in base ad "Area" verrebbero generati valori di colonna da ogni valore delle vendite, ad esempio "25", "50", "1000" e così via. Tuttavia, se si imposta un valore di prefisso "Vendite-", ogni valore di colonna aggiungerebbe "Vendite-" all'inizio del valore.

![Opzioni di Pivot](media/data-flow/pivot5.png "Pivot 5")

Impostare la disposizione delle colonne "Normal" (Normale) per raggruppare insieme tutte le colonne trasformate tramite Pivot con i relativi valori aggregati. La disposizione delle colonne "Lateral" (Laterale) consentirà di alternare colonna e valore.

### <a name="aggregation"></a>Aggregazione

Per impostare l'aggregazione da usare per i valori Pivot, fare clic sul campo nella parte inferiore del riquadro Pivoted Columns (Colonne trasformate tramite pivot). Verrà aperto il generatore di espressioni del flusso di dati di Azure Data Factory in cui è possibile compilare un'espressione di aggregazione e specificare un nome alias descrittivo per i nuovi valori aggregati.

Usare il linguaggio per le espressioni del flusso dei dati di Azure Data Factory per descrivere le trasformazioni di colonna tramite Pivot nel generatore di espressioni: https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Come unire di nuovo in join i campi originali
> [!NOTE]
> La trasformazione Pivot proietterà solo le colonne usate nelle azioni di aggregazione, raggruppamento e Pivot. Se si desidera includere altre colonne nel passaggio precedente nel flusso, usare un nuovo ramo nel passaggio precedente e usare il modello di self join per connettere il flusso con i metadati originali.

## <a name="next-steps"></a>Passaggi successivi

Provare il [trasformazione unpivot](data-flow-unpivot.md) per trasformare i valori delle colonne in valori di riga. 
