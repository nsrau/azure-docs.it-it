---
title: Pianificare attività ricorrenti con trigger di finestra temporale scorrevole - App per la logica di Azure
description: Pianificare ed eseguire le attività automatizzate ricorrente e flussi di lavoro con il trigger di finestra temporale scorrevole nelle App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299503"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Creare, pianificare ed eseguire le attività ricorrenti e flussi di lavoro con il trigger di finestra temporale scorrevole nelle App per la logica di Azure

Per eseguire regolarmente le attività, processi o i processi che devono gestire i dati in blocchi continui, è possibile avviare il flusso di lavoro app per la logica con il **finestra temporale scorrevole - pianificazione** trigger. È possibile impostare una data e ora, nonché un fuso orario per l'avvio del flusso di lavoro e una ricorrenza per la ripetizione del flusso di lavoro. Se le ricorrenze non sono installate per qualsiasi motivo, questo trigger elabora le ricorrenze mancanti. Ad esempio, durante la sincronizzazione dei dati tra il database e l'archiviazione dei backup, usare il trigger di finestra temporale scorrevole in modo che i dati viene sincronizzati senza incorrere in gap. Per altre informazioni sulle azioni e trigger di pianificazione predefinite, vedere [pianificazione e ricorrente automatizzato di esecuzione, attività e flussi di lavoro con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ecco alcuni modelli che questo trigger supporta:

* Esecuzione immediata e ripetizione ogni *n* numero di secondi, minuti o ore.

* Iniziare in una specifica data e ora, quindi esecuzione e ripetizione ogni *n* numero di secondi, minuti o ore. Con questo trigger, è possibile specificare un'ora di inizio nel passato, che esegue tutti passati ricorrenze.

* Ritardare ogni ricorrenza per una durata specifica prima dell'esecuzione.

Le differenze tra questo trigger e i trigger di ricorrenza o per ulteriori informazioni sulla pianificazione dei flussi di lavoro ricorrente, vedere [pianificazione e ricorrente esecuzione automatizzate le attività, processi e flussi di lavoro con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se si desidera attivare l'app per la logica ed eseguire solo una volta in futuro, vedere [esecuzione dei processi di una sola volta](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi a un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base [App per la logica](../logic-apps/logic-apps-overview.md). Se si ha familiarità con App per la logica, vedere l'articolo [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Aggiungere trigger di finestra temporale scorrevole

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Dopo che viene visualizzato Progettazione App per la logica, nella casella di ricerca immettere "finestra scorrevole" come filtro. Nell'elenco di trigger, selezionare questo trigger come primo passaggio nel flusso di lavoro di app per la logica: **Finestra temporale scorrevole**

   ![Selezionare il trigger "Finestra scorrevole"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana.

   ![Impostare intervallo e frequenza](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Proprietà | Obbligatorio | Nome JSON | Type | Descrizione |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Yes | interval | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p>- Hour: 1-12.000 ore </br>- Minute: 1-72.000 minuti </br>- Second: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Hour", la ricorrenza è ogni 6 ore. |
   | **Frequenza** | Yes | frequency | String | Unità di tempo per la ricorrenza: **Seconda**, **minuto**, o **ora** |
   ||||||

   ![Opzioni di ricorrenza avanzate](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Per altre opzioni di ricorrenza, aprire il **Aggiungi nuovo parametro** elenco. 
   Le opzioni selezionate vengono visualizzate sul trigger dopo la selezione.

   | Proprietà | Obbligatorio | Nome JSON | Type | Descrizione |
   |----------|----------|-----------|------|-------------|
   | **Ritardo** | No | delay | String | La durata di ritardo ogni ricorrenza usando il [specifica ora data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso orario** | No | timeZone | String | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | No | startTime | String | Specificare una data di inizio e un'ora nel formato seguente: <p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Ad esempio, se si desidera 18 settembre 2017 alle 14:00, specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le ricorrenze avanzate, il trigger non viene attivato prima alla data di inizio. [*In quali modi posso usare la data e l'ora di inizio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. A questo punto compilare il flusso di lavoro rimanente con altre azioni. Per altre azioni che è possibile aggiungere, vedere [connettori per App per la logica di Azure](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definizione del flusso di lavoro - finestra temporale scorrevole

Nella definizione del flusso di lavoro sottostante dell'app per la logica, che usa JSON, è possibile visualizzare la definizione di trigger di finestra temporale scorrevole con le opzioni che si è scelto. Per visualizzare questa definizione, sulla barra degli strumenti della finestra di progettazione, scegliere **visualizzazione codice**. Per tornare alla finestra di progettazione, scegliere barra degli strumenti della finestra di progettazione **progettazione**.

Questo esempio mostra come potrebbe apparire una definizione di trigger di finestra temporale scorrevole in una definizione del flusso di lavoro sottostante in cui il ritardo per ogni ricorrenza è cinque secondi per una ricorrenza ogni ora:

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