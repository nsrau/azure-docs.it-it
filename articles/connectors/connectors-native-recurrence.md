---
title: Pianificare attività ricorrenti con trigger di ricorrenza - App per la logica di Azure
description: Pianificare ed eseguire le attività automatizzate ricorrente e flussi di lavoro con il trigger di ricorrenza nelle App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297566"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Creare, pianificare ed eseguire le attività ricorrenti e flussi di lavoro con il trigger di ricorrenza nelle App per la logica di Azure

Per eseguire regolarmente le attività, processi o i processi nella pianificazione specifica, è possibile avviare il flusso di lavoro di app per la logica con l'oggetto incorporato **ricorrenza - pianificazione** trigger. È possibile impostare una data e ora, nonché un fuso orario per l'avvio del flusso di lavoro e una ricorrenza per la ripetizione del flusso di lavoro. Se le ricorrenze non sono installate per qualsiasi motivo, il trigger continua ricorrente al successivo intervallo pianificato. Per altre informazioni sulle azioni e trigger di pianificazione predefinite, vedere [pianificazione e ricorrente automatizzato di esecuzione, attività e flussi di lavoro con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ecco alcuni modelli che questo trigger supporta oltre a pianificazioni complesse e ricorrenze più avanzate:

* Esecuzione immediata e ripetizione ogni *n* numero di secondi, minuti, ore, giorni, settimane o mesi.

* Iniziare in una specifica data e ora, quindi esecuzione e ripetizione ogni *n* numero di secondi, minuti, ore, giorni, settimane o mesi.

* Esecuzione e ripetizione a uno o più orari ogni giorno, ad esempio alle 8:00 e alle 17:00.

* Esecuzione e ripetizione ogni settimana, ma solo in giorni specifici, ad esempio sabato e domenica.

* Esecuzione e ripetizione ogni settimana, ma solo in ore e giorni specifici, ad esempio dal lunedì al venerdì alle 8:00 e alle 17:00.

Le differenze tra questo trigger e il trigger di finestra temporale scorrevole o per ulteriori informazioni sulla pianificazione dei flussi di lavoro ricorrente, vedere [pianificazione e ricorrente esecuzione automatizzate le attività, processi e flussi di lavoro con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se si desidera attivare l'app per la logica ed eseguire solo una volta in futuro, vedere [esecuzione dei processi di una sola volta](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi a un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base [App per la logica](../logic-apps/logic-apps-overview.md). Se si ha familiarità con App per la logica, vedere l'articolo [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Aggiungere trigger di ricorrenza

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Dopo che viene visualizzato Progettazione App per la logica, nella casella di ricerca immettere "ricorrenza" come filtro. Nell'elenco di trigger, selezionare questo trigger come primo passaggio nel flusso di lavoro di app per la logica: **Ricorrenza**

   ![Selezionare il trigger "Ricorrenza"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana.

   ![Impostare intervallo e frequenza](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Proprietà | Obbligatorio | Nome JSON | Type | Descrizione |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Yes | interval | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p>- Month: 1-16 mesi </br>- Day: 1-500 giorni </br>- Hour: 1-12.000 ore </br>- Minute: 1-72.000 minuti </br>- Second: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Mese", la ricorrenza è ogni 6 mesi. |
   | **Frequenza** | Yes | frequency | string | Unità di tempo per la ricorrenza: **Secondo**, **Minuto**, **Ora**, **Giorno**, **Settimana** o **Mese** |
   ||||||

   Per altre opzioni di pianificazione, aprire il **Aggiungi nuovo parametro** elenco. 
   Le opzioni selezionate vengono visualizzate sul trigger dopo la selezione.

   ![Opzioni di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Proprietà | Obbligatorio | Nome JSON | Type | Descrizione |
   |----------|----------|-----------|------|-------------|
   | **Fuso orario** | No  | timeZone | string | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | No  | startTime | string | Specificare una data di inizio e un'ora nel formato seguente: <p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Ad esempio, se si desidera 18 settembre 2017 alle 14:00, specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non si attiva prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **In questi giorni** | No  | weekDays | Stringa o matrice di stringhe | Se si seleziona "Settimana", è possibile selezionare uno o più giorni in cui eseguire il flusso di lavoro: **Lunedì**, **Martedì**, **Mercoledì**, **Giovedì**, **Venerdì**, **Sabato** e **Domenica** |
   | **A queste ore** | No | hours | Intero o matrice di intero | Se si seleziona "Giorno" o "Week", è possibile selezionare uno o più numeri interi da 0 a 23 come ore del giorno per quando si desidera eseguire il flusso di lavoro. <p><p>Ad esempio, se si specifica "10", "12" e "14", puoi ottenere 10 AM, PM di 12 e 2 ore per le ore del giorno, ma i minuti del giorno vengono calcolati in base all'avvio la ricorrenza. Per impostare i minuti del giorno, specificare il valore per il **a questi minuti** proprietà. |
   | **A questi minuti** | No  | minutes | Intero o matrice di intero | Se si seleziona "Giorno" o "Settimana", è possibile selezionare uno o più numeri interi da 0 a 59 come minuti dell'ora in cui si desidera eseguire il flusso di lavoro. <p>Ad esempio, è possibile specificare "30" come indicatore dei minuti e, usando l'esempio precedente per le ore del giorno, si otterranno le ore 10.30, 12.30 e 14.30. |
   |||||

   Ad esempio, si supponga che sia lunedì 4 settembre 2017. Non viene attivato il trigger di ricorrenza seguente *prima* alla data di inizio e all'ora, ovvero lunedì 18 settembre 2017 alle ore 8:00: 00 PST. Tuttavia, la pianificazione di ricorrenza è impostata solo per i lunedì alle 10:30, 12:30 e 14:30. Il trigger viene pertanto attivato e crea un'istanza del flusso di lavoro dell'app per la logica per la prima volta alle 10:30. Per altre informazioni sul funzionamento degli orari di inizio, vedere questi [esempi](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Le esecuzioni future avranno luogo alle 12:30 e 14:30 nella stessa giornata. Ogni ricorrenza crea la propria istanza del flusso di lavoro. L'intera pianificazione verrà quindi ripetuta da capo il lunedì successivo. [*Quali sono altre occorrenze di esempio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Esempio di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Il trigger mostra un'anteprima della ricorrenza specificata solo quando si seleziona "Giorno" o "Settimana" come frequenza.

1. A questo punto compilare il flusso di lavoro rimanente con altre azioni. Per altre azioni che è possibile aggiungere, vedere [connettori per App per la logica di Azure](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definizione del flusso di lavoro - ricorrenza

Nella definizione del flusso di lavoro sottostante dell'app per la logica, che usa JSON, è possibile visualizzare il [definizione di trigger di ricorrenza](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) con le opzioni che si è scelto. Per visualizzare questa definizione, sulla barra degli strumenti della finestra di progettazione, scegliere **visualizzazione codice**. Per tornare alla finestra di progettazione, scegliere barra degli strumenti della finestra di progettazione **progettazione**.

Questo esempio mostra come potrebbe apparire una definizione di trigger di ricorrenza in una definizione del flusso di lavoro sottostante:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Flussi di lavoro di pausa con azioni di ritardo](../connectors/connectors-native-delay.md)
* [Connettori per App per la logica](../connectors/apis-list.md)
