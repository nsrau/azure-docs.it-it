---
title: Pianificare le attività per gestire i dati contigui
description: Creare ed eseguire attività ricorrenti che gestiscono dati contigui usando le finestre scorrevoli nelle app per la logica di AzureCreate and run recurring tasks that handle contiguous data by using sliding windows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786912"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Pianificare ed eseguire attività per dati contigui usando il trigger Finestra scorrevole in App per la logica di AzureSchedule and run tasks for contiguous data by using the Sliding Window trigger in Azure Logic Apps

Per eseguire regolarmente attività, processi o processi che devono gestire i dati in blocchi contigui, è possibile avviare il flusso di lavoro dell'app per la logica con il trigger **Finestra scorrevole.** È possibile impostare una data e un'ora, nonché un fuso orario per l'avvio del flusso di lavoro e una ricorrenza per la ripetizione del flusso di lavoro. Se le ricorrenze vengono perse per qualsiasi motivo, questo trigger elabora le ricorrenze perse. Ad esempio, quando si sincronizzano i dati tra il database e l'archiviazione di backup, utilizzare il trigger Finestra scorrevole in modo che i dati vengano sincronizzati senza incorrere in spazi vuoti. Per altre informazioni sui trigger e sulle azioni di pianificazione predefiniti, vedere [Pianificare ed eseguire flussi di lavoro automatici, attività e flussi di lavoro ricorrenti con App per la logica](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)di Azure.For more information about the built-in Schedule triggers and actions, see Schedule and run recurring automated, tasks, and workflows with Azure Logic Apps.

Ecco alcuni modelli supportati da questo trigger:Here are some patterns that this trigger supports:

* Esegui immediatamente e ripeti ogni *n* numero di secondi, minuti o ore.

* Iniziare da una data e un'ora specifiche, quindi eseguire e ripetere ogni *n* numero di secondi, minuti o ore. Con questo trigger, è possibile specificare un'ora di inizio nel passato, che esegue tutte le ricorrenze passate.

* Ritardare ogni ricorrenza per una durata specifica prima dell'esecuzione.

Per differenze tra questo trigger e il trigger Ricorrenza o per altre informazioni sulla pianificazione dei flussi di lavoro ricorrenti, vedere Pianificare ed eseguire attività, processi e flussi di lavoro automatici ricorrenti con app per la logica di Azure.For differences between this trigger and the Recurrence trigger or for more information about scheduling recurring workflows, see Schedule and [run recurring automated tasks, processes, and workflows with Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se si vuole attivare l'app per la logica ed eseguire una sola volta in futuro, vedere [Eseguire processi una sola volta](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione, è possibile [iscriversi per ottenere un account Azure gratuito.](https://azure.microsoft.com/free/)

* Informazioni di base sulle [app per la logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, scoprire [come creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-sliding-window-trigger"></a>Aggiungi trigger finestra scorrevole

1. Accedere al [portale](https://portal.azure.com)di Azure . Creare un'app per la logica vuota.

1. Dopo la visualizzazione Progettazione app logica, nella casella di ricerca immettere "finestra scorrevole" come filtro. Nell'elenco dei trigger selezionare questo trigger come primo passaggio del flusso di lavoro dell'app per la logica: **Finestra scorrevole**

   ![Selezionare il trigger "Sliding Window"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana.

   ![Impostare intervallo e frequenza](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Proprietà | Obbligatoria | Nome JSON | Type | Descrizione |
   |----------|----------|-----------|------|-------------|
   | **Intervallo** | Sì | interval | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p>- Ora: 1-12.000 ore </br>- Minuto: 1-72.000 minuti </br>- Secondo: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Ora", la ricorrenza è ogni 6 ore. |
   | **Frequenza** | Sì | frequency | string | L'unità di tempo per la ricorrenza: **Secondo**, **Minuto**o **Ora** |
   ||||||

   ![Opzioni di ricorrenza avanzate](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Per altre opzioni di ricorrenza, aprire l'elenco **Aggiungi nuovo parametro.** 
   Tutte le opzioni selezionate vengono visualizzate sul trigger dopo la selezione.

   | Proprietà | Obbligatoria | Nome JSON | Type | Descrizione |
   |----------|----------|-----------|------|-------------|
   | **Ritardo** | No | delay | string | La durata di ritardo di ogni ricorrenza utilizzando la [specifica ISO 8601 date time](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso orario** | No | timeZone | string | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | No | startTime | string | Specificare una data e un'ora di inizio in questo formato:Provide a start date and time in this format: <p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Ad esempio, se si desidera 18 settembre 2017 alle 14:00, quindi specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le ricorrenze avanzate, il trigger non viene attivato prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Creare ora il flusso di lavoro rimanente con altre azioni. Per altre azioni che è possibile aggiungere, vedere [Connettori per app per la logica](../connectors/apis-list.md)di Azure.For more actions that you can add, see Connectors for Azure Logic Apps .

## <a name="workflow-definition---sliding-window"></a>Definizione flusso di lavoro - Finestra scorrevole

Nella definizione del flusso di lavoro sottostante dell'app per la logica, che usa JSON, è possibile visualizzare la definizione del trigger della finestra scorrevole con le opzioni scelte. Per visualizzare questa definizione, nella barra degli strumenti della finestra di progettazione scegliere **La visualizzazione Codice**. Per tornare alla finestra di progettazione, scegliere nella barra degli strumenti della finestra di **progettazione**Designer .

Questo esempio mostra l'aspetto di una definizione di trigger della finestra scorrevole in una definizione di flusso di lavoro sottostante in cui il ritardo per ogni ricorrenza è di cinque secondi per una ricorrenza oraria:This example shows how a Sliding Window trigger definition might look in an underlying workflow definition where the delay for each recurrence is five seconds for an hourly recurrence:

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
* [Connettori per App per la logica](../connectors/apis-list.md)