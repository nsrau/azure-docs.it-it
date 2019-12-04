---
title: Pianificare le attività per gestire i dati contigui
description: Creare ed eseguire attività ricorrenti che gestiscono dati contigui usando finestre scorrevoli in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786912"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Pianificare ed eseguire attività per dati contigui usando il trigger finestra temporale scorrevole in app per la logica di Azure

Per eseguire regolarmente attività, processi o processi che devono gestire i dati in blocchi contigui, è possibile avviare il flusso di lavoro dell'app per la logica con il trigger della **finestra temporale scorrevole** . È possibile impostare una data e un'ora, nonché un fuso orario per l'avvio del flusso di lavoro e una ricorrenza per la ripetizione del flusso di lavoro. Se vengono perse ricorrenze per qualsiasi motivo, questo trigger elabora le ricorrenze perse. Ad esempio, quando si sincronizzano i dati tra il database e l'archiviazione di backup, utilizzare il trigger finestra temporale scorrevole in modo che i dati vengano sincronizzati senza interruzioni. Per altre informazioni sui trigger di pianificazione e sulle azioni predefinite, vedere [pianificare ed eseguire attività ricorrenti automatizzate, attività e flussi di lavoro con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ecco alcuni modelli supportati da questo trigger:

* Esecuzione immediata e ripetizione ogni *n* secondi, minuti o ore.

* Iniziare da una data e un'ora specifiche, quindi eseguire e ripetere ogni *n* secondi, minuti o ore. Con questo trigger è possibile specificare un'ora di inizio nel passato, che esegue tutte le ricorrenze precedenti.

* Ritardare ogni ricorrenza per una durata specifica prima dell'esecuzione.

Per le differenze tra questo trigger e il trigger di ricorrenza o per altre informazioni sulla pianificazione di flussi di lavoro ricorrenti, vedere [pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se si vuole attivare l'app per la logica ed eseguire solo una volta in futuro, vedere [eseguire processi solo una volta](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* Informazioni di base sulle app per la [logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, scoprire [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Aggiungi trigger finestra temporale scorrevole

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Dopo aver visualizzato Progettazione app per la logica, nella casella di ricerca immettere "finestra temporale scorrevole" come filtro. Dall'elenco trigger selezionare questo trigger come primo passaggio nel flusso di lavoro dell'app per la logica: **finestra temporale scorrevole**

   ![Selezionare il trigger finestra temporale scorrevole](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana.

   ![Impostare intervallo e frequenza](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Proprietà | Obbligatoria | Nome JSON | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **Interval** | SÌ | interval | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p>- Ora: 1-12.000 ore </br>- Minuto: 1-72.000 minuti </br>- Secondo: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "hour", la ricorrenza è ogni 6 ore. |
   | **Frequenza** | SÌ | frequency | Stringa | Unità di tempo per la ricorrenza: **secondo**, **minuto**o **ora** |
   ||||||

   ![Opzioni di ricorrenza avanzate](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Per altre opzioni di ricorrenza, aprire l'elenco **Aggiungi nuovo parametro** . 
   Tutte le opzioni selezionate vengono visualizzate nel trigger dopo la selezione.

   | Proprietà | Obbligatoria | Nome JSON | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **Ritardo** | No | delay | Stringa | Durata per ritardare ogni ricorrenza utilizzando la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso orario** | No | timeZone | Stringa | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | No | startTime | Stringa | Specificare una data e un'ora di inizio nel formato seguente: <p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Quindi, ad esempio, se si desidera il 18 settembre 2017 alle 2:00 PM, specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le ricorrenze avanzate il trigger non viene attivato prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. A questo punto, compilare il flusso di lavoro rimanente con altre azioni. Per altre azioni che è possibile aggiungere, vedere [connettori per app per la logica di Azure](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definizione del flusso di lavoro-finestra temporale scorrevole

Nella definizione del flusso di lavoro sottostante dell'app per la logica, che usa JSON, è possibile visualizzare la definizione del trigger della finestra temporale scorrevole con le opzioni selezionate. Per visualizzare questa definizione, scegliere **visualizzazione codice**sulla barra degli strumenti della finestra di progettazione. Per tornare alla finestra di progettazione, scegliere sulla barra degli strumenti della finestra di progettazione, **finestra**di progettazione.

Questo esempio illustra come una definizione di trigger della finestra temporale scorrevole potrebbe apparire in una definizione del flusso di lavoro sottostante in cui il ritardo per ogni ricorrenza è di cinque secondi per una ricorrenza oraria:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Ritardare l'azione successiva nei flussi di lavoro](../connectors/connectors-native-delay.md)
* [Connettori per app per la logica](../connectors/apis-list.md)