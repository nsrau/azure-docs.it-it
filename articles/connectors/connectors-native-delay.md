---
title: Ritardare l'azione successiva nei flussi di lavoro - App per la logica di Azure
description: Attendere per eseguire l'azione successiva nei flussi di lavoro app per la logica usando le azioni di ritardo o ritardo fino a quando nell'App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297653"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Ritardo esecuzione dell'azione successiva nell'App per la logica di Azure

Per includere l'app per la logica di attendere un periodo di tempo prima di eseguire l'azione successiva, è possibile aggiungere l'elemento predefinito **ritardo - pianificare** azione prima di un'azione nel flusso di lavoro dell'app per la logica. In alternativa, è possibile aggiungere predefiniti **Ritarda fino a - pianificare** azione attendere fino a una data e ora specifiche prima di eseguire l'azione successiva. Per altre informazioni sulle azioni di pianificazione predefinite e trigger, vedere [pianificazione e ricorrente automatizzato di esecuzione, attività e flussi di lavoro con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **ritardo**: Attendere che il numero specificato di unità di tempo, ad esempio secondi, minuti, ore, giorni, settimane o mesi, prima di eseguire l'azione successiva.

* **Ritarda fino a**: Attendere la data e ora specificate prima di eseguire l'azione successiva.

Ecco alcuni metodi di esempio per usare queste azioni:

* Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.

* Ritardare il flusso di lavoro finché non viene completata una chiamata HTTP prima di riprendere e il recupero dei dati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi a un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base [App per la logica](../logic-apps/logic-apps-overview.md). Prima che è possibile usare un'azione, l'app per la logica deve iniziare con un trigger. È possibile usare qualsiasi trigger desiderato, aggiungere altre azioni prima di aggiungere un'azione di ritardo. Questo argomento Usa un trigger di Office 365 Outlook. Se si ha familiarità con App per la logica, vedere l'articolo [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Aggiungere l'azione di ritardo

1. In Progettazione App per la logica, sotto il passaggio in cui si desidera aggiungere l'azione di ritardo, scegliere **nuovo passaggio**.

   Per aggiungere l'azione di ritardo tra i vari passaggi, spostare il puntatore del mouse sulla freccia che connette i passaggi. Scegliere il segno più (+) che viene visualizzato e quindi selezionare **aggiungere un'azione**.

1. Nella casella di ricerca immettere "ritardo" come filtro. Nell'elenco di azioni selezionare questa azione: **Ritardo**

   ![Aggiungere un'azione "Ritardo"](./media/connectors-native-delay/add-delay-action.png)

1. Specificare la quantità di tempo di attesa prima di eseguire l'azione successiva.

   ![Impostare un tempo di ritardo](./media/connectors-native-delay/delay-time-intervals.png)

   | Proprietà | Nome JSON | Obbligatorio | Type | Descrizione |
   |----------|-----------|----------|------|-------------|
   | Conteggio | count | Yes | Integer | Il numero di unità di tempo di ritardo |
   | Unità | unit | Yes | String | L'unità di tempo, ad esempio: `Second`, `Minute`, `Hour`, `Day`, `Week`, o `Month` |
   ||||||

1. Aggiungere tutte le altre azioni che si desidera eseguire il flusso di lavoro.

1. Al termine, salvare l'app per la logica.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Aggiungere il ritardo-azione until

1. In Progettazione App per la logica, sotto il passaggio in cui si desidera aggiungere l'azione di ritardo, scegliere **nuovo passaggio**.

   Per aggiungere l'azione di ritardo tra i vari passaggi, spostare il puntatore del mouse sulla freccia che connette i passaggi. Scegliere il segno più (+) che viene visualizzato e quindi selezionare **aggiungere un'azione**.

1. Nella casella di ricerca immettere "ritardo" come filtro. Nell'elenco di azioni selezionare questa azione: **Ritarda fino a**

   ![Aggiungere un'azione "Ritarda fino a"](./media/connectors-native-delay/add-delay-until-action.png)

1. Specificare la data di fine e l'ora per quando si desidera riprendere il flusso di lavoro.

   ![Specificare il timestamp per quando alla fine del ritardo](./media/connectors-native-delay/delay-until-timestamp.png)

   | Proprietà | Nome JSON | Obbligatorio | Type | Descrizione |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | Yes | String | La data di fine e l'ora per riprendere il flusso di lavoro usando questo formato: <p>YYYY-MM-DDThh:mm:ssZ <p>Ad esempio, se si desidera 18 settembre 2017 alle 14:00, specificare "2017-09-18T14:00:00Z". <p>**Nota:** Questo formato di ora deve seguire il [specifica ora data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [formato data UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza una [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Senza un fuso orario, è necessario aggiungere la lettera "Z" alla fine senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. |
   ||||||

1. Aggiungere tutte le altre azioni che si desidera eseguire il flusso di lavoro.

1. Al termine, salvare l'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Creare, pianificare ed eseguire le attività ricorrenti e flussi di lavoro con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md)
* [Connettori per App per la logica](../connectors/apis-list.md)