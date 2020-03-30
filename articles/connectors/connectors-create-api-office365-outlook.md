---
title: Connettersi a Office 365 Outlook
description: Automatizzare attività e flussi di lavoro che gestiscono posta elettronica, contatti e calendari in Office 365 Outlook usando le app per la logica di AzureAutomate tasks and workflows that manage email, contacts, and calendars in Office 365 Outlook by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732695"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gestire posta elettronica, contatti e calendari con Outlook di Office 365 usando App per la logica di Azure

Con [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il [connettore di Office 365 Outlook,](/connectors/office365connector/)è possibile creare attività e flussi di lavoro automatizzati che gestiscono l'account di Office 365 creando app per la logica. Ad esempio, si automatizzano queste attività:For example, you automate these tasks:

* Ricevere, inviare e rispondere a un messaggio di posta elettronica. 
* Pianificare riunioni nel calendario.
* Aggiungere e modificare contatti. 

È possibile utilizzare qualsiasi trigger per avviare il flusso di lavoro, ad esempio quando arriva un nuovo messaggio di posta elettronica, quando un elemento del calendario viene aggiornato o quando si verifica un evento in un servizio di differenza, ad esempio Salesforce. È possibile utilizzare azioni che rispondono all'evento trigger, ad esempio inviare un messaggio di posta elettronica o creare un nuovo evento del calendario. 

> [!NOTE]
> Per automatizzare @outlook.com le @hotmail.com attività per un account o , utilizzare il [connettore Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Prerequisiti

* Un account di [Office 365](https://www.office.com/)

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* App per la logica in cui si vuole accedere all'account di Office 365 Outlook. Per avviare il flusso di lavoro con un trigger di Office 365 Outlook, è necessario disporre di [un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per aggiungere un'azione di Office 365 Outlook al flusso di lavoro, l'app per la logica deve avere già un trigger.

## <a name="add-a-trigger"></a>Aggiungere un trigger

Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un evento che avvia il flusso di lavoro nell'app per la logica. Questa app per la logica di esempio usa un trigger "polling" che controlla la presenza di qualsiasi evento di calendario aggiornato nell'account di posta elettronica, in base all'intervallo e alla frequenza specificati.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella casella di ricerca immettere `office 365 outlook` come filtro. In questo esempio viene selezionato **Quando inizia un evento imminente**a breve .
   
   ![Selezionare il trigger per avviare l'app per la logica](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se viene richiesto di accedere, fornire le credenziali di Office 365 in modo che l'app per la logica possa connettersi all'account. In caso contrario, se la connessione esiste già, fornire le informazioni per le proprietà del trigger.

   In questo esempio viene selezionato il calendario controllato dal trigger, ad esempio:

   ![Configurare le proprietà del trigger](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Nel trigger impostare i valori **Frequenza** e **Intervallo.** Per aggiungere altre proprietà trigger disponibili, ad esempio **Fuso orario**, selezionare tali proprietà dall'elenco **Aggiungi nuovo parametro** .

   Ad esempio, se si desidera che il trigger controlli il calendario ogni 15 minuti, **impostare Frequenza** su **Minuti**e **impostare Intervallo** su `15`. 

   ![Impostare la frequenza e l'intervallo per il trigger](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Aggiungere ora un'azione che viene eseguita dopo l'attivazione del trigger. Ad esempio, è possibile aggiungere l'azione Invia **messaggio** Twilio, che invia un testo quando un evento del calendario inizia tra 15 minuti.

## <a name="add-an-action"></a>Aggiungere un'azione

[Un'azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un'operazione eseguita dal flusso di lavoro nell'app per la logica. Questa app per la logica di esempio crea un nuovo contatto in Office 365 Outlook. È possibile utilizzare l'output di un altro trigger o azione per creare il contatto. Si supponga, ad esempio, che l'app per la logica utilizzi il trigger Dynamics 365, **Quando viene creato un record.** È possibile aggiungere l'azione **Crea contatto** di Office 365 Outlook e utilizzare gli output del trigger SalesForce per creare il nuovo contatto.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Per aggiungere un'azione come ultimo passaggio del flusso di lavoro, selezionare **Nuovo passaggio**. 

   Per aggiungere un'azione tra i passaggi, spostare il puntatore sulla freccia tra questi passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere `office 365 outlook` come filtro. In questo esempio viene selezionato **Crea contatto**.

   ![Selezionare l'azione da eseguire nell'app per la logica](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se viene richiesto di accedere, fornire le credenziali di Office 365 in modo che l'app per la logica possa connettersi all'account. In caso contrario, se la connessione esiste già, fornire le informazioni per le proprietà dell'azione.

   In questo esempio viene selezionata la cartella contatti in cui l'azione crea il nuovo contatto, ad esempio:

   ![Configurare le proprietà dell'azione](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Per aggiungere altre proprietà di azione disponibili, selezionare tali proprietà dall'elenco **Aggiungi nuovo parametro.**

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti descritti nel file Swagger del connettore, vedere la [pagina di riferimento del connettore.](/connectors/office365connector/) 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
