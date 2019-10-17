---
title: Trasformazione UnPivot flusso di dati mapping Azure Data Factory
description: Trasformazione UnPivot flusso di dati mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 41423f704c87f05ad3e31b253d4a80799a554849
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387738"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Trasformazione UnPivot Azure Data Factory



Utilizzare UnPivot nel flusso di dati del mapping di ADF per trasformare un set di dati non normalizzato in una versione più normalizzata espandendo i valori da più colonne di un singolo record in più record con gli stessi valori in una singola colonna.

![Trasformazione UnPivot](media/data-flow/unpivot1.png "Opzioni UnPivot 1")

## <a name="ungroup-by"></a>Ungroup By (Separa)

![Trasformazione UnPivot](media/data-flow/unpivot5.png "Opzioni UnPivot 2")

Impostare prima di tutto le colonne in base alle quali si desidera eseguire il raggruppamento per l'aggregazione di Pivot. Impostare una o più colonne per la separazione usando il segno + accanto all'elenco di colonne.

## <a name="unpivot-key"></a>Unpivot Key (Chiave UnPivot)

![Trasformazione UnPivot](media/data-flow/unpivot6.png "Opzioni UnPivot 3")

La chiave Pivot è la colonna in base alla quale Azure Data Factory eseguirà la trasformazione tramite Pivot da riga a colonna. Per impostazione predefinita, ogni valore univoco nel set di dati per questo campo verrà trasformato tramite Pivot in una colonna. Tuttavia, è facoltativamente possibile immettere i valori dal set di dati che si desidera trasformare tramite Pivot in valori di colonna.

## <a name="unpivoted-columns"></a>Colonne trasformate tramite UnPivot

![Trasformazione UnPivot](media/data-flow//unpivot7.png "Opzioni UnPivot 4")

Scegliere infine l'aggregazione che si desidera usare per i valori trasformati tramite Pivot e la modalità di visualizzazione per le colonne nella nuova proiezione di output dalla trasformazione.

(Facoltativo) È possibile impostare un criterio di denominazione con prefisso, valore intermedio e suffisso da aggiungere a ogni nuovo nome di colonna dai valori di riga.

Ad esempio, se si trasforma tramite Pivot "Vendite" in base ad "Area" verrebbero generati semplicemente nuovi valori di colonna da ogni valore delle vendite. Ad esempio: "25", "50", "1000",... Tuttavia, se si imposta un valore di prefisso "Sales", "Sales" sarà anteposto ai valori.

<img src="media/data-flow/unpivot3.png" width="400">

Impostare la disposizione delle colonne "Normal" (Normale) per raggruppare insieme tutte le colonne trasformate tramite Pivot con i relativi valori aggregati. La disposizione delle colonne "Lateral" (Laterale) consentirà di alternare colonna e valore.

![Trasformazione UnPivot](media/data-flow//unpivot7.png "Opzioni UnPivot 5")

Il set di risultati finale dei dati trasformati tramite UnPivot mostra i totali delle colonne, ora trasformati tramite UnPivot in valori di riga separati.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare la [trasformazione pivot](data-flow-pivot.md) per trasformare le righe in colonne.
