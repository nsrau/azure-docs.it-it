---
title: Automatizzare i processi di Azure Application Insights con Microsoft Flow
description: Informazioni su come usare Microsoft Flow per automatizzare in poco tempo i processi ripetibili usando il connettore di Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903545"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizzare i processi di Azure Application Insights con il connettore per Microsoft Flow

Ci si trova spesso a eseguire ripetutamente le stesse query sui dati di telemetria per verificare il corretto funzionamento del servizio? Si vuole automatizzare queste query per trovare tendenze e anomalie e creare quindi flussi di lavoro basati su queste informazioni? Il connettore di Azure Application Insights per Microsoft Flow è lo strumento ideale per questi scopi.

Con questa integrazione è ora possibile automatizzare numerosi processi senza dover scrivere codice. Dopo avere creato un flusso usando un'azione di Application Insights, il flusso esegue automaticamente la query di Application Insights - Analisi. 

È possibile aggiungere anche altre azioni. In Microsoft Flow sono disponibili centinaia di azioni. Ad esempio, è possibile usare Microsoft Flow per inviare automaticamente una notifica di posta elettronica o per creare un bug in Azure DevOps. È anche possibile usare uno dei numerosi [modelli](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibili per il connettore per Microsoft Flow. Questi modelli velocizzano il processo di creazione di un flusso. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Creare un flusso per Application Insights

Questa esercitazione illustra come creare un flusso che usa l'algoritmo di cluster automatico di Analisi per raggruppare gli attributi dei dati per un'applicazione Web. Il flusso invia automaticamente i risultati tramite posta elettronica. Questo è solo un esempio di uso congiunto di Microsoft Flow e Application Insights - Analisi. 

### <a name="step-1-create-a-flow"></a>Passaggio 1: Creare un flusso
1. Accedere a [Microsoft Flow](https://flow.microsoft.com) e quindi selezionare **Flussi personali**.
2. Fare clic su **New** quindi **Crea da zero**.

    ![Crea nuovo flusso da zero](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passaggio 2: Creare un trigger per il flusso
1. La compilazione nella scheda selezionare **pianificazione**, quindi selezionare **pianificazione - ricorrenza**.

    ![Selezionare pianificarla in compilazione](./media/automate-with-flow/2schedule.png)

1. Nel **intervallo** immettere **1**e il **frequenza** , quindi selezionare **giorno**.
2. Fare clic su **nuovo passaggio**

    ![Impostare la pianificazione ricorrenza con immettere frequenza e intervallo](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Passaggio 3: aggiungere un'azione di Application Insights
1. Cercare **Azure Application Insights**.
2. Fare clic su **Azure Application Insights - query Analitica visualizzare**.
 
    ![Scegliere un'azione: Query di Azure Application Insights Visualizza Analitica](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passaggio 4: Connettersi a una risorsa di Application Insights

Per completare questo passaggio, sono necessari un ID applicazione e una chiave API per la risorsa. È possibile recuperare queste informazioni dal portale di Azure, come illustrato nell'immagine seguente:

![ID applicazione nel portale di Azure](./media/automate-with-flow/5apiaccess.png)

![Chiave API nel portale di Azure](./media/automate-with-flow/6apikey.png)

- Specificare un nome per la connessione, con l'ID applicazione e la chiave API.

    ![Finestra di connessione di Microsoft Flow](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passaggio 5: Specificare la query e il tipo di grafico di Analytics
Questa query di esempio seleziona le richieste non riuscite entro l'ultimo giorno e le correla alle eccezioni che si sono verificate durante l'operazione. La correlazione eseguita da Analisi si basa sull'identificatore operation_Id. La query segmenta quindi i risultati usando l'algoritmo di cluster automatico. 

Quando si creano query, verificare che funzionino correttamente in Analisi prima di aggiungerle al flusso.

- Aggiungere la seguente query Analitica e selezionare il tipo di grafico di tabella HTML. Quindi selezionare **nuovo passaggio**.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Finestra di configurazione della query di Analisi](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passaggio 6: Configurare il flusso per l'invio di un messaggio di posta elettronica

1. Cercare **Office 365 Outlook**.
2. Fare clic su **Office 365 Outlook - Send an email** (Office 365 Outlook - Invia un messaggio di posta elettronica).

    ![Finestra di selezione di Office 365 Outlook](./media/automate-with-flow/9outlookaction.png)

1. Nella finestra **Invia un messaggio di posta elettronica** seguire questa procedura:

   a. Digitare l'indirizzo e-mail del destinatario.

   b. Digitare l'oggetto del messaggio di posta elettronica.

   c. Fare clic in un punto qualsiasi della casella **Corpo** e scegliere **Corpo** dal menu di contenuto dinamico che viene visualizzato a destra.

   d. Fare clic su **Mostra opzioni avanzate**.

    ![Configurazione di Office 365 Outlook](./media/automate-with-flow/10sendemailbody.png)

1. Nel menu di contenuto dinamico seguire questa procedura:

    a. Selezionare **Nome allegato**.

    b. Selezionare **Contenuto allegato**.
    
    c. Nella casella **HTML** selezionare **Sì**.

    ![Finestra di configurazione della posta elettronica di Office 365](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Passaggio 7: Salvare e testare il flusso
- Nel **il nome del flusso** casella, aggiungere un nome per il flusso e quindi fare clic su **salvare**.

    ![Nome del flusso e salvarlo](./media/automate-with-flow/12nameflow.png)

È possibile attendere che il trigger esegua l'azione oppure è possibile eseguire il flusso immediatamente [eseguendo il trigger su richiesta](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando il flusso è in esecuzione, i destinatari specificati nell'elenco di posta elettronica ricevono un messaggio simile al seguente:

![Esempio di messaggio di posta elettronica](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](../../azure-monitor/log-query/get-started-queries.md).
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





