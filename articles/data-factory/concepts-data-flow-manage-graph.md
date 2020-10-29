---
title: Gestione del grafico del flusso di dati di mapping
description: Come gestire e modificare in modo efficace il grafico del flusso di dati di mapping
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 15a4a0ea78ef169480d4494683cb929cfefd16f2
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026973"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Gestione del grafico del flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I flussi di dati di mapping vengono creati utilizzando un'area di progettazione come il grafico del flusso di dati. Nel grafico, la logica di trasformazione viene compilata da sinistra a destra e vengono aggiunti altri flussi di dati dall'alto verso il basso. Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente.

![Canvas](media/data-flow/canvas2.png "Canvas")

Poiché i flussi di dati sono più complessi, utilizzare i meccanismi seguenti per esplorare e gestire in modo efficace il grafico del flusso di dati. 

## <a name="moving-transformations"></a>Trasferimento di trasformazioni

Nei flussi di dati di mapping, un set di logica di trasformazione connessa è noto come **flusso** . Il campo del **flusso in ingresso** determina quale flusso di dati sta inserendo la trasformazione corrente. Ogni trasformazione ha uno o due flussi in ingresso a seconda della funzione e rappresenta un flusso di output. Lo schema di output dei flussi in ingresso determina i metadati della colonna a cui è possibile fare riferimento dalla trasformazione corrente.

![Sposta nodo](media/data-flow/move-nodes.png "Sposta nodo")

Diversamente dall'area di disegno della pipeline, le trasformazioni del flusso di dati non vengono modificate utilizzando un modello di trascinamento della selezione. Per modificare il flusso in ingresso o "spostare" una trasformazione, scegliere un valore diverso dall'elenco a discesa **flusso in ingresso** . Quando si esegue questa operazione, tutte le trasformazioni a valle vengono spostate insieme alla trasformazione modificata. Il grafico verrà aggiornato automaticamente per mostrare il nuovo flusso logico. Se si modifica il flusso in ingresso in una trasformazione che dispone già di una trasformazione a valle, verrà creato un nuovo flusso di dati di ramo o parallelo. Altre informazioni sui [nuovi rami nel flusso di dati di mapping](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Nascondi grafico e Mostra grafico

Quando si modifica la trasformazione, è possibile espandere il pannello di configurazione in modo che occupi l'intera area di disegno, nascondendo il grafico. Fare clic sulla freccia di espansione verso l'alto posizionata sul lato destro dell'area di disegno.

![Nascondi grafico](media/data-flow/hide-graph.png "Nascondi grafico")

Quando il grafico è nascosto, è possibile spostarsi tra le trasformazioni all'interno di un flusso facendo clic su **Avanti** o su **indietro** . Fare clic sulla freccia di espansione rivolta verso il basso per visualizzare il grafico.

![Mostra grafico](media/data-flow/show-graph.png "Mostra grafico")

## <a name="searching-for-transformations"></a>Ricerca di trasformazioni

Per trovare rapidamente una trasformazione nel grafico, fare clic sull'icona di **ricerca** sopra l'impostazione dello zoom.

![Screenshot che mostra il pulsante Cerca.](media/data-flow/search-1.png "Grafico di ricerca")

Per individuare una trasformazione, è possibile eseguire una ricerca in base al nome della trasformazione o alla descrizione.

![Screenshot che mostra la casella di testo Cerca.](media/data-flow/search-2.png "Grafico di ricerca")

## <a name="hide-reference-nodes"></a>Nascondi nodi di riferimento

Se il flusso di dati include le trasformazioni join, Lookup, EXISTS o Union, il flusso di dati Mostra i nodi di riferimento a tutti i flussi in ingresso. Se si desidera ridurre al minimo la quantità di spazio verticale, è possibile ridurre al minimo i nodi di riferimento. A tale scopo, fare clic con il pulsante destro del mouse sull'area di disegno e scegliere **Nascondi nodi di riferimento** .

![Nascondi nodi di riferimento](media/data-flow/hide-reference-nodes.png "Nascondi nodi di riferimento")

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la logica del flusso di dati, attivare la [modalità di debug](concepts-data-flow-debug-mode.md) e testarla in un'anteprima dei dati.
