---
title: Modellazione dei dati in ambienti di anteprima - Azure Time Series Insights Documenti Microsoft
description: Informazioni sulla modellazione dei dati in Azure Time Series Insights Preview.Learn about data modeling in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470752"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modellazione dei dati in Anteprima di Azure Time Series Insights

Questo articolo descrive come usare il modello Time Series in Azure Time Series Insights Preview.This article describes how to work with Time Series Model in Azure Time Series Insights Preview. e illustra in dettaglio alcuni scenari di dati comuni.

> [!TIP]
> * Informazioni sul [modello Anteprima time Series](time-series-insights-update-tsm.md).
> * Altre informazioni sull'esplorazione dell'interfaccia utente di anteprima in [Esplora anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Istanze

Azure Time Series Insights Explorer supporta le operazioni **CREATE**, **READ**, **UPDATE**e **DELETE** dell'istanza all'interno del browser. 

Per iniziare, selezionare la vista **Modello** dalla vista **Analizza** di Time Series Insights.

### <a name="create-a-single-instance"></a>Creare una singola istanza

1. Vai al pannello del selettore Modello Time Series e seleziona **Istanze** dal menu. Verranno visualizzate tutte le istanze associate all'ambiente Time Series Insights selezionato.

    [![Creare una singola istanza selezionando prima Istanze.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selezionare **+ Aggiungi**.

    [![Aggiungere un'istanza selezionando il pulsante Aggiungi.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Immettere i dettagli dell'istanza, selezionare l'associazione con il tipo e la gerarchia e selezionare **Crea**.

### <a name="bulk-upload-one-or-more-instances"></a>Caricamento in blocco di una o più istanze

> [!TIP]
> È possibile salvare le istanze sul desktop in JSON. Il file JSON scaricato può quindi essere caricato tramite i passaggi seguenti.

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload delle istanze.

    [![Caricamento bulk delle istanze tramite JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selezionare **Carica**.

### <a name="edit-a-single-instance"></a>Modificare una singola istanza

1. Selezionare l'istanza e selezionare l'icona di **modifica** o **matita**. 
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Modificare una singola istanza.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Eliminare un'istanza

1. Selezionare l'istanza e selezionare l'icona **di eliminazione** o **cestino**.

   [![Eliminare un'istanza selezionando Elimina.Delete an instance by selecting Delete.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confermare l'eliminazione selezionando **Elimina**.

> [!NOTE]
> Un'istanza deve superare correttamente un controllo di convalida del campo da eliminare.

## <a name="hierarchies"></a>Gerarchie

Azure Time Series Insights Explorer supporta le operazioni **CREATE**, **READ**, **UPDATE**e **DELETE** della gerarchia all'interno del browser. 

Per iniziare, selezionare la vista **Modello** dalla vista **Analizza** di Time Series Insights.

### <a name="create-a-single-hierarchy"></a>Creare una singola gerarchia

1. Passare al pannello del selettore Modello Time Series e selezionare **Gerarchie** dal menu. Verranno visualizzate tutte le gerarchie associate all'ambiente Time Series Insights selezionato.

    [![Creare una gerarchia tramite il riquadro.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selezionare **+ Aggiungi**.

    [![Pulsante Gerarchia - Aggiungi.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Nel riquadro di destra, **selezionare Aggiungi livello.**

    [![Aggiungere un livello alla gerarchia.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Immettere i dettagli della gerarchia e selezionare **Salva**.

    [![Specificare i dettagli della gerarchia.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Caricamento in blocco di una o più gerarchie

> [!TIP]
> È possibile salvare le gerarchie sul desktop in JSON. Il file JSON scaricato può quindi essere caricato tramite i passaggi seguenti.

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload della gerarchia.
1. Selezionare **Carica**.

    [![Selezioni per il caricamento in blocco delle gerarchie.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modificare una singola gerarchia

1. Selezionare la gerarchia e selezionare l'icona di **modifica** o **matita**.
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Selezioni per la modifica di una singola gerarchia.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Eliminare una gerarchia

1. Selezionare la gerarchia e selezionare l'icona **di eliminazione** o **cestino**. 

    [![Eliminare una gerarchia selezionando il pulsante Elimina.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confermare l'eliminazione selezionando **Elimina**.

## <a name="types"></a>Tipi

Azure Time Series Insights Explorer supporta le operazioni **CREATE**, **READ**, **UPDATE**e **DELETE** all'interno del browser. 

Per iniziare, selezionare la vista **Modello** dalla vista **Analizza** di Time Series Insights.

### <a name="create-a-single-type"></a>Creare un singolo tipo

1. Vai al pannello del selettore Modello Time Series e seleziona **Tipi** dal menu. Verranno visualizzati tutti i tipi associati all'ambiente Time Series Insights selezionato.

    [![Riquadro Tipi di modello Time Series.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selezionare **Aggiungi** per visualizzare il modale **Aggiungi un nuovo tipo** popup.
1. Immettere le proprietà e le variabili per il tipo. Una volta inserito, selezionare **Salva**. 

    [![Impostazioni di configurazione per aggiungere un tipo.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Caricamento in blocco di uno o più tipi

> [!TIP]
> È possibile salvare i tipi sul desktop in JSON. Il file JSON scaricato può quindi essere caricato tramite i passaggi seguenti.

1. Selezionare **Carica JSON**.
1. Selezionare il file che contiene il payload del tipo.
1. Selezionare **Carica**.

    [![Opzioni di caricamento di tipi di massa.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Modificare un singolo tipo

1. Selezionare il tipo e selezionare l'icona di **modifica** o **matita**.
1. Apportare le modifiche necessarie e quindi selezionare **Salva**.

    [![Modificare un tipo nel riquadro.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Eliminare un tipo

1. Selezionare il tipo e selezionare l'icona **di eliminazione** o **cestino**. .

   [![Eliminare un tipo selezionando Elimina.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confermare l'eliminazione selezionando **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sul modello Time Series, leggere [Modellazione dei dati](./time-series-insights-update-tsm.md).

- Per altre informazioni sull'anteprima, vedere [Visualizzare dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Per informazioni sulle forme JSON supportate, vedere [Forme JSON supportate](./time-series-insights-send-events.md#supported-json-shapes).
