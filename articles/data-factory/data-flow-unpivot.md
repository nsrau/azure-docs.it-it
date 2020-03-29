---
title: Mapping data flow Unpivot Transformation
description: Azure Data Factory mapping data flow Unpivot Transformation
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930145"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory Unpivot Transformation



Usare Unpivot nel flusso di dati di mapping di ADF per trasformare un set di dati non normalizzato in una versione più normalizzata espandendo i valori da più colonne in un singolo record in più record in più record con gli stessi valori in una singola colonna.

![Trasformazione UnPivotUnpivot Transformation](media/data-flow/unpivot1.png "Opzioni di unpivot 1")

## <a name="ungroup-by"></a>Ungroup By (Separa)

![Trasformazione UnPivotUnpivot Transformation](media/data-flow/unpivot5.png "Opzioni unpivot 2")

Impostare prima di tutto le colonne in base alle quali si desidera eseguire il raggruppamento per l'aggregazione di Pivot. Impostare una o più colonne per la separazione usando il segno + accanto all'elenco di colonne.

## <a name="unpivot-key"></a>Unpivot Key (Chiave UnPivot)

![Trasformazione UnPivotUnpivot Transformation](media/data-flow/unpivot6.png "Opzioni di unpivot 3")

La chiave Pivot è la colonna in base alla quale Azure Data Factory eseguirà la trasformazione tramite Pivot da riga a colonna. Per impostazione predefinita, ogni valore univoco nel set di dati per questo campo verrà trasformato tramite Pivot in una colonna. Tuttavia, è facoltativamente possibile immettere i valori dal set di dati che si desidera trasformare tramite Pivot in valori di colonna.

## <a name="unpivoted-columns"></a>Colonne trasformate tramite UnPivot

![Trasformazione UnPivotUnpivot Transformation](media/data-flow//unpivot7.png "Opzioni unpivot 4")

Scegliere infine l'aggregazione che si desidera usare per i valori trasformati tramite Pivot e la modalità di visualizzazione per le colonne nella nuova proiezione di output dalla trasformazione.

(Facoltativo) È possibile impostare un criterio di denominazione con prefisso, valore intermedio e suffisso da aggiungere a ogni nuovo nome di colonna dai valori di riga.

Ad esempio, se si trasforma tramite Pivot "Vendite" in base ad "Area" verrebbero generati semplicemente nuovi valori di colonna da ogni valore delle vendite. Ad esempio: "25", "50", "1000", ... Tuttavia, se si imposta un valore di prefisso di "Sales", "Sales" verrà anteposto ai valori.

<img src="media/data-flow/unpivot3.png" width="400">

Impostare la disposizione delle colonne "Normal" (Normale) per raggruppare insieme tutte le colonne trasformate tramite Pivot con i relativi valori aggregati. La disposizione delle colonne "Lateral" (Laterale) consentirà di alternare colonna e valore.

![Trasformazione UnPivotUnpivot Transformation](media/data-flow//unpivot7.png "Opzioni unpivot 5")

Il set di risultati finale dei dati trasformati tramite UnPivot mostra i totali delle colonne, ora trasformati tramite UnPivot in valori di riga separati.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare la [trasformazione Pivot](data-flow-pivot.md) per eseguire il pivot delle righe in colonne.
