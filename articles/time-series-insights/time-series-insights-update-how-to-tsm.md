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
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 73384868deb8f0e33b233e363c42a12adbcbe402
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237556"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modellazione dei dati in Anteprima di Azure Time Series Insights

Questo documento descrive come usare i modelli di serie temporali seguendo Anteprima di Azure Time Series Insights e illustra in dettaglio alcuni scenari di dati comuni.

Per altre informazioni su come usare l'aggiornamento, vedere [Strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipi

### <a name="create-a-single-type"></a>Creare un singolo tipo

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Tipi** dal menu. Comprimere il pannello per esaminare i tipi di modelli di serie temporali.

    [![Creare un singolo tipo](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. Selezionare **Aggiungi**.
1. Immettere tutti i dettagli relativi ai tipi e selezionare **Crea**. Questa azione crea i tipi nell'ambiente.

    [![Aggiungere un tipo](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Caricamento in blocco di uno o più tipi

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload del tipo.
1. Selezionare **Carica**.

    [![Carica JSON](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>Modificare un singolo tipo

1. Selezionare il tipo e quindi selezionare **Modifica**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Modificare un tipo](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>Eliminare un tipo

1. Selezionare il tipo e quindi selezionare **Elimina**.
1. Se nessuna istanza è associata i tipi, viene eliminato.

    [![Eliminare un tipo](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>Gerarchie

### <a name="create-a-single-hierarchy"></a>Creare una singola gerarchia

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Gerarchie** dal menu. Comprimere il pannello per esaminare i tipi di gerarchie di serie temporali.

    [![Selezionare le gerarchie](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. Selezionare **Aggiungi**.

    [![Aggiungere una gerarchia](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. Selezionare **Aggiungi livello** nel riquadro a destra.

    [![Aggiungere un livello](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. Immettere i dettagli della gerarchia e selezionare **Crea**.

    [![Creare un livello](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Caricamento in blocco di una o più gerarchie

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload della gerarchia.
1. Selezionare **Carica**.

    [![Gerarchie di caricamento BULK](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modificare una singola gerarchia

1. Selezionare la gerarchia e quindi selezionare **Modifica**.
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Modificare una singola gerarchia](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Eliminare una gerarchia

1. Selezionare la gerarchia e quindi selezionare **Elimina**. 
1. Se nessuna istanza è associata alla gerarchia, viene eliminata.

    [![Eliminare una gerarchia](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>Istanze

### <a name="create-a-single-instance"></a>Creare una singola istanza

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Istanze** dal menu. Comprimere il pannello per esaminare le istanze di modelli di serie temporali.

    [![Creare una singola istanza](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. Selezionare **Aggiungi**.

    [![Aggiungere un'istanza](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. Immettere i dettagli dell'istanza, selezionare l'associazione con il tipo e la gerarchia e selezionare **Crea**.

### <a name="bulk-upload-one-or-more-instances"></a>Caricamento in blocco di una o più istanze

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload delle istanze.

    [![Una o più istanze il caricamento in blocco](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. Selezionare **Carica**.

### <a name="edit-a-single-instance"></a>Modificare una singola istanza

1. Selezionare l'istanza e quindi selezionare **Modifica**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Modificare una singola istanza](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli di serie temporali, vedere [Modellazione di dati](./time-series-insights-update-tsm.md).

- Per altre informazioni sull'anteprima, vedere [Visualizzare dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Per informazioni sulle forme JSON supportate, vedere [Forme JSON supportate](./time-series-insights-send-events.md#json).
