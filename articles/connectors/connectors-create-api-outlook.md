---
title: Connessione ad Outlook.com
description: Automatizzare le attività e i flussi di lavoro che gestiscono la posta elettronica, i calendari e i contatti in Outlook.com usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75707187"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Gestire la posta elettronica, i calendari e i contatti in Outlook.com usando app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il [connettore Outlook.com](/connectors/outlook/)è possibile creare attività e flussi di lavoro automatizzati che gestiscono l'account o compilando @outlook.com @hotmail.com app per la logica. Ad esempio, è possibile automatizzare queste attività:

* Ottenere, inviare e rispondere alla posta elettronica.
* Pianifica riunioni nel calendario.
* Aggiungere e modificare i contatti.

È possibile usare qualsiasi trigger per avviare il flusso di lavoro, ad esempio quando arriva un nuovo messaggio di posta elettronica, quando viene aggiornato un elemento del calendario o quando si verifica un evento in un servizio di differenza. È possibile usare azioni che rispondono all'evento trigger, ad esempio, inviare un messaggio di posta elettronica o creare un nuovo evento del calendario.

> [!NOTE]
> Per automatizzare le attività per un account di lavoro Microsoft @fabrikam.onmicrosoft.com , ad esempio, usare il [connettore Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [account Outlook.com](https://outlook.live.com/owa/)

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* L'app per la logica in cui si vuole accedere all'account Outlook.com. Per avviare il flusso di lavoro con un trigger Outlook.com, è necessario disporre di un'app per la [logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per aggiungere un'azione Outlook.com al flusso di lavoro, è necessario che l'app per la logica disponga già di un trigger.

## <a name="add-a-trigger"></a>Aggiungere un trigger

Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un evento che avvia il flusso di lavoro nell'app per la logica. Questo esempio di app per la logica usa un trigger di polling che verifica la presenza di nuovi messaggi di posta elettronica nell'account di posta elettronica, in base all'intervallo e alla frequenza specificati.

1. Nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica vuota nella finestra di progettazione dell'app per la logica.

1. Nella casella di ricerca immettere "outlook.com" come filtro. Per questo esempio, selezionare **quando arriva un nuovo messaggio di posta elettronica**.

1. Se viene richiesto di eseguire l'accesso, fornire le credenziali di Outlook.com in modo che l'app per la logica possa connettersi all'account. In caso contrario, se la connessione esiste già, fornire le informazioni per le proprietà del trigger:

1. Nel trigger impostare i valori di **frequenza** e **intervallo** .

   Ad esempio, se si desidera che il trigger esegua il polling ogni 15 minuti, impostare la **frequenza** su **minuti**e impostare l' **intervallo** su **15**.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Save (Salva**) per salvare l'app per la logica.

Per rispondere al trigger, aggiungere un'altra azione. Ad esempio, è possibile aggiungere l'azione di **invio del messaggio** Twilio, che invia un testo all'arrivo di un messaggio di posta elettronica.

## <a name="add-an-action"></a>Aggiungere un'azione

Un' [azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un'operazione eseguita dal flusso di lavoro nell'app per la logica. Questo esempio di app per la logica Invia un messaggio di posta elettronica dall'account Outlook.com. È possibile utilizzare l'output di un altro trigger per popolare l'azione. Si supponga, ad esempio, che l'app per la logica usi SalesForce **quando viene creato un oggetto** trigger. È possibile aggiungere l'azione **Invia un messaggio di posta elettronica** di Outlook.com e usare gli output del trigger Salesforce nel messaggio di posta elettronica.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Per aggiungere un'azione come ultimo passaggio del flusso di lavoro, selezionare **nuovo passaggio**. 

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "outlook.com" come filtro. Per questo esempio, selezionare **Invia un messaggio di posta elettronica**. 

1. Se viene richiesto di eseguire l'accesso, fornire le credenziali di Outlook.com in modo che l'app per la logica possa connettersi all'account. In caso contrario, se la connessione esiste già, fornire le informazioni per le proprietà dell'azione.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Save (Salva**) per salvare l'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/outlook/). 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
