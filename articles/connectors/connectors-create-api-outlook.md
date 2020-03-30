---
title: Connessione ad Outlook.com
description: Automatizzare attività e flussi di lavoro che gestiscono posta elettronica, calendari e contatti in Outlook.com tramite app per la logica di AzureAutomate tasks and workflows that manage email, calendars, and contacts in Outlook.com by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707187"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Gestire posta elettronica, calendari e contatti in Outlook.com tramite app per la logica di AzureManage email, calendars, and contacts in Outlook.com by using Azure Logic Apps

Con [app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il [connettore Outlook.com,](/connectors/outlook/)è possibile creare attività e flussi di lavoro automatizzati che gestiscono il proprio @outlook.com account o @hotmail.com l'account mediante la creazione di app per la logica. Ad esempio, si automatizzano queste attività:For example, you automate these tasks:

* Ricevere, inviare e rispondere a un messaggio di posta elettronica.
* Pianificare riunioni nel calendario.
* Aggiungere e modificare contatti.

È possibile utilizzare qualsiasi trigger per avviare il flusso di lavoro, ad esempio quando arriva un nuovo messaggio di posta elettronica, quando un elemento del calendario viene aggiornato o quando si verifica un evento in un servizio di differenza. È possibile utilizzare azioni che rispondono all'evento trigger, ad esempio inviare un messaggio di posta elettronica o creare un nuovo evento del calendario.

> [!NOTE]
> Per automatizzare le attività per @fabrikam.onmicrosoft.comun account di lavoro Microsoft, ad esempio , utilizzare il [connettore di Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [account Outlook.com](https://outlook.live.com/owa/)

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* L'app per la logica in cui si vuole accedere all'account Outlook.com. Per avviare il flusso di lavoro con un trigger di Outlook.com, è necessario disporre di [un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per aggiungere un'azione Outlook.com al flusso di lavoro, l'app per la logica deve avere già un trigger.

## <a name="add-a-trigger"></a>Aggiungere un trigger

Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un evento che avvia il flusso di lavoro nell'app per la logica. Questa app per la logica di esempio usa un trigger "polling" che controlla la presenza di qualsiasi nuovo messaggio di posta elettronica nell'account di posta elettronica, in base all'intervallo e alla frequenza specificati.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella casella di ricerca immettere "outlook.com" come filtro. Per questo esempio, selezionare **All'arrivo di un nuovo messaggio**di posta elettronica.

1. Se viene richiesto di accedere, fornire le credenziali Outlook.com in modo che l'app per la logica possa connettersi all'account. In caso contrario, se la connessione esiste già, fornire le informazioni per le proprietà del trigger:Otherwise, if your connection already exists, provide the information for the trigger properties:

1. Nel trigger impostare i valori **Frequenza** e **Intervallo.**

   Ad esempio, se si desidera che il trigger eseda ogni 15 minuti, **impostare Frequenza** su **Minuti**e **impostare Intervallo** su **15**.

1. Nella barra degli strumenti della finestra di progettazione selezionare **Salva**, che consente di salvare l'app per la logica.

Per rispondere al trigger, aggiungere un'altra azione. Ad esempio, è possibile aggiungere l'azione Invia **messaggio** Twilio, che invia un testo all'arrivo di un messaggio di posta elettronica.

## <a name="add-an-action"></a>Aggiungere un'azione

[Un'azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un'operazione eseguita dal flusso di lavoro nell'app per la logica. Questa app per la logica di esempio invia un messaggio di posta elettronica dall'account Outlook.com. È possibile usare l'output di un altro trigger per popolare l'azione. Si supponga, ad esempio, che l'app per la logica usi il trigger SalesForce **When an object is created.** È possibile aggiungere l'azione Outlook.com **Invia un messaggio di posta elettronica** e utilizzare gli output del trigger SalesForce nel messaggio di posta elettronica.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Per aggiungere un'azione come ultimo passaggio del flusso di lavoro, selezionare **Nuovo passaggio**. 

   Per aggiungere un'azione tra i passaggi, spostare il puntatore sulla freccia tra questi passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca, inserisci "outlook.com" come filtro. Per questo esempio, selezionare **Invia un messaggio di posta elettronica**. 

1. Se viene richiesto di accedere, fornire le credenziali Outlook.com in modo che l'app per la logica possa connettersi all'account. In caso contrario, se la connessione esiste già, fornire le informazioni per le proprietà dell'azione.

1. Nella barra degli strumenti della finestra di progettazione selezionare **Salva**, che consente di salvare l'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/outlook/). 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
