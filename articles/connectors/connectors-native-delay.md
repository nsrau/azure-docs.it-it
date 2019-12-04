---
title: Ritardare l'azione successiva nei flussi di lavoro
description: Attendere per eseguire l'azione successiva nei flussi di lavoro delle app per la logica usando il ritardo o il ritardo fino alle azioni in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787337"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Ritardare l'esecuzione dell'azione successiva in app per la logica di Azure

Per fare in modo che l'app per la logica attenda una certa quantità di tempo prima di eseguire l'azione successiva, è possibile aggiungere l'azione predefinita di **pianificazione ritardata** prima di un'azione nel flusso di lavoro dell'app per la logica. In alternativa, è possibile aggiungere il ritardo predefinito **fino a quando** non si prevede di attendere una data e un'ora specifiche prima di eseguire l'azione successiva. Per altre informazioni sulle azioni e sui trigger di pianificazione predefiniti, vedere [pianificare ed eseguire attività ricorrenti automatizzate, attività e flussi di lavoro con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Ritardo**: attendere il numero specificato di unità di tempo, ad esempio secondi, minuti, ore, giorni, settimane o mesi, prima dell'esecuzione dell'azione successiva.

* **Ritardo fino a**: attendere fino alla data e all'ora specificate prima dell'esecuzione dell'azione successiva.

Di seguito sono riportati alcuni esempi di modalità di utilizzo di queste azioni:

* Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.

* Ritardare il flusso di lavoro fino al termine di una chiamata HTTP prima di riprendere e recuperare i dati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* Informazioni di base sulle app per la [logica](../logic-apps/logic-apps-overview.md). Prima di poter usare un'azione, l'app per la logica deve iniziare con un trigger. È possibile usare qualsiasi trigger desiderato e aggiungere altre azioni prima di aggiungere un'azione di ritardo. Questo argomento usa un trigger Office 365 Outlook. Se non si ha familiarità con le app per la logica, scoprire [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Aggiungere l'azione Delay

1. Nella finestra di progettazione dell'app per la logica, sotto il passaggio in cui si vuole aggiungere l'azione ritardo, scegliere **nuovo passaggio**.

   Per aggiungere l'azione delay tra i passaggi, spostare il puntatore sulla freccia che connette la procedura. Scegliere il segno più (+) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "ritardo" come filtro. Nell'elenco azioni selezionare questa azione: **ritardo**

   ![Aggiungi azione "ritardo"](./media/connectors-native-delay/add-delay-action.png)

1. Specificare la quantità di tempo di attesa prima dell'esecuzione dell'azione successiva.

   ![Imposta la quantità di tempo per il ritardo](./media/connectors-native-delay/delay-time-intervals.png)

   | Proprietà | Nome JSON | Obbligatoria | Type | Description |
   |----------|-----------|----------|------|-------------|
   | Conteggio | count | SÌ | Integer | Il numero di unità di tempo di ritardo |
   | Unità | unit | SÌ | Stringa | Unità di tempo, ad esempio: `Second`, `Minute`, `Hour`, `Day`, `Week`o `Month` |
   ||||||

1. Aggiungere eventuali altre azioni che si desidera eseguire nel flusso di lavoro.

1. Al termine, salvare l'app per la logica.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Aggiungere l'azione ritardo-fino a

1. Nella finestra di progettazione dell'app per la logica, sotto il passaggio in cui si vuole aggiungere l'azione ritardo, scegliere **nuovo passaggio**.

   Per aggiungere l'azione delay tra i passaggi, spostare il puntatore sulla freccia che connette la procedura. Scegliere il segno più (+) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "ritardo" come filtro. Nell'elenco azioni selezionare questa azione: **ritardo fino a**

   ![Aggiungere un'azione "Ritarda fino a"](./media/connectors-native-delay/add-delay-until-action.png)

1. Specificare la data e l'ora di fine del momento in cui si desidera riprendere il flusso di lavoro.

   ![Specificare il timestamp per la fine del ritardo](./media/connectors-native-delay/delay-until-timestamp.png)

   | Proprietà | Nome JSON | Obbligatoria | Type | Description |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | SÌ | Stringa | Data e ora di fine per la ripresa del flusso di lavoro utilizzando il formato seguente: <p>AAAA-MM-GGThh: mm: ssZ <p>Se, ad esempio, si desidera che il 18 settembre 2017 alle 2:00 PM, specificare "2017-09-18T14:00:00Z". <p>**Nota:** Questo formato di ora deve seguire la specifica di data e ora [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [offset UTC](https://en.wikipedia.org/wiki/UTC_offset). Senza un fuso orario, è necessario aggiungere la lettera "Z" alla fine senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. |
   ||||||

1. Aggiungere eventuali altre azioni che si desidera eseguire nel flusso di lavoro.

1. Al termine, salvare l'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md)
* [Connettori per app per la logica](../connectors/apis-list.md)