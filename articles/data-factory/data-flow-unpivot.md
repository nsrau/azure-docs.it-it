---
title: Trasformazione UnPivot nel flusso di dati di mapping
description: Trasformazione UnPivot flusso di dati mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040202"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Trasformazione UnPivot nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare UnPivot nel flusso di dati del mapping di ADF per trasformare un set di dati non normalizzato in una versione più normalizzata espandendo i valori da più colonne di un singolo record in più record con gli stessi valori in una singola colonna.

![Screenshot Visualizza UnPivot selezionato dal menu.](media/data-flow/unpivot1.png "Opzioni UnPivot 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Ungroup By (Separa)

![Screenshot mostra le impostazioni di UnPivot con la scheda Ungroup by selezionata.](media/data-flow/unpivot5.png "Opzioni UnPivot 2")

Per prima cosa, impostare le colonne da deraggruppare per l'aggregazione UNPIVOT. Impostare una o più colonne per la separazione usando il segno + accanto all'elenco di colonne.

## <a name="unpivot-key"></a>Unpivot Key (Chiave UnPivot)

![Screenshot mostra le impostazioni UnPivot con la scheda chiave UnPivot selezionata.](media/data-flow/unpivot6.png "Opzioni UnPivot 3")

La chiave UnPivot è la colonna da cui verrà applicata la trasformazione ADF dalla colonna alla riga. Per impostazione predefinita, ogni valore univoco nel set di dati per questo campo verrà trasformato in una riga. Tuttavia, facoltativamente, è possibile immettere i valori del set di dati che si desidera trasformare in valori di riga.

## <a name="unpivoted-columns"></a>Colonne trasformate tramite UnPivot

![Screenshot mostra le impostazioni di UnPivot con la scheda Anteprima dati selezionata.](media/data-flow//unpivot7.png "Opzioni UnPivot 4")

Infine, scegliere il nome della colonna per archiviare i valori per le colonne trasformate tramite UnPivot che vengono trasformate in righe.

Opzionale È possibile eliminare righe con valori null.

Ad esempio, SumCost è il nome della colonna scelto nell'esempio condiviso sopra.

![Immagine che mostra le colonne PO, vendor e Fruit prima e dopo una trasformazione unipivot usando la colonna Fruit come chiave unipivot.](media/data-flow/unpivot3.png)

Impostando la disposizione delle colonne su "Normal", tutte le nuove colonne trasformate tramite UnPivot vengono raggruppate in base a un singolo valore. Impostando la disposizione delle colonne su "laterale" si raggruppano le nuove colonne trasformate tramite UnPivot generate da una colonna esistente.

![Screenshot mostra il risultato della trasformazione.](media/data-flow//unpivot7.png "Opzioni UnPivot 5")

Il set di risultati finale dei dati trasformati tramite UnPivot mostra i totali delle colonne, ora trasformati tramite UnPivot in valori di riga separati.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare la [trasformazione pivot](data-flow-pivot.md) per trasformare le righe in colonne.
