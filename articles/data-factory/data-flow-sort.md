---
title: Mapping di trasformazione Ordinamento del flusso di dati di Azure Data Factory
description: Data Factory di Azure la trasformazione ordinamento dei dati di Mapping
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: d0482d1081c16dc89e7371c4c33de9b2bb4e4c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348253"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Trasformazioni di ordinamento basato sul flusso di dati di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Impostazioni per l'ordinamento](media/data-flow/sort.png "Ordinamento")

La trasformazione Ordinamento consente di ordinare le righe in ingresso nel flusso di dati corrente. Le righe in uscita dalla trasformazione Ordinamento seguiranno poi le regole di ordinamento impostate. È possibile scegliere singole colonne e disporle in ordine crescente o decrescente, usando l'indicatore freccia accanto a ogni campo. Se è necessario modificare la colonna prima di applicare l'ordinamento, fare clic su "Computed Columns" (Colonne calcolate) per avviare l'editor espressioni. Si avrà così l'opportunità di creare un'espressione per l'operazione di ordinamento invece di limitarsi a selezionare semplicemente una colonna per l'ordinamento.

## <a name="case-insensitive"></a>Non fa distinzione tra maiuscole e minuscole
È possibile attivare "Case insensitive" (Senza distinzione tra maiuscole e minuscole) se si vuole ignorare la combinazione di maiuscole/minuscole quando si ordinano i campi stringa o di testo.

"Sort Only Within Partitions" (Ordina solo all'interno delle partizioni) sfrutta il partizionamento dei dati Spark. Ordinando i dati in ingresso solo all'interno di ogni partizione, i flussi di dati possono ordinare i dati partizionati invece dell'intero flusso di dati.

È possibile ridisporre tutte le condizioni di ordinamento nella trasformazione Ordinamento. Pertanto, se è necessario spostare una colonna più in alto nella precedenza di ordinamento, selezionare tale riga con il mouse e trascinarla più in alto o più in basso nell'elenco di ordinamento.

Effetti del partizionamento sull'ordinamento

ADF Data Flow viene eseguito su cluster Spark di Big Data con dati distribuiti tra più nodi e partizioni. È importante tenere presente questo aspetto durante la progettazione del flusso di dati, se si dipende dalla trasformazione Ordinamento per mantenere i dati nello stesso ordine. Se si sceglie di ripartizionare i dati in una trasformazione successiva, si potrebbe perdere l'ordinamento a causa di tale rimaneggiamento dei dati.

## <a name="next-steps"></a>Passaggi successivi

Dopo l'ordinamento, è possibile usare il [trasformazione aggregazione](data-flow-aggregate.md)
