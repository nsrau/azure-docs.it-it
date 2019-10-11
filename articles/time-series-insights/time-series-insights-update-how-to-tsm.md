---
title: Modellazione dei dati in Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sulla modellazione dei dati in Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273735"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modellazione dei dati in Anteprima di Azure Time Series Insights

Questo documento descrive come usare i modelli di serie temporali seguendo Anteprima di Azure Time Series Insights e illustra in dettaglio alcuni scenari di dati comuni.

Per altre informazioni su come usare l'aggiornamento, vedere [Strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipi

### <a name="create-a-single-type"></a>Creare un singolo tipo

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Tipi** dal menu. Comprimere il pannello per esaminare i tipi di modelli di serie temporali.

    [@no__t 1Create un unico tipo](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selezionare **+ Aggiungi**.
1. Immettere tutti i dettagli relativi ai tipi e selezionare **Crea**. Questa azione crea i tipi nell'ambiente.

    [![Add un tipo](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Caricamento in blocco di uno o più tipi

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload del tipo.
1. Selezionare **Carica**.

    [JSON @no__t 1Upload](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Modificare un singolo tipo

1. Selezionare il tipo e quindi selezionare **Modifica**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Edit un tipo](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Eliminare un tipo

1. Selezionare il tipo e quindi selezionare **Elimina**.
1. Se nessuna istanza è associata i tipi, viene eliminato.

    [![Delete un tipo](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Gerarchie

### <a name="create-a-single-hierarchy"></a>Creare una singola gerarchia

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Gerarchie** dal menu. Comprimere il pannello per esaminare i tipi di gerarchie di serie temporali.

    [gerarchie ![Selezionare](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selezionare **+ Aggiungi**.

    [@no__t 1Add una gerarchia](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selezionare **+ Aggiungi livello** nel riquadro di destra.

    [![Add un livello](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Immettere i dettagli della gerarchia e selezionare **Crea**.

    [![Create un livello](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Caricamento in blocco di una o più gerarchie

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload della gerarchia.
1. Selezionare **Carica**.

    [gerarchie di caricamento ![Bulk](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modificare una singola gerarchia

1. Selezionare la gerarchia e quindi selezionare **Modifica**.
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [@no__t 1Edit una singola gerarchia](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Eliminare una gerarchia

1. Selezionare la gerarchia e quindi selezionare **Elimina**. 
1. Se nessuna istanza è associata alla gerarchia, viene eliminata.

    [@no__t 1Delete una gerarchia](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Istanze

### <a name="create-a-single-instance"></a>Creare una singola istanza

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Istanze** dal menu. Comprimere il pannello per esaminare le istanze di modelli di serie temporali.

    [@no__t 1Create una singola istanza](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selezionare **Aggiungi**.

    [@no__t 1Add un'istanza](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Immettere i dettagli dell'istanza, selezionare l'associazione con il tipo e la gerarchia e selezionare **Crea**.

### <a name="bulk-upload-one-or-more-instances"></a>Caricamento in blocco di una o più istanze

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload delle istanze.

    [![Bulk caricare una o più istanze](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Selezionare **Carica**.

### <a name="edit-a-single-instance"></a>Modificare una singola istanza

1. Selezionare l'istanza e quindi selezionare **Modifica**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [@no__t 1Edit una singola istanza](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli di serie temporali, vedere [Modellazione di dati](./time-series-insights-update-tsm.md).

- Per altre informazioni sull'anteprima, vedere [Visualizzare dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Per informazioni sulle forme JSON supportate, vedere [Forme JSON supportate](./time-series-insights-send-events.md#supported-json-shapes).
