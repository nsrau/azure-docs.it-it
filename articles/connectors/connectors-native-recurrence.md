---
title: Pianificare attività e flussi di lavoro ricorrenti
description: Pianificare ed eseguire attività e flussi di lavoro automatizzati ricorrenti con il trigger di ricorrenza nelle app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0df34c0632875be3d2d3956ca90f615cb0a990ed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787200"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger di ricorrenza nelle app per la logica di Azure

Per eseguire regolarmente attività, processi o processi in base a una pianificazione specifica, è possibile avviare il flusso di lavoro dell'app per la logica con il trigger di **pianificazione della ricorrenza** predefinita. È possibile impostare una data e un'ora, nonché un fuso orario per l'avvio del flusso di lavoro e una ricorrenza per la ripetizione del flusso di lavoro. Se per qualsiasi motivo vengono perse le ricorrenze, questo trigger continua a essere ripetuto al successivo intervallo pianificato. Per altre informazioni sui trigger di pianificazione e sulle azioni predefinite, vedere [pianificare ed eseguire attività ricorrenti automatizzate, attività e flussi di lavoro con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ecco alcuni modelli supportati da questo trigger insieme a ricorrenze più avanzate e pianificazioni complesse:

* Esecuzione immediata e ripetizione ogni *n* secondi, minuti, ore, giorni, settimane o mesi.

* Iniziare da una data e un'ora specifiche, quindi eseguire e ripetere ogni *n* secondi, minuti, ore, giorni, settimane o mesi.

* Esecuzione e ripetizione a uno o più orari ogni giorno, ad esempio alle 8:00 e alle 17:00.

* Esecuzione e ripetizione ogni settimana, ma solo in giorni specifici, ad esempio sabato e domenica.

* Esecuzione e ripetizione ogni settimana, ma solo in ore e giorni specifici, ad esempio dal lunedì al venerdì alle 8:00 e alle 17:00.

Per le differenze tra questo trigger e il trigger della finestra temporale scorrevole o per altre informazioni sulla pianificazione di flussi di lavoro ricorrenti, vedere [pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se si vuole attivare l'app per la logica ed eseguire solo una volta in futuro, vedere [eseguire processi solo una volta](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* Informazioni di base sulle app per la [logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, scoprire [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Aggiungi trigger di ricorrenza

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Dopo aver visualizzato Progettazione app per la logica, nella casella di ricerca immettere "ricorrenza" come filtro. Dall'elenco trigger selezionare questo trigger come primo passaggio nel flusso di lavoro dell'app per la logica: **ricorrenza**

   ![Selezionare il trigger "ricorrenza"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana.

   ![Impostare intervallo e frequenza](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Proprietà | Obbligatoria | Nome JSON | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **Interval** | SÌ | interval | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p>- Mese: 1-16 mesi </br>- Giorno: 1-500 giorni </br>- Ora: 1-12.000 ore </br>- Minuto: 1-72.000 minuti </br>- Secondo: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Mese", la ricorrenza è ogni 6 mesi. |
   | **Frequenza** | SÌ | frequency | Stringa | L'unità di tempo per la ricorrenza: **Secondo**, **Minuto**, **Ora**, **Giorno**, **Settimana** o **Mese** |
   ||||||

   Per altre opzioni di pianificazione, aprire l'elenco **Aggiungi nuovo parametro** . 
   Tutte le opzioni selezionate vengono visualizzate nel trigger dopo la selezione.

   ![Opzioni di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Proprietà | Obbligatoria | Nome JSON | Type | Description |
   |----------|----------|-----------|------|-------------|
   | **Fuso orario** | No | timeZone | Stringa | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | No | startTime | Stringa | Specificare una data e un'ora di inizio nel formato seguente: <p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Quindi, ad esempio, se si desidera il 18 settembre 2017 alle 2:00 PM, specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** Questa ora di inizio ha un numero massimo di 49 anni nel futuro e deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [offset UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non viene attivato prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **In questi giorni** | No | weekDays | Stringa o matrice di stringhe | Se si seleziona "Settimana", è possibile selezionare uno o più giorni in cui eseguire il flusso di lavoro: **Lunedì**, **Martedì**, **Mercoledì**, **Giovedì**, **Venerdì**, **Sabato** e **Domenica** |
   | **A queste ore** | No | ore | Numero intero o matrice di numeri interi | Se si seleziona "giorno" o "settimana", è possibile selezionare uno o più numeri interi da 0 a 23 come ore del giorno in cui si desidera eseguire il flusso di lavoro. <p><p>Se ad esempio si specifica "10", "12" e "14", si ottengono le 10:00, le 12.00 e le 14.00 per le ore del giorno, ma i minuti del giorno vengono calcolati in base all'inizio della ricorrenza. Per impostare i minuti del giorno, specificare il valore per la proprietà **in corrispondenza di questi minuti** . |
   | **A questi minuti** | No | minuti | Numero intero o matrice di numeri interi | Se si seleziona "Giorno" o "Settimana", è possibile selezionare uno o più numeri interi da 0 a 59 come minuti dell'ora in cui si desidera eseguire il flusso di lavoro. <p>Ad esempio, è possibile specificare "30" come indicatore dei minuti e, usando l'esempio precedente per le ore del giorno, si otterranno le ore 10.30, 12.30 e 14.30. |
   |||||

   Ad esempio, si supponga che sia lunedì 4 settembre 2017. Il trigger di ricorrenza seguente non viene attivato *prima* della data e dell'ora di inizio, ovvero lunedì 18 settembre 2017 alle 8:00 PST. Tuttavia, la pianificazione di ricorrenza è impostata solo per i lunedì alle 10:30, 12:30 e 14:30. Il trigger viene pertanto attivato e crea un'istanza del flusso di lavoro dell'app per la logica per la prima volta alle 10:30. Per altre informazioni sul funzionamento degli orari di inizio, vedere questi [esempi](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Le esecuzioni future avranno luogo alle 12:30 e 14:30 nella stessa giornata. Ogni ricorrenza crea la propria istanza del flusso di lavoro. L'intera pianificazione verrà quindi ripetuta da capo il lunedì successivo. [*Quali sono altre occorrenze di esempio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Esempio di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Il trigger mostra un'anteprima della ricorrenza specificata solo quando si seleziona "Giorno" o "Settimana" come frequenza.

1. A questo punto, compilare il flusso di lavoro rimanente con altre azioni. Per altre azioni che è possibile aggiungere, vedere [connettori per app per la logica di Azure](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definizione del flusso di lavoro-ricorrenza

Nella definizione del flusso di lavoro sottostante dell'app per la logica, che usa JSON, è possibile visualizzare la [definizione del trigger di ricorrenza](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) con le opzioni selezionate. Per visualizzare questa definizione, scegliere **visualizzazione codice**sulla barra degli strumenti della finestra di progettazione. Per tornare alla finestra di progettazione, scegliere sulla barra degli strumenti della finestra di progettazione, **finestra**di progettazione.

Questo esempio illustra come una definizione di trigger di ricorrenza può apparire in una definizione del flusso di lavoro sottostante:

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

* [Sospendere i flussi di lavoro con azioni di ritardo](../connectors/connectors-native-delay.md)
* [Connettori per app per la logica](../connectors/apis-list.md)
