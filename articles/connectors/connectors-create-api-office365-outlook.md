---
title: Eseguire l'integrazione con Office 365 Outlook
description: Automatizzare le attività e i flussi di lavoro che gestiscono la posta elettronica, i contatti e i calendari in Office 365 Outlook usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 9caf69a7f78c7872f0a5f8a2ed07bdc749a29023
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682996"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gestire posta elettronica, contatti e calendari con Outlook di Office 365 usando App per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il [connettore Outlook per Office 365](/connectors/office365connector/)è possibile creare attività e flussi di lavoro automatizzati che gestiscono l'account aziendale o dell'Istituto di istruzione creando app per la logica. È ad esempio possibile automatizzare queste attività:

* Ottenere, inviare e rispondere alla posta elettronica.
* Pianifica riunioni nel calendario.
* Aggiungere e modificare i contatti.

È possibile usare qualsiasi trigger per avviare il flusso di lavoro, ad esempio quando arriva un nuovo messaggio di posta elettronica, quando viene aggiornato un elemento del calendario o quando si verifica un evento in un servizio di differenza, ad esempio Salesforce. È possibile usare azioni che rispondono all'evento trigger, ad esempio, inviare un messaggio di posta elettronica o creare un nuovo evento del calendario.

## <a name="prerequisites"></a>Prerequisiti

* Un account di Outlook a cui si accede con un [account aziendale o dell'Istituto di istruzione](https://www.office.com/). Se si dispone di @outlook.com un @hotmail.com account o, usare invece il [connettore Outlook.com](../connectors/connectors-create-api-outlook.md) . Per connettersi a Outlook con un account utente diverso, ad esempio un account del servizio, vedere [connettersi con altri account](#connect-using-other-accounts).

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* App per la logica in cui si vuole accedere all'account di Outlook. Per avviare il flusso di lavoro con un trigger Office 365 Outlook, è necessario disporre di un'app per la [logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per aggiungere un'azione di Office 365 Outlook al flusso di lavoro, l'app per la logica deve avere già un trigger.

## <a name="add-a-trigger"></a>Aggiungere un trigger

Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un evento che avvia il flusso di lavoro nell'app per la logica. Questo esempio di app per la logica usa un trigger di polling che verifica la presenza di eventuali eventi del calendario aggiornati nell'account di posta elettronica, in base all'intervallo e alla frequenza specificati.

1. Nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica vuota nella finestra di progettazione dell'app per la logica.

1. Nella casella di ricerca immettere `office 365 outlook` come filtro. Questo esempio consente **di selezionare l'avvio imminente di un prossimo evento**.
   
   ![Selezionare trigger per avviare l'app per la logica](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se non si ha una connessione attiva con l'account Outlook, viene chiesto di eseguire l'accesso e creare tale connessione. Per connettersi a Outlook con un account utente diverso, ad esempio un account del servizio, vedere [connettersi con altri account](#connect-using-other-accounts). In caso contrario, fornire le informazioni per le proprietà del trigger.

   > [!NOTE]
   > La connessione non scade fino alla revoca, anche se si modificano le credenziali di accesso. Per altre informazioni, vedere [Durata dei token configurabili in Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Questo esempio Mostra come selezionare il calendario controllato dal trigger, ad esempio:

   ![Configurare le proprietà del trigger](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Nel trigger impostare i valori di **frequenza** e **intervallo** . Per aggiungere altre proprietà del trigger disponibili, ad esempio il **fuso orario**, selezionare le proprietà dall'elenco **Aggiungi nuovo parametro** .

   Ad esempio, se si desidera che il trigger verifichi il calendario ogni 15 minuti, impostare **frequenza** su **minuto** e impostare **intervallo** su `15` . 

   ![Impostare la frequenza e l'intervallo per il trigger](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Aggiungere ora un'azione che viene eseguita dopo il trigger attivato. Ad esempio, è possibile aggiungere l'azione di **invio del messaggio** Twilio, che invia un testo quando un evento del calendario inizia in 15 minuti.

## <a name="add-an-action"></a>Aggiungere un'azione

Un' [azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un'operazione eseguita dal flusso di lavoro nell'app per la logica. Questo esempio di app per la logica crea un nuovo contatto in Office 365 Outlook. È possibile utilizzare l'output di un altro trigger o azione per creare il contatto. Si supponga, ad esempio, che l'app per la logica usi il trigger Dynamics 365 **quando viene creato un record**. È possibile aggiungere l'azione Office 365 Outlook **Crea contatto** e usare gli output del trigger Salesforce per creare il nuovo contatto.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Per aggiungere un'azione come ultimo passaggio del flusso di lavoro, selezionare **nuovo passaggio**. 

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. Nella casella di ricerca immettere `office 365 outlook` come filtro. Questo esempio Mostra come selezionare **Crea contatto**.

   ![Selezionare l'azione da eseguire nell'app per la logica](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se non si ha una connessione attiva con l'account Outlook, viene chiesto di eseguire l'accesso e creare tale connessione. Per connettersi a Outlook con un account utente diverso, ad esempio un account del servizio, vedere [connettersi con altri account](#connect-using-other-accounts). In caso contrario, fornire le informazioni per le proprietà dell'azione.

   > [!NOTE]
   > La connessione non scade fino alla revoca, anche se si modificano le credenziali di accesso. Per altre informazioni, vedere [Durata dei token configurabili in Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Questo esempio Mostra come selezionare la cartella contatti in cui l'azione crea il nuovo contatto, ad esempio:

   ![Configurare le proprietà dell'azione](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Per aggiungere altre proprietà dell'azione disponibili, selezionare le proprietà dall'elenco **Aggiungi nuovo parametro** .

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Connetti con altri account

Se si prova a connettersi a Outlook usando un account diverso da quello attualmente connesso ad Azure, è possibile che vengano visualizzati errori di [Single Sign-on (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) . Questo problema si verifica quando si accede al portale di Azure con un account, ma si usa un account diverso per creare la connessione. La finestra di progettazione dell'app per la logica prevede di usare l'account che ha eseguito l'accesso ad Azure. Per risolvere il problema, sono disponibili le opzioni seguenti:

* Configurare l'altro account come **collaboratore** per il gruppo di risorse dell'app per la logica.

  1. Nel menu del gruppo di risorse dell'app per la logica selezionare **controllo di accesso (IAM)**. Configurare l'altro account con il ruolo **collaboratore** . Per altre informazioni, vedere [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md).

  1. Se è stato effettuato l'accesso al portale di Azure con l'account aziendale o dell'Istituto di istruzione, disconnettersi ed eseguire di nuovo l'accesso con il proprio account. È ora possibile creare una connessione a Outlook usando l'altro account.

* Configurare l'altro account in modo che l'account aziendale o dell'Istituto di istruzione disponga delle autorizzazioni "Invia come".

   Se si dispone delle autorizzazioni di amministratore, nella cassetta postale dell'account del servizio configurare l'account aziendale o dell'Istituto di istruzione con l'opzione **Invia come** o **Invia per conto delle** autorizzazioni. Per ulteriori informazioni, vedere [concedere le autorizzazioni per le cassette postali alla guida dell'amministratore di un altro utente](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). È quindi possibile creare la connessione con l'account aziendale o dell'Istituto di istruzione. A questo punto, in trigger o azioni in cui è possibile specificare il mittente, è possibile usare l'indirizzo di posta elettronica dell'account del servizio.

   Ad esempio, l'azione **Invia un messaggio di posta elettronica** ha un parametro facoltativo, **da (Send As)**, che è possibile aggiungere all'azione e usare l'indirizzo di posta elettronica dell'account del servizio come mittente. Per aggiungere questo parametro, attenersi alla procedura seguente:

   1. Nell'azione **Invia un messaggio di posta elettronica** aprire l'elenco **Aggiungi un parametro** e selezionare il parametro **from (Invia come)** .

   1. Dopo aver visualizzato il parametro sull'azione, immettere l'indirizzo di posta elettronica dell'account del servizio.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file spavalderia del connettore, vedere la [pagina di riferimento](/connectors/office365/)del connettore. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)