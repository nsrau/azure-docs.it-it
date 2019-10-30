---
title: Modellazione dei dati in Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sulla modellazione dei dati in Anteprima di Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 81c2c2af78f5f066e1b27e14fa774df04d7c5868
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063995"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modellazione dei dati in Anteprima di Azure Time Series Insights

Questo articolo descrive come usare il modello Time Series in Azure Time Series Insights Preview. e illustra in dettaglio alcuni scenari di dati comuni.

Per altre informazioni su come usare l'aggiornamento, vedere [Strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipi

### <a name="create-a-single-type"></a>Creare un singolo tipo

1. Passare al pannello di selezione del modello Time Series e scegliere **tipi** dal menu. Comprimere il pannello per concentrarsi sui tipi di modello Time Series.

    [![riquadro "tipi"](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selezionare **+ Aggiungi**.
1. Immettere tutti i dettagli relativi ai tipi e selezionare **Crea**. Questa azione crea i tipi nell'ambiente.

    [![selezioni per l'aggiunta di un tipo](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Caricamento in blocco di uno o più tipi

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload del tipo.
1. Selezionare **Carica**.

    [Selezione ![per il caricamento bulk di uno o più tipi](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Modificare un singolo tipo

1. Selezionare il tipo e quindi selezionare **Modifica**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![selezioni per la modifica di un tipo](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Eliminare un tipo

1. Selezionare il tipo e quindi selezionare **Elimina**.
1. Se nessuna istanza è associata i tipi, viene eliminato.

    [![pulsante "Elimina"](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Gerarchie

### <a name="create-a-single-hierarchy"></a>Creare una singola gerarchia

1. Passare al pannello di selezione del modello Time Series e selezionare **gerarchie** dal menu. Comprimere il pannello per concentrarsi sulle gerarchie del modello Time Series.

    [riquadro !["gerarchie"](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selezionare **+ Aggiungi**.

    [![pulsante "Aggiungi"](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selezionare **+ Aggiungi livello** nel riquadro di destra.

    [![pulsante "Aggiungi livello"](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Immettere i dettagli della gerarchia e selezionare **Crea**.

    [![i dettagli e il pulsante "Crea" di ObjectType](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Caricamento in blocco di una o più gerarchie

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload della gerarchia.
1. Selezionare **Carica**.

    [![selezioni per il caricamento bulk di gerarchie](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modificare una singola gerarchia

1. Selezionare la gerarchia e quindi selezionare **Modifica**.
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![selezioni per la modifica di una singola gerarchia](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Eliminare una gerarchia

1. Selezionare la gerarchia e selezionare **Elimina**. 
1. Se nessuna istanza è associata alla gerarchia, viene eliminata.

    [![pulsante "Elimina"](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Istanze

### <a name="create-a-single-instance"></a>Creare una singola istanza

1. Passare al pannello di selezione del modello Time Series e selezionare **istanze** dal menu. Comprimere il pannello per concentrarsi sulle istanze del modello Time Series.

    [![riquadro "istanze"](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selezionare **Aggiungi**.

    [![selezioni per l'aggiunta di un'istanza](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Immettere i dettagli dell'istanza, selezionare l'associazione con il tipo e la gerarchia e selezionare **Crea**.

### <a name="bulk-upload-one-or-more-instances"></a>Caricamento in blocco di una o più istanze

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload delle istanze.

    [![selezioni per il caricamento bulk di una o più istanze](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Selezionare **Carica**.

### <a name="edit-a-single-instance"></a>Modificare una singola istanza

1. Selezionare l'istanza e quindi selezionare **Modifica**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![selezioni per la modifica di una singola istanza](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sul modello Time Series, vedere [modellazione dei dati](./time-series-insights-update-tsm.md).

- Per altre informazioni sull'anteprima, vedere [Visualizzare dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Per informazioni sulle forme JSON supportate, vedere [forme JSON supportate](./time-series-insights-send-events.md#supported-json-shapes).
