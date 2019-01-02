---
title: Modellazione di dati in Anteprima di Time Series Insights - Modellazione di dati in Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sulla modellazione dei dati in Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: e68bc01d2c0781333454fa753992d0136fac0c06
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269091"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modellazione dei dati in Anteprima di Azure Time Series Insights

Questo documento descrive come usare i modelli di serie temporali seguendo Anteprima di Azure Time Series Insights e illustra in dettaglio alcuni scenari di dati comuni.

Per altre informazioni su come usare l'aggiornamento, vedere [Strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipi

### <a name="create-a-single-type"></a>Creare un singolo tipo

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Tipi** dal menu. Comprimere il pannello per esaminare i tipi di modelli di serie temporali.

    ![Portal_one][1]

1. Selezionare **Aggiungi**.
1. Immettere tutti i dettagli relativi ai tipi e selezionare **Crea**. Questa azione crea i tipi nell'ambiente.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Caricamento in blocco di uno o più tipi

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload del tipo.
1. Selezionare **Carica**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Modificare un singolo tipo

Selezionare il tipo e quindi selezionare **Modifica**. Apportare le modifiche necessarie e quindi selezionare **Salva**.

![Portal_four][4]

### <a name="delete-a-type"></a>Eliminare un tipo

Selezionare il tipo e quindi selezionare **Elimina**. Se nessuna istanza è associata i tipi, viene eliminato.

![Portal_five][5]

## <a name="hierarchies"></a>Gerarchie

### <a name="create-a-single-hierarchy"></a>Creare una singola gerarchia

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Gerarchie** dal menu. Comprimere il pannello per esaminare i tipi di gerarchie di serie temporali.

    ![Portal_six][6]

1. Selezionare **Aggiungi**.

    ![Portal_seven][7]

1. Selezionare **Aggiungi livello** nel riquadro a destra.

    ![Portal_eight][8]

1. Immettere i dettagli della gerarchia e selezionare **Crea**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Caricamento in blocco di una o più gerarchie

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload della gerarchia.
1. Selezionare **Carica**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Modificare una singola gerarchia

Selezionare la gerarchia e quindi selezionare **Modifica**. Apportare le modifiche necessarie e quindi selezionare **Salva**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Eliminare una gerarchia

Selezionare la gerarchia e quindi selezionare **Elimina**. Se nessuna istanza è associata alla gerarchia, viene eliminata.

![Portal_twelve][12]

## <a name="instances"></a>Istanze

### <a name="create-a-single-instance"></a>Creare una singola istanza

1. Passare al pannello di selezione dei modelli di serie temporali e scegliere **Istanze** dal menu. Comprimere il pannello per esaminare le istanze di modelli di serie temporali.

    ![Portal_thirteen][13]

1. Selezionare **Aggiungi**.

    ![Portal_thirteen][14]

1. Immettere i dettagli dell'istanza, selezionare l'associazione con il tipo e la gerarchia e selezionare **Crea**.

### <a name="bulk-upload-one-or-more-instances"></a>Caricamento in blocco di una o più istanze

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload delle istanze.

    ![Portal_fifteen][15]

1. Selezionare **Carica**.

### <a name="edit-a-single-instance"></a>Modificare una singola istanza

Selezionare l'istanza e quindi selezionare **Modifica**. Apportare le modifiche necessarie e quindi selezionare **Salva**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Eliminare un'istanza

Selezionare l'istanza e quindi selezionare **Elimina**. Se nessun evento è associato alle istanze, viene eliminata.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli di serie temporali, vedere [Modellazione di dati](./time-series-insights-update-tsm.md).
- Per altre informazioni sull'anteprima, vedere [Visualizzare dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).
- Per informazioni sulle forme JSON supportate, vedere [Forme JSON supportate](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png