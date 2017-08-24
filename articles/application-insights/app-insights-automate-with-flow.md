---
title: Automatizzare i processi di Azure Application Insights con Microsoft Flow
description: Informazioni su come usare Microsoft Flow per automatizzare in poco tempo i processi ripetibili usando il connettore di Application Insights.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: d0cd0cc97fa61d3401f6101292b82132622c9e81
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---

# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizzare i processi di Azure Application Insights con il connettore per Microsoft Flow

Ci si trova spesso a eseguire ripetutamente le stesse query sui dati di telemetria per verificare il corretto funzionamento del servizio? Per automatizzare queste query per trovare tendenze e anomalie e creare quindi flussi di lavoro basati su queste informazioni, il connettore di Azure Application Insights (anteprima) per Microsoft Flow è lo strumento ideale.

Con questa integrazione è ora possibile automatizzare numerosi processi senza dover scrivere codice. Dopo avere creato un flusso usando un'azione di Application Insights, il flusso esegue automaticamente la query di Application Insights - Analisi. 

È possibile aggiungere anche altre azioni. In Microsoft Flow sono disponibili centinaia di azioni. È ad esempio possibile usare Microsoft Flow per inviare automaticamente una notifica di posta elettronica o creare un bug in Visual Studio Team Services. È anche possibile usare uno dei numerosi [modelli](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibili per il connettore per Microsoft Flow. Questi modelli velocizzano il processo di creazione di un flusso. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Creare un flusso per Application Insights

Questa esercitazione illustra come creare un flusso che usa l'algoritmo di cluster automatico di Analisi per raggruppare gli attributi dei dati per un'applicazione Web. Il flusso invia automaticamente i risultati tramite posta elettronica. Questo è solo un esempio di uso congiunto di Microsoft Flow e Application Insights - Analisi. 

### <a name="step-1-create-a-flow"></a>Passaggio 1: Creare un flusso
1. Accedere a [Microsoft Flow](http://flow.microsoft.com) e quindi selezionare **Flussi personali**.
2. Fare clic su **Crea un flusso da un modello vuoto**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passaggio 2: Creare un trigger per il flusso
1. Selezionare **Pianificazione** e quindi selezionare **Pianificazione - Ricorrenza**.
2. Nella casella **Frequenza** selezionare **Giorno** e nella casella **Intervallo** immettere **1**.

    ![Finestra di dialogo del trigger di Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Passaggio 3: Aggiungere un'azione di Application Insights
1. Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.
2. Cercare **Azure Application Insights**.
3. Fare clic su **Application Insights - Visualize Analytics query Preview** (Application Insights - Visualizza query di Analisi Anteprima).

    ![Finestra di esecuzione della query di Analisi](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passaggio 4: Connettersi a una risorsa di Application Insightss

Per completare questo passaggio, sono necessari un ID applicazione e una chiave API per la risorsa. È possibile recuperare queste informazioni dal portale di Azure, come illustrato nel diagramma seguente:

![ID applicazione nel portale di Azure](./media/app-insights-automate-with-flow/appid.png) 

- Specificare un nome per la connessione, con l'ID applicazione e la chiave API.

    ![Finestra di connessione di Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passaggio 5: Specificare la query e il tipo di grafico di Analisi
Questa query di esempio seleziona le richieste non riuscite entro l'ultimo giorno e le correla alle eccezioni che si sono verificate durante l'operazione. La correlazione eseguita da Analisi si basa sull'identificatore operation_Id. La query segmenta quindi i risultati usando l'algoritmo di cluster automatico. 

Quando si creano query, verificare che funzionino correttamente in Analisi prima di aggiungerle al flusso.

- Aggiungere la query di Analisi seguente e quindi selezionare il tipo di grafico di tabella HTML. 

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
    
    ![Finestra di configurazione della query di Analisi](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passaggio 6: Configurare il flusso per l'invio tramite posta elettronica

1. Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.
2. Cercare **Office 365 Outlook**.
3. Fare clic su **Office 365 Outlook - Invia un messaggio di posta elettronica**.

    ![Finestra di selezione di Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. Nella finestra **Invia un messaggio di posta elettronica** seguire questa procedura:

   a. Digitare l'indirizzo di posta elettronica del destinatario.

   b. Digitare l'oggetto del messaggio di posta elettronica.

   c. Fare clic in qualsiasi punto della casella **Corpo** e quindi scegliere **Corpo** dal menu di contenuto dinamico che viene visualizzato a destra.

   d. Fare clic su **Mostra opzioni avanzate**.

    ![Configurazione di Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. Nel menu di contenuto dinamico seguire questa procedura:

    a. Selezionare **Nome allegato**.

    b. Selezionare **Contenuto allegato**.
    
    c. Nella casella **HTML** selezionare **Sì**.

    ![Finestra di configurazione della posta elettronica di Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Passaggio 7: Salvare e testare il flusso
- Nella casella **Nome flusso** aggiungere un nome per il flusso e quindi fare clic su **Crea flusso**.

    ![Finestra di creazione del flusso](./media/app-insights-automate-with-flow/flow8.png)

È possibile attendere che il trigger esegua l'azione oppure è possibile eseguire il flusso immediatamente [eseguendo il trigger su richiesta](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando il flusso è in esecuzione, i destinatari specificati nell'elenco di posta elettronica ricevono un messaggio simile al seguente:

![Esempio di messaggio di posta elettronica](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di [query di Analisi](app-insights-analytics-using.md).
- Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






